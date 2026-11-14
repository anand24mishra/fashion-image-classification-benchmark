# Phase v2/05 — Final Report

| | |
|---|---|
| Status | review |
| Branch | `phase/v2-05-report` |
| Depends on | Phase v2/04 |
| PRD features | F-21 (revised, D-046) |

## Goal
One IEEE report that covers the whole project: the problem, the Phase 1 classical benchmark, the Phase 2 CNNs, the
hybrid, and each author's contributions, with every number and formula matching the saved results.

## Previous phase summary
From v2/04: `rbf_svm_cnn` test 92.44% (vs CNN3 92.09%, p = 0.012; vs `rbf_svm_hog` 91.08%, p = 5.2 × 10⁻⁷); 15
experiments in `test_log.csv` and `summary_all.csv`.

## In scope — exact features
1. **`report/v2/report.tex`** rewritten as the final paper: problem statement, RQ1–RQ9, H1/H2, metrics and formulas,
   shared protocol, Phase 1, Phase 2, hybrid with the feature–classifier decomposition, discussion, threats to validity,
   author-contributions table (name, enrollment number, Phase 1, Phase 2).
   - Acceptance: every number matches `summary_all.csv`, the JSONs or values recomputed from saved predictions;
     static checks pass (braces, environments, references, citations, figures).
2. **Slides and walkthrough removed for now** (`report/v2/ppt.md`, `report/v2/presentation.md`); they will be rebuilt
   from the final report.

## Out of scope
- New experiments, new result files or figures (the accuracy–time plot is drawn in LaTeX from the results table).

## Tasks
- [x] 05.1 — final report, remove the old slide source and walkthrough, README/docs → `docs(report): final report covering both phases and the hybrid`

## Done criteria
- [ ] Report compiles on Overleaf (pending: LaTeX is not installed locally).
- [x] Every number cross-checked against the saved results.
- [x] Docs match what was built; decision logged.

## Phase summary
- **Built:** `report/v2/report.tex` (IEEE conference, 14 references (D-047), 7 PNG figures plus TikZ protocol and architecture
  diagrams and a pgfplots accuracy-vs-time plot); author-contribution macros at the top of the file; README §11 and
  team table updated.
- **Verified:** all numbers recomputed from `summary_all.csv`, the metrics JSONs and the saved test predictions
  (`.scratch` script): Wilson CIs, McNemar χ² and exact p-values, agreement tables, per-class deltas, tops-confusion
  shares, dead-unit count and `gamma="scale"` for the CNN features.
- **Next:** compile on Overleaf; confirm the Phase 2 contribution split; rebuild slides from the report.
