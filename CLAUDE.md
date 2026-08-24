# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **SIC3D User Study** - a self-contained HTML questionnaire for evaluating 3D generation models (Gaussian Splatting) in a user study. Users compare rendered 3D objects from different methods and answer 2 questions per sample: **Overall Object Quality** and **Style Alignment**. The two questions are shown side by side on one page (no tabs, no keyboard shortcuts). Overall Object Quality is judged by weighing 5 underlying aspects (text alignment, 3D plausibility, geometry-texture alignment, texture details, geometry details) — these aspects are explained on the intro page but are NOT separate questions.

## Running the Study

Open `index.html` in any modern web browser - no build step or server required.

```bash
# macOS
open index.html

# Windows
start index.html

# Linux
xdg-open index.html
```

## Workflow

Page sequence: `startPage` → `infoPage` → `consentPage` → `page0` (task instructions) → `sampleGuidePage` → question pages → `finalPage`. Static pages navigate via `showStaticPage(id)`.

1. User opens `index.html` in a browser and lands on a welcome page (`startPage`), then reads the "Participant Information Details" page (`infoPage`, content from `../SIC3D_Information_Sheet.docx`).
2. On the "Participant Consent Details" page (`consentPage`, content from `../SIC3D_consent_form.docx`) the user must tick all 9 consent checkboxes AND enter their email address before the study starts. A highlighted "I agree to all" checkbox (`#consent-check-all`, not counted in the 9) ticks/unticks all of them and stays in sync with the individual boxes. The email is checked against the Firestore `participants` collection — an email that already participated is rejected. Emails in `TEST_EMAILS` bypass the check, are not registered, and their results carry `isTest: true`. The consent time is recorded as `consentTimestamp`.
3. Reads the evaluation criteria (2 questions; Overall Object Quality is explained via 5 underlying aspects)
4. Compares 4-view renderings for 20 items assigned from a fixed 30-item pool plus 4 interleaved attention checks
5. For each sample answers both questions (shown side by side) by selecting: "Method A is better" / "Method B is better" / "Cannot decide"
6. Submits — results upload to Firestore (`study_results` collection in project `sic3d-user-study`). If the tab is hidden or closed before finishing, a partial document (`partial: true`) is auto-saved.

## Code Architecture

- **Two files**: page logic in `index.html`, per-question config in `samples/questions.js`
- **Data structure**: `window.QUESTION_DATA` in `samples/questions.js` is the fixed 30-item pool. `POOL_SIZE` is 30, `NUM_QUESTIONS` is deliberately 20, and `TOTAL_TRIALS` is 24 after adding four attention checks.
- **Assignment**: production registration atomically allocates `p` from `study_meta/assignment_counter`. Trial `t` uses sample `(2p+t) mod 30` and pair `(t+p) mod 10`; all ten pairs occur twice. Trials are then shuffled and A/B placement randomized.
- **Methods**: `SIC3D`, `g-style`, `sgsst`, `styleGS`, and `style_prompt`.
- **Images**: Stored in `samples/s{1..30}/{method}/`, named `rgb_{view}.png` plus one `style.png`.
- **Views**: Fixed at 4 angles [0, 30, 60, 90] degrees
- **Attention checks**: Two prompt-style mismatches, one inconsistent multi-view grid, and one severely degraded grid are placed in separated random windows. Correct sides are randomized but balanced 2A/2B per participant. Both criteria must choose the intact side. More than one failed check makes the record ineligible for analysis.
- **Results**: Submitted via `saveToFirestoreData()`. Assignment audit fields are stored at document and trial level in addition to sample/method metadata and `overallQuality`/`styleAlignment`. Attention checks have no method identities and store their ID, correct answer, and pass/fail result.

## Key JavaScript Functions

| Function | Purpose |
|----------|---------|
| `generateStudyData()` | Assigns 20 balanced main trials from `p`, randomizes them, then inserts four separated attention checks. |
| `createQuestionPages()` | Dynamically builds the question pages from `studyData.questions` (two criterion blocks side by side + one Next button) |
| `markCriteriaAnswered()` | Enables the page's Next button once both questions are answered |
| `goToNext()` | Validates both answers, saves them, navigates to next sample (or submits) |
| `showStaticPage(id)` | Navigates between the static pages (start / info / consent / instructions / guide) |
| `startStudy()` | Validates consent/email, atomically registers production participants and obtains `assignmentIndex`, then shows instructions |
| `saveToFirestoreData()` | Uploads the JSON result document to Firestore (`partial=true` for mid-study auto-saves) |
| `savePartialResults()` | Called on `visibilitychange`/`pagehide`; uploads a partial document if any answer exists |

## Evaluation Criteria (2 Questions)

1. **Overall Object Quality** - Which object is better overall, weighing 5 underlying aspects: text–asset alignment, 3D plausibility, geometry–texture alignment, low-level texture details, low-level geometry details. (These 5 are explained on the intro page but are not separate questions.)
2. **Style Alignment** - Which object's style is closer to the style reference image (colour palette and tones)?

## Image Naming Convention

```
samples/s{question_num}/{method}/rgb_{view}.png
samples/s{question_num}/{method}/style.png
```

- `method`: One of the 5 active method folders
- `view`: Camera angle (0, 30, 60, 90)
- `style.png`: Style reference rendering — the one shown is taken from whichever method lands in the `method1` slot
- `prompt_id` and `seed` are no longer part of the filenames; they live in `samples/questions.js` and are recorded with each result

Test emails bypass the counter. Use `?assignment=0` through `?assignment=29` to inspect any schedule. The four attention checks required by `../ethics.pdf` are implemented. Mandatory training remains future work; do not recruit before implementing it.
