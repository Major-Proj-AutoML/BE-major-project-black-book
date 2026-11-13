# Black Book Revision Log

_Started 2026-11-10. Driven by: the real 270-cell Stage-4b sweep (live `Auto-ML-Postgres`
container), the actual **main-branch** source of all services, the prior audits
(`verification_framework_audit/`, `blackbook_corrections.md`), and the revision brief
(`CLAUDE_CONTENT_REVISION.md`). Guiding rule: distinguish **proposed / implemented /
validated**; never invent numbers; preserve the research direction (reasoning–code alignment)._

## Governing decisions

1. **Framing kept.** Title and research direction unchanged (reasoning–code alignment
   verification). The revision makes the book *honest about what is built vs designed*, per the
   revision brief ("preserve the research direction").
2. **"Main branches only"** (user instruction). The implemented, data-backed verifier is
   **prototype v1** = `automl-reusables/src/execution/verification.py` on `main`
   (symbol-presence + meta-feature value match, boolean `faithful`). The richer graph-based
   RIR/PIR engine is presented as **proposed methodology + future work**; the off-main
   `feat/rir-pir-verifier` prototype is deliberately **not** used as evidence.
3. **Results = the 270-run sweep**, not the stale 6-run `results.xlsx` (2026-07-14 mid-term).

## Changes (file · section · problem → change · evidence)

| File | Section | Problem | Change | Evidence |
|---|---|---|---|---|
| main.tex | preamble | no way to mark diagrams still to be drawn | added `\miroplaceholder` helper (captioned grey box if image absent) | — |
| frontmatter/abstract.tex | whole | claimed full 4-valued multigraph verifier delivered "behind a Flask/Redis backend" | scoped to designed-vs-implemented-vs-validated; Flask→FastAPI; added real 270-run finding | verification.py; sweep |
| chapter1 | Objectives, Contributions | "Flask API"; contributions read as all-delivered | FastAPI; split conceptual/engineering/empirical contributions; added implementation-status note | service `app/main.py` (FastAPI); sweep |
| chapter3 | Software Reqs, Metrics | "Flask"; "networkx" as used; "pre-registered criticality" | FastAPI; mark graph libs as proposed; drop "pre-registered"; add proposed-vs-implemented banner + worked RIR/PIR example | code; audit |
| chapter4 | Architecture, DB Design | "Flask"; 3-table sketch wrong; placeholder diagrams unlabelled | FastAPI + real 7 services; real 4-table schema (datasets, meta_features, run_results, sweep_jobs); embed schema screenshots; ER + use-case Miro placeholders | PROJECT_REPORT_GUIDE §E; schema screenshots |
| chapter5 | whole | describes full graph verifier / CLI / dashboard as implemented | rewritten to main-branch reality (prototype v1, 7 FastAPI services, RQ worker, Ollama, sandbox, 13-cat taxonomy, Postgres+MinIO); full engine marked proposed; embed code screenshots | verification.py; feature_inventory.md |
| chapter6 | whole | "pre-registered"; empty placeholder tables; tautological 100% baseline | rewritten as evidence-based results: real per-condition / per-dataset tables, paired Wilcoxon, 7 reasoning_unfaithful flags; verifier baseline comparison kept as explicit future work; removed pre-registration + 100% tautology | blackbook_analytics.md; sweep |
| chapter7 | Conclusion | claims objectives met + "ready to run pre-registered corpora" | states what is designed / implemented / validated (270 runs) and what is pending (full-verifier population eval) | sweep; audit |

## Status classification (report-back)

- **Verified (evidence):** 7-service FastAPI pipeline; B0/B1/B2 conditions; prototype-v1
  faithfulness verifier; 270-run sweep; 7 `reasoning_unfaithful` detections.
- **Corrected:** Flask→FastAPI; RQ disambiguation; removed pre-registration; removed 100%
  tautology; verifier scoped to prototype v1.
- **Pending:** full graph-based verifier; independent pre-code reasoning (B2R); seeded-mutation
  corpus + human annotation; verifier precision/recall/F1 at population scale.
- **Blocked:** dep-heavy re-runs need the dead root `.venv` rebuilt (CPython 3.13.x).

## Citations & figures

- **FlowPipe 2027 citation (verified, not an error):** arXiv:2606.24679, **accepted at
  SIGMOD 2027**, ACM DOI 10.1145/3837112. Added `note = {To appear; accepted at SIGMOD 2027}`
  + DOI to `references.bib` so a reviewer does not mistake the future year for a typo.
- **15 code/schema screenshots** copied from `Pictures/Major-Project-SS` into
  `figures/project_figures/screenshots/` and embedded (4 schema figures in Ch4, 6 code figures
  in Ch5).
- **Miro placeholders** (captioned grey boxes via `\miroplaceholder`, rendered until the PNG is
  dropped at the named path): `rir_pir_mapping` (Ch3), `er_diagram` + `usecase` (Ch4),
  `fig_success_by_condition` + `fig_error_taxonomy` + `fig_score_comparison` (Ch6). The existing
  `architecture.png` is kept but its caption now specifies the real 7-service layout to redraw.

## Compilation

- No LaTeX toolchain is installed on this machine (no `pdflatex`/`latexmk`/`xelatex`), so the
  document was **not compiled** here. Refs, labels and table column counts were checked by
  inspection. Compile on Overleaf or a local TeX install before submission.
