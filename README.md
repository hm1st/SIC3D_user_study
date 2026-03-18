# User Study HTML Questionnaire

## Folder Structure

```
html/
├── index.html              # Main questionnaire (open in browser)
├── images/
│   ├── style/             # Style reference images ✓
│   │   ├── wave.png
│   │   ├── fire.png
│   │   └── van_gogh.png
│   └── renderings/        # 3D model renderings ✓
│       ├── q1_left.png    # Q1 - Method A (SIC3D)
│       ├── q1_right.png   # Q1 - Method B (stylised_prompt)
│       ... (20 images total)
└── README.md
```

## How to Use

1. **Open index.html** in a web browser

2. **Fill out the questionnaire**
   - Read the instructions carefully
   - Examine the images for each question
   - Select: Left is better / Right is better / Cannot decide for each criterion

3. **Click Submit** when finished

4. Results will be downloaded as `user_study_results.txt`

## Evaluation Criteria (7 Total)

Based on GPT evaluation prompts from `tournament_style/gpt_prompts/`:

### n4_full.txt (Criteria 1-6)
1. **Text-Asset Alignment** - How well model corresponds to text prompt
2. **3D Plausibility** - Natural, solid, no abnormalities
3. **Geometry-Texture Alignment** - Texture adheres to geometry
4. **Low-Level Texture Details** - Fine details, not blurry
5. **Low-Level Geometry Details** - Accurate shape, no noise
6. **Overall Quality** - Better overall

### n4_style.txt (Criterion 7)
7. **Style Alignment** - Similarity to style reference (color palette, tones)

## Response Options
- **Left is better** - Method A is better
- **Right is better** - Method B is better
- **Cannot decide** - Both equal (use sparingly)

## Question Mapping

| Q# | Text Prompt | Style | Method A (Left) | Method B (Right) |
|----|-------------|-------|-----------------|-----------------|
| Q1 | A fox-wave | wave | SIC3D | stylised_prompt |
| Q2 | A fox-fire | fire | SIC3D | stylised_prompt |
| Q3 | A fox-van gogh | van_gogh | SIC3D | stylised_prompt |
| Q4 | A panda-fire | fire | SIC3D | stylised_prompt |
| Q5 | A hamburger-fire | fire | SIC3D | stylised_prompt |
| Q6 | A robot-fire | fire | SIC3D | stylised_prompt |
| Q7 | A lego castle-fire | fire | SIC3D | stylised_prompt |
| Q8 | A toy car-fire | fire | SIC3D | stylised_prompt |
| Q9 | A mug-fire | fire | SIC3D | stylised_prompt |
| Q10 | A cat-fire | fire | SIC3D | stylised_prompt |

## Image Layout

Each question shows:
- **Left side**: 4 renderings of Method A from different viewpoints
- **Right side**: 4 renderings of Method B from different viewpoints
- **Bottom**: Style reference image

Users should mentally construct 3D models from the 4-view renderings and compare them.
