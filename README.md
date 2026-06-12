# User Study HTML Questionnaire

A self-contained browser questionnaire for pairwise comparison of stylised text-to-3D methods.

## How to Use

1. Open `index.html` in any modern browser — no server needed. A welcome page leads to the Participant Information Sheet and then the consent form (content sourced from `../SIC3D_Information_Sheet.docx` and `../SIC3D_consent_form.docx`).
2. On the consent page, tick all 9 consent boxes and enter your email address. Each email can participate once (checked against the Firestore `participants` collection; test emails listed in `TEST_EMAILS` in `index.html` bypass the check and are tagged `isTest`). The consent time is stored as `consentTimestamp` with the results.
3. For each of the 20 questions, examine the 4-view renderings of Method A vs Method B and the style reference.
4. Answer the 2 questions shown side by side — **Overall Object Quality** and **Style Alignment** — each by selecting **Method A is better / Method B is better / Cannot decide**.
5. Click Submit. Results upload to Firestore. Leaving mid-study auto-saves a partial document (`partial: true`).

The question order is randomized on every session, and Method A / Method B are randomly drawn from the 5 methods per question. Results are tagged with `prompt`, `style`, `promptId`, `seed`, `methodA`, `methodB`, and `presentationOrder` so the actual prompt–style pair (not the sequential slot) is what's recorded.

## Folder Structure

```
html/
├── index.html        # Page logic + UI (do not edit question data here)
├── sample_page.png   # Annotated screenshot shown on the Sample Page Guide
├── samples/
│   ├── questions.js  # Per-question config — EDIT THIS to change the questionnaire
│   └── s{1..N}/
│       └── {method}/
│           ├── rgb_{0,30,60,90}.png
│           └── style.png
└── README.md
```

## Modifying the Questionnaire

Edit `samples/questions.js` — it defines `window.QUESTION_DATA`, an array of `{ q, prompt_id, seed, prompt, style }` entries. `prompt` is the text shown to the participant (e.g. "A book"); `style` is the style label (e.g. "flower") which is **not** displayed (the style reference image already conveys it) but is recorded with the result. After saving, refresh the browser. `NUM_QUESTIONS` and the page layout are derived from the array length, so you do not need to edit `index.html`. Caveat: each new entry requires its image files (5 per method) to already exist under `samples/s{q}/{method}/` — see Source Data below.

`{method}` folders present: `SIC3D`, `SIC3D_trellis`, `g-style`, `sgsst`, `styleGS`, `style_prompt`. The draw pool (`ALL_METHODS` in `index.html`) contains 5 of them — `SIC3D_trellis` is excluded. Per session, the page randomly picks 2 of the 5 methods as Left/Right (`generateStudyData()` in `index.html`).

## Source Data

All renderings are copied from `../../tournament_v2/methods/{method}/{prompt_id}/{seed}/`. The 4 views correspond to `rgb_0.png`, `rgb_30.png`, `rgb_60.png`, `rgb_90.png` from the 120 frames available there. `prompt_id` is the 0-based index into `../../tournament_v2/prompts.json`. Note the files are renamed on copy: `prompt_id`/`seed` are dropped from the filenames (they live in `questions.js`).

## Question Mapping

The `q` field below is the canonical question ID stored in Firestore as `questionId`. The order users actually see is randomized per session (`presentationOrder` records the seen order).

| q   | prompt_id | seed  | Prompt        | Style |
|-----|-----------|-------|---------------|-------|
| 1   | 109       | 30857 | A book        | flower |
| 2   | 19        | 25310 | A boat        | starry night |
| 3   | 4         | 9264  | A torn hat    | flower |
| 4   | 46        | 14879 | A cat         | flower |
| 5   | 41        | 22436 | A rabbit      | van gogh |
| 6   | 38        | 29504 | A rabbit      | mosaic |
| 7   | 23        | 4931  | A handbag     | fire |
| 8   | 125       | 28458 | A fox         | van gogh |
| 9   | 17        | 9345  | A boat        | mosaic |
| 10  | 115       | 17497 | A hamburger   | mosaic |
| 11  | 93        | 5725  | A mug         | fire |
| 12  | 14        | 14867 | A boat        | wave |
| 13  | 100       | 31670 | A lego castle | fire |
| 14  | 72        | 2175  | A robot horse | fire |
| 15  | 5         | 15121 | A torn hat    | starry night |
| 16  | 5         | 9264  | A torn hat    | starry night |
| 17  | 15        | 14867 | A boat        | northern light |
| 18  | 37        | 29504 | A rabbit      | fire |
| 19  | 39        | 22436 | A rabbit      | flower |
| 20  | 86        | 24686 | A wooden bear | fire |

Style coverage: fire ×6, flower ×4, mosaic ×3, starry night ×3, van gogh ×2, wave ×1, northern light ×1. (q15 and q16 share `prompt_id` 5 with different seeds.)

## Evaluation Criteria (2 questions)

The participant answers 2 questions per sample (shown side by side):

1. **Overall Object Quality** — which object is better overall. Judged by weighing 5 underlying aspects (explained on the intro page, not asked separately):
   - Text-Asset Alignment — does the model match the text prompt?
   - 3D Plausibility — natural, solid, no abnormalities
   - Geometry-Texture Alignment — texture adheres to geometry
   - Low-Level Texture Details — fine details, not blurry
   - Low-Level Geometry Details — accurate shape, no noise
2. **Style Alignment** — similar palette / tone to the style reference

Criteria wording is sourced from `../../tournament_v2/gpt_prompts/` (`n4_full.txt`, `n4_style.txt`).

Stored in Firestore as `criteria.overallQuality` (c1) and `criteria.styleAlignment` (c2), valued `A` / `B` / `N`.
