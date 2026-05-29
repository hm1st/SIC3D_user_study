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

1. User opens `index.html` in a browser
2. Reads the evaluation criteria (2 questions; Overall Object Quality is explained via 5 underlying aspects)
3. Compares 4-view renderings of Method A vs Method B for 20 questions (order randomized per session)
4. For each sample answers both questions (shown side by side) by selecting: "Method A is better" / "Method B is better" / "Cannot decide"
5. Submits — results upload to Firestore (`study_results` collection in project `sic3d-user-study`)

## Code Architecture

- **Two files**: page logic in `index.html`, per-question config in `questions.js`
- **Data structure**: `window.QUESTION_DATA` array in `questions.js` is the source of truth. `index.html` reads it via `<script src="questions.js">` and derives `NUM_QUESTIONS = QUESTION_DATA.length`. Do not redefine `QUESTION_DATA` in `index.html`. Adding/removing questions only requires editing `questions.js` (and copying the matching image files).
- **Methods**: 6 methods available - `SIC3D`, `SIC3D_trellis`, `g-style`, `sgsst`, `styleGS`, `style_prompt`
- **Images**: Stored in `images/q{1..20}/{method}/` folders, named `{prompt_id}_{seed}_rgb_{view}.png`
- **Views**: Fixed at 4 angles [0, 30, 60, 90] degrees
- **Results**: Submitted to Firestore via `saveToFirestoreData()`. Each result entry has `presentationOrder`, `questionId`, `prompt`, `promptId`, `seed`, `methodA`, `methodB`, plus a `criteria` object with 2 fields: `overallQuality` (c1) and `styleAlignment` (c2). Older records may still contain the legacy 7-field criteria.

## Key JavaScript Functions

| Function | Purpose |
|----------|---------|
| `generateStudyData()` | Per-question: pick 2 random methods. Then shuffle the 20 questions for this session. |
| `createQuestionPages()` | Dynamically builds the question pages from `studyData.questions` (two criterion blocks side by side + one Next button) |
| `markCriteriaAnswered()` | Enables the page's Next button once both questions are answered |
| `goToNext()` | Validates both answers, saves them, navigates to next sample (or submits) |
| `saveToFirestoreData()` | Uploads the JSON result document to Firestore |

## Evaluation Criteria (2 Questions)

1. **Overall Object Quality** - Which object is better overall, weighing 5 underlying aspects: text–asset alignment, 3D plausibility, geometry–texture alignment, low-level texture details, low-level geometry details. (These 5 are explained on the intro page but are not separate questions.)
2. **Style Alignment** - Which object's style is closer to the style reference image (colour palette and tones)?

## Image Naming Convention

```
images/q{question_num}/{method}/{prompt_id}_{seed}_rgb_{view}.png
images/q{question_num}/{method}/{prompt_id}_{seed}_style.png
```

- `method`: One of 6 methods
- `prompt_id`, `seed`: Model configuration IDs
- `view`: Camera angle (0, 30, 60, 90)
- `_style.png`: Style reference rendering