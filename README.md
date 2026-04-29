# User Study HTML Questionnaire

A self-contained browser questionnaire for pairwise comparison of 6 stylised text-to-3D methods.

## How to Use

1. Open `index.html` in any modern browser — no server needed.
2. For each of the 20 questions, examine the 4-view renderings of Method A vs Method B and the style reference.
3. For each of the 7 criteria, select **Method A is better / Method B is better / Cannot decide**.
4. Click Submit. Results upload to Firestore.

The question order is randomized on every session, and Method A / Method B are randomly drawn from the 6 methods per question. Results are tagged with `prompt`, `promptId`, `seed`, `methodA`, `methodB`, and `presentationOrder` so the actual prompt–style pair (not the sequential slot) is what's recorded.

## Folder Structure

```
html/
├── index.html        # Page logic + UI (do not edit question data here)
├── questions.js      # Per-question config — EDIT THIS to change the questionnaire
├── images/
│   └── q{1..N}/
│       └── {method}/
│           ├── {prompt_id}_{seed}_rgb_{0,30,60,90}.png
│           └── {prompt_id}_{seed}_style.png
└── README.md
```

## Modifying the Questionnaire

Edit `questions.js` — it defines `window.QUESTION_DATA`, an array of `{ q, prompt_id, seed, prompt, style }` entries. `prompt` is the text shown to the participant (e.g. "A fox"); `style` is the style label (e.g. "wave") which is **not** displayed (the style reference image already conveys it) but is recorded with the result. After saving, refresh the browser. `NUM_QUESTIONS` and the page layout are derived from the array length, so you do not need to edit `index.html`. Caveat: each new entry requires its 30 image files (6 methods × 5 files) to already exist under `images/q{q}/{method}/` — see Source Data below.

`{method}` is one of `SIC3D`, `SIC3D_trellis`, `g-style`, `sgsst`, `styleGS`, `style_prompt`. All 6 methods have a full set of files in every question folder. Per session, the page randomly picks 2 of the 6 methods as Left/Right (`generateStudyData()` in `index.html`).

## Source Data

All renderings are copied from `../../tournament_v2/methods/{method}/{prompt_id}/{seed}/`. The 4 views correspond to `rgb_0.png`, `rgb_30.png`, `rgb_60.png`, `rgb_90.png` from the 120 frames available there. `prompt_id` is the 0-based index into `../../tournament_v2/prompts.json`.

## Question Mapping

The `q` field below is the canonical question ID stored in Firestore as `questionId`. The order users actually see is randomized per session (`presentationOrder` records the seen order).

| q   | prompt_id | seed  | Prompt        | Style |
|-----|-----------|-------|---------------|-------|
| 1   | 30        | 13905 | A fox         | fire |
| 2   | 31        | 13905 | A fox         | mosaic |
| 3   | 32        | 13905 | A fox         | flower |
| 4   | 60        | 18958 | A mug         | flower |
| 5   | 37        | 22436 | A hamburger   | fire |
| 6   | 79        | 11533 | A robot       | fire |
| 7   | 51        | 7044  | A lego castle | fire |
| 8   | 100       | 14206 | A toy car     | fire |
| 9   | 16        | 14867 | A cat         | fire |
| 10  | 65        | 22680 | A panda       | fire |
| 11  | 4         | 9264  | A boat        | flower |
| 12  | 17        | 25310 | A cat         | mosaic |
| 13  | 19        | 9345  | A cat         | starry night |
| 14  | 24        | 4931  | A clownfish   | mosaic |
| 15  | 39        | 29504 | A hamburger   | flower |
| 16  | 43        | 14633 | A handbag     | northern light |
| 17  | 48        | 14633 | A handbag     | van gogh |
| 18  | 96        | 16727 | A torn hat    | starry night |
| 19  | 112       | 14216 | An elephant   | wave |
| 20  | 118       | 8074  | An elephant   | van gogh |

Style coverage: fire ×7, flower ×4, mosaic ×3, van gogh ×2, wave ×1, starry night ×2, northern light ×1.

## Evaluation Criteria (7)

Sourced from `../../tournament_v2/gpt_prompts/`:

`n4_full.txt` — criteria 1–6, no style reference:
1. **Text-Asset Alignment** — does the model match the text prompt?
2. **3D Plausibility** — natural, solid, no abnormalities
3. **Geometry-Texture Alignment** — texture adheres to geometry
4. **Low-Level Texture Details** — fine details, not blurry
5. **Low-Level Geometry Details** — accurate shape, no noise
6. **Overall Quality** — better overall

`n4_style.txt` — criterion 7, with style reference:

7. **Style Alignment** — similar palette / tone to the style reference
