# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **SIC3D User Study** - a self-contained HTML questionnaire for evaluating 3D generation models (Gaussian Splatting) in a user study. Users compare rendered 3D objects from different methods and rate them across 7 criteria.

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
2. Reads evaluation criteria (7 dimensions)
3. Compares 4-view renderings of Method A vs Method B for 10 questions
4. Answers each question by selecting: "Left is better" / "Right is better" / "Cannot decide"
5. Downloads results as `user_study_results.txt`

## Code Architecture

- **Single file**: All logic (HTML, CSS, JavaScript) is in `index.html`
- **Data structure**: Questions defined in `QUESTION_DATA` array (line ~1029)
- **Methods**: 6 methods available - `SIC3D`, `SIC3D_trellis`, `g-style`, `sgsst`, `styleGS`, `style_prompt`
- **Images**: Stored in `images/q{1-10}/` folders, named `{method}_{prompt_id}_{seed}_rgb_{view}.png`
- **Views**: Fixed at 4 angles [0, 30, 60, 90] degrees
- **Results**: Downloaded as plain text file

## Key JavaScript Functions

| Function | Purpose |
|----------|---------|
| `generateStudyData()` | Shuffles method assignments for each question |
| `createQuestionPages()` | Dynamically builds the 10 question pages |
| `showCriteriaTab()` | Switches between 7 criteria tabs |
| `goToNext()` | Navigates to next question/criteria |
| `downloadResults()` | Exports answers to text file |

## Evaluation Criteria (7 Total)

1. **Text-Asset Alignment** - Does model reflect text prompt attributes?
2. **3D Plausibility** - Natural, solid, no abnormalities?
3. **Geometry-Texture Alignment** - Texture adheres to geometry?
4. **Low-Level Texture Details** - Fine details, not blurry?
5. **Low-Level Geometry Details** - Accurate shape, no noise?
6. **Overall Quality** - Better overall?
7. **Style Alignment** - Matches style reference image?

## Image Naming Convention

```
images/q{question_num}/{method}_{prompt_id}_{seed_rgb_{view}.png
images/q{question_num}/{method}_{prompt_id}_{seed}_style.png
```

- `method`: One of 6 methods
- `prompt_id`, `seed`: Model configuration IDs
- `view`: Camera angle (0, 30, 60, 90)
- `_style.png`: Style reference rendering