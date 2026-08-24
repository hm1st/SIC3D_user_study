# User Study HTML Questionnaire

This directory contains the live SIC3D pairwise-comparison study. It ranks five
stylised text-to-3D methods on Overall Object Quality and Style Alignment.

## Running the Study

Open `index.html` in a modern browser. A participant reads the information
sheet, accepts all consent statements, enters an email address, reads the task
guide, evaluates 20 assigned samples plus 4 interleaved attention checks, and
submits to Firestore.

For local testing, use the test email in `TEST_EMAILS`. Add
`?assignment=0` through `?assignment=29` to the URL to inspect a particular
assignment without incrementing the production counter.

## Pool and Assignment Design

`samples/questions.js` defines the fixed pool of 30 prompt-style samples.
Each production participant receives an atomic, zero-based
`assignmentIndex` (`p`) from `study_meta/assignment_counter`. For
assignment trial `t = 0..19`:

```text
poolIndex = (2p + t) mod 30
methodPairIndex = (t + p) mod 10
```

The ten unordered pairs of the five methods are generated in the order implied
by `ALL_METHODS`. Consequently, every participant evaluates each method pair
twice. Across 30 consecutive assignment indices, every sample × method-pair
cell is observed exactly twice. Pair assignment happens before trial order is
shuffled; A/B placement is randomized independently per trial.

## Attention Checks

Four checks are added after the 20 main trials are assigned. Two target Overall
Object Quality using failed or severely degraded geometry while holding the
style condition fixed. Two target Style Alignment using the same object and
seed rendered with a deliberately incorrect style.
One check is placed randomly within each of four separated presentation
windows. The intact side is randomized with exactly two correct-A and two
correct-B checks per participant, with one A and one B correct answer for each
criterion. Only the designated target criterion determines whether a check
passes; the other response is retained but ignored for attention scoring. A
participant who
fails more than one check is excluded from analysis, following the ethics
application. Checks carry an attention-check flag, have no method identities,
and never enter method rankings.

The analysis script also excludes legacy records without an attention-check
summary by default because their eligibility cannot be established.

## File Structure

```text
html/
├── index.html
├── sample_page.png
└── samples/
    ├── questions.js
    └── s{1..30}/{method}/
        ├── rgb_{0,30,60,90}.png
        └── style.png
```

Active methods are `SIC3D`, `g-style`, `sgsst`, `styleGS`, and
`style_prompt`. Each sample therefore contains 25 images.

## Selection Provenance

The pool was selected reproducibly from the actual 18 × 7 candidate grid using
`../select_sample_pool.py`. Four-view CLIP text and style-reference scores
were averaged over three seeds and five methods, standardized separately, and
combined with equal weight. Five score quantiles contribute six samples each;
all prompts occur once or twice and all styles occur four or five times. The
fixed selection/seed RNG is `20260821`; detailed scores and the verified KS
statistic are in `../selection_artifacts/`.

The object labels in `../../tournament_v2/prompts.json` do not match the
render directory IDs. The selection script recovers the real object label from
the three-seed sets in `../../stage_1/`; do not reconstruct labels from the
stale JSON object text.

## Stored Results

Each result stores `assignmentIndex`, the design constants, and per-trial
`poolIndex`, `assignmentTrial`, `methodPairIndex`, presentation order,
sample metadata, actual A/B methods, and the two responses. Attention-check
records store their check ID, type, target criterion, correct side, and
pass/fail status; the
document stores an `attentionCheckSummary`. Partial documents are written
when a participant leaves after answering at least one item.

Mandatory training is not yet implemented and must be completed before
recruitment, as required by `../ethics.pdf`.
