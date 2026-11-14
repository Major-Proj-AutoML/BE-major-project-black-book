# Black Book Revision — `main`-only honesty pass (2026-11-14)

Revises the black book to describe **only** what exists on the `main` branch and what the
270-cell sweep actually shows. No application source code was modified. No non-`main`
branch was inspected, merged, or used as evidence.

- **BlackBook repo:** `main` @ `f61496bd` (edit target).
- **Implementation baseline:** `automl-reusables` `main` @ `73e8e4b9`; the `feat/rir-pir-verifier`
  branch was **not** checked out or read.
- **Authoritative data:** `experiment_sweeps (1).xlsx` `run_results`, filtered to the clean
  sweep (seeds **3001–3005**, 270 rows). The xlsx `summary_*` tabs are computed over the full
  326-row table **including July pilots** and were deliberately **not** used.
- **Not compiled:** no LaTeX toolchain on this machine. Compile on Overleaf (Phase 9).

---

## 1. Chapter-by-chapter change log

### Abstract
- Dropped the feature-branch mention ("a rule-based verifier prototype on a feature branch").
- `LLM runner (Ollama or OpenRouter)` → `(Ollama in the reported runs)`.
- `PostgreSQL / MinIO store` → PostgreSQL + **local-filesystem** artefacts.
- Added the **post-hoc trace** caveat (the check tests code–trace consistency, not independent intent).

### Chapter 1 — Introduction
- **Objectives** rewritten to the real project: define alignment · build the pipeline · implement
  the first *limited* check (action presence + value grounding) · run the 270-cell sweep · state
  limits + propose the fuller verifier as future work.
- **Scope** split into the *implemented* check vs a *fuller* verifier's targets (columns,
  parameters, ordering, leakage, metric) — "a target, not a delivered capability".
- **Contributions** regrouped Conceptual / Engineering / Empirical; RIR/PIR + metric suite +
  baseline protocol collapsed to one "specified, not implemented, not evaluated" item.
- **Status paragraph:** removed the `feat/rir-pir-verifier` "implemented prototype" sentences.
- **Organization:** Ch3 = *proposed*; Ch6 future-tense → results; "resulting publication" →
  "supporting material". Added `\label{ch:intro}`.

### Chapter 2 — Literature Review
- Comparison-table self-row: `AST + pipeline graph` → `AST (pipeline graph proposed)`.
- Hypotheses sentence: H1 **supported**; H2 only *illustrated* (baseline comparison = future work).
- Verified all 28 `\cite` keys resolve in `references.bib`.

### Chapter 3 — Proposed Methodology
- Scope banner: removed the feature-branch sentence; two honest levels (implemented main boolean
  vs proposed everything-else).
- **Added** the explicit post-hoc paragraph ("What the reasoning trace actually is").
- Evaluation Metrics: banner that the three metrics are **proposed, not computed**; plain-language
  gloss for each; summary metric suite relabelled proposed.
- Alignment relation: noted the main check uses only presence + value grounding.
- Worked example: `implemented prototype` → `implemented main-branch verifier`.
- Baselines framed as the proposed protocol, "not carried out in this project".
- Software reqs: dropped OpenRouter as a main dependency.

### Chapter 4 — System Design
- Verifier module: removed "realised in the prototype verifier" → "not implemented in this project".
- Data-flow rewritten to `main`: one self-contained script; B2 trace printed at runtime, read
  from stdout; code parsed only into a name-set; verifier report fields stated exactly
  (`n_decisions`, `n_faithful`, boolean `faithful`, per-decision `step/action/action_present/value_matches/reasons`);
  category/severity/source-span marked proposed.
- Use-case "Audit a run": dropped source-span → verdict + recorded reasons.
- LLM runner (×2): OpenRouter → Ollama (reported runs).
- `MinIO` artefact claims → **local filesystem** (`logs/runs/`); MinIO noted integrated-but-disabled.
- Verified: 13-member `ErrorCategory`; four-table schema matches the live tables.

### Chapter 5 — Implementation
- Status table: the AST-PIR / existence-parameter-ordering-column / 3-&4-valued / CLI row
  → **Proposed** (was "Implemented … feat/rir-pir-verifier").
- **Deleted** the "RIR/PIR prototype (feature branch)" paragraph.
- "Proposed Components" section: no longer credits the branch as "already implements"; lists the
  fuller verifier parts as future work.
- "Algorithm: Implemented vs Proposed" prose: removed "realised by the prototype".
- `MinIO` → filesystem (`logs/runs/`), MinIO integrated-but-disabled; persistence summary fixed.
- Service table: frontend ports filled in (`8501 / 3000`), verified against `automl-infra`.
- Verified: left-hand code listing matches `verification.py`; ports 8001/8002/8003/8004/8000 correct.

### Chapter 6 — Results and Discussion
- **Every table recomputed from the clean sweep and confirmed** (see §4 of this file).
- Wilcoxon caption: `n` = discordant (non-zero-difference) pairs; median Δ over those pairs
  (the rest tied). **Resolves the open B1-vs-B2 median = +0.0043** (correct; it is the non-zero
  median — the earlier "≈0.000" was the all-pairs median).
- Added an **effect-size `r`** column (0.21 / 0.09 / 0.05, all small).
- Added a paragraph clarifying the **seven unfaithful runs are counted among the 34 unsuccessful
  B2 cells** (success requires passing the faithfulness check), separate from the other 27 failures.
- Per-backend caption: nemotron-nano B2 token means are over the 14 cells that recorded usage.
- Softened the "adds failure surface" line to an explicit interpretation, not an isolated cause.

### Chapter 7 — Conclusion
- `\ref{ch:lit}` → `\ref{ch:intro}` (objectives are in Chapter 1, not Chapter 2).
- Removed the feature-branch "RIR/PIR prototype verifier on a feature branch" sentence.
- LLM runner OpenRouter → Ollama; `MinIO` → filesystem.
- **Added** the post-hoc reasoning-trace limitation to the Limitations list (with the
  StandardScaler/RobustScaler "wrong-in-the-same-way" example).

### Front matter & appendix
- `project_details.tex`: `\studenttwoprn` `06.` → `06`.
- `acknowledgement.tex`: `Prof.\ Mr.\ Amit Singh` → `Mr.\ Amit Singh` (matches the certificate).
- `appendix.tex`: open-source "seven repositories" → "nine" (nine are listed and exist).
- `references.bib` (`flowpipe`, ~ref [26]): note no longer asserts acceptance — now "listed as to
  appear at SIGMOD 2027 — verify before final submission".

---

## 2. Claims removed or downgraded (unsupported by `main`)

| Claim | Action |
|---|---|
| `feat/rir-pir-verifier` AST/PIR engine (existence/parameter/column/ordering, 3-/4-valued verdicts, criticality gating, CLI) presented as implemented | **Removed** everywhere; now proposed/future |
| Directed-attributed-multigraph PIR, set-valued witness mapper, four-valued verdicts | Downgraded to **proposed** |
| Claim Coverage / Weighted Semantic Alignment / Critical Mismatch Rate as computed metrics | Downgraded to **proposed, not computed** |
| Baseline comparison + seeded-mutation corpus + human annotation as done | **Future work** |
| "MinIO holds large artefacts" (as-run) | Corrected to **local filesystem**; MinIO integrated-but-disabled |
| OpenRouter as a `main` runner backend | Narrowed to "Ollama in the reported runs" |
| FlowPipe "accepted at SIGMOD 2027" | Softened to "listed as to appear — verify" |

---

## 3. Figure briefs (user to draw in Miro / build as charts — do NOT auto-generate)

### Figure 3.1 — RIR–PIR conceptual mapping — **label: PROPOSED (not on `main`)**
Left→right flow, four columns, with a clear "PROPOSED FRAMEWORK" banner:
1. **NL reasoning** (quote): *"Fare is heavily skewed with many outliers (outlier ratio 0.128 > 0.05), so I scale it with RobustScaler rather than StandardScaler."*
2. **RIR atomic claim:** `TRANSFORM(op=scale, scope={Fare}, family=robust_scaling, meta_feature=distributional.outlier_ratio_per_numeric.Fare, observed_value=0.128, threshold=0.05, action=RobustScaler)`.
3. **Generated code:** `RobustScaler()` inside `ColumnTransformer([... ["Fare"]])`.
4. **PIR:** AST call node `RobustScaler` + import edge + column-literal set `{Fare}`.
- **Arrows:** claim → witness(es); per-check verdict badges: **presence ✓** and **value ✓** solid
  (these two are implemented on `main`); **column** and **fit-after-split order** dashed/greyed and
  labelled *proposed*.
- Value 0.128 is real (Titanic `Fare` outlier ratio = 0.1278 in the meta-features).

### Figure 4.1 — System architecture — **`main` only**
Three tiers sharing Postgres + Redis. Verified components and ports:
- **Ingestion/Generation:** `gateway` **:8000** → `data-service` **:8001**, `metafeatures-service` **:8002**, `generation-service` **:8003**.
- **Run/Verification:** `generation-worker` (no host port) runs `jobs.run_cell`: build prompt (B0/B1/B2) → call **Ollama** runner → **sandboxed subprocess** executor → (B2) **faithfulness check** (presence + value grounding) → persist.
- **Analysis:** `analysis-service` **:8004** (read-only RQ1–RQ5).
- **Front-ends:** Streamlit `automl-frontend` **:8501**, Next.js `automl-openui` **:3000**.
- **Stores:** **PostgreSQL 16** (`:5433`→5432) = system of record; **Redis 7** (`:6380`→6379) = queue; **artefacts on local filesystem** `logs/runs/`. *MinIO is integrated in code but DISABLED — draw it greyed/optional, not as an active store.*
- **Line styles:** solid = synchronous HTTP (client↔gateway↔services); dashed = asynchronous RQ jobs (generation → Redis → worker → Postgres/filesystem).
- Do **not** draw any RIR/PIR graph component as a deployed service.

### Figure 4.2 — Use-case — **`main` only**
Actor **Researcher**; three use cases: **Submit workflow** (upload dataset + task, start a sweep);
**Audit a run** (inspect code, execution trace, and for B2 the faithfulness verdict + recorded
reasons — *no source-span*); **Reproduce an analysis** (regenerate RQ1–RQ5 tables from stored rows
without re-calling the LLM).

### Figure 4.3 — ER diagram — **`main` schema**
- **datasets** (PK `id`): `name, source, openml_id, target_col, task_type, train_path, test_path, n_rows, n_cols, created_at`.
- **meta_features** (PK `id`; FK `dataset_id` **UNIQUE** → one-to-one): `features` (JSON), `computed_at`.
- **run_results** (PK `id`; FK `dataset_id` → many): `condition, llm_backend, seed, iteration, success, test_score, error_category, iterations_used, max_iterations, runtime_seconds, prompt_tokens, completion_tokens, generated_code_path, created_at, reasoning_trace` (JSON, nullable, B2), `verification_report` (JSON, nullable, B2).
- **sweep_jobs** (PK `id`, independent — no FK): `rq_job_id, status, params` (JSON), `total_cells, completed_cells, failed_cells, error_message, created_at, updated_at, completed_at`.
- Cardinalities: `datasets 1—1 meta_features`, `datasets 1—N run_results`, `sweep_jobs` standalone.

### Figure 6.1 — Success rate (bar) — exact values
Grouped bars by backend × condition (y-axis 0–100%):
gemma4 B0 100 / B1 100 / B2 **80.0**; gpt-oss B0 100 / B1 100 / B2 **80.0**; nemotron-nano B0 100 / B1 100 / B2 **26.7**. Overall: B0 100 (90/90), B1 100 (90/90), B2 **62.2** (56/90).

### Figure 6.2 — Failure distribution (the **34** unsuccessful B2 cells) — exact counts
Stacked bars by backend: gemma4 `reasoning_unfaithful` 5, `syntax_error` 1; gpt-oss `runtime_other` 4, `api_hallucination` 1, `reasoning_unfaithful` 1; nemotron `infrastructure` 19, `import_error` 2, `reasoning_unfaithful` 1. (Sum = 34.) Highlight the **7** `reasoning_unfaithful` — but label them as the subset that *ran and scored* yet failed verification, distinct from the other 27.

### Figure 6.3 — Predictive-score comparison — data exists
Box/strip of per-cell `balanced_accuracy` by condition (optionally faceted by dataset), with
denominators (B0 90, B1 90, B2 56 successful runs) and the B2 survivorship-bias caveat. Per-dataset
means are in Table 6.4. Source data is sufficient — no fabrication needed.

---

## 4. Experiment verification (recomputed from the clean sweep, seeds 3001–3005)

All confirmed against the book:
- Cells: 270 (6×3×3×5). B0 90/90, B1 90/90, B2 **56/90 (62.2%)**.
- Mean score (successful): B0 0.8432, B1 0.8468, B2 0.8549. Iters (succ): 1.01/1.06/1.41.
- Per-dataset ok/score — all match (clean 15/15 for B0/B1, **not** the pilot-inflated 20/21).
- Error categories — gemma 5+1, gpt-oss 4+1+1, nemotron 19+2+1 (sum 34); **7** `reasoning_unfaithful`
  (scores 0.717, 0.721, 0.722, 0.925, 0.942, 0.944, 0.969).
- Wilcoxon: n = 64/54/51, median Δ +0.0044 / −0.0022 / +0.0043, p 0.095 / 0.52 / 0.72, r 0.21/0.09/0.05.
- Tokens per backend×condition match (one sub-token rounding note below).

---

## 5. Screenshots needed (Chapter 5 — user to capture from the running `main` app)
Existing embedded screenshots are kept. The brief recommends one more to support RQ2:
- **`SCREENSHOT REQUIRED — USER TO CAPTURE`:** a real `reasoning_unfaithful` result — e.g. the
  `analysis` service response or a stored `*.verification.json` for one of the seven flagged B2 runs,
  showing the decision whose `action_present=false` or `value_matches=false`. Belongs in §5.7
  (Implemented Verifier) or §6 RQ2. Caption: "A real `reasoning_unfaithful` verdict: the cited
  action is absent from / the cited value disagrees with the generated code." Do not fabricate.

---

## 6. Unresolved — need your input

1. **Reference audit:** confirm the forward-dated citations resolve to real papers with correct
   fields — `jiang2026survey`, `shen2026failure`, `young2026lie`, `zhao2026crv`, `perrier2025`,
   `li2025structured`, `tyen2024`, and especially **`flowpipe`** (SIGMOD 2027 status).
2. **Appendix CO list:** CO1–CO6 currently read as a generic research-methodology course
   (nature of science, Z/T/Chi-square tests, dimensional analysis). **Confirm these are the
   official Major-Project COs** or replace with the department's list. Left untouched.
3. **Appendix blank templates:** Patent/Copyright, Data Contribution, Grant Proposal,
   Commercialization/Startup/Tech-transfer/Industry, Research Conclave, SDG table, CO–PO and
   CO–PSO mappings + justifications — fill per department norms or remove the optional ones.
   Nothing was invented.
4. **Open-source appendix:** add the actual repository URLs (GitHub org) as evidence.
5. **Guide designation:** standardized to "Mr. Amit Singh" — confirm the correct title/designation.
6. **Diagrams:** draw Figures 3.1, 4.1, 4.2, 4.3 in Miro from §3; build 6.1–6.3 as charts.
   `architecture.png` is an existing embedded image — replace it with the Fig 4.1 brief version.
7. **LLM runner (optional):** on `main` the generation runner is configurable; the infra README
   lists OpenRouter with Ollama optional. The report now says only "Ollama (reported runs)", which
   is accurate. If you want, it can also note OpenRouter as a supported backend.
8. **Minor (no change made):** nemotron B1 prompt-token mean rounds to 1432 vs the table's 1433
   (sub-token boundary). Wilcoxon `z` signs are shown all-negative; magnitudes and p-values are
   exact and the verdict is unaffected (the sign is a convention).

---

## 7. Final verification checklist

- [x] Every implementation claim describes `main` only; `feat/rir-pir-verifier` not read or cited.
- [x] Proposed RIR/PIR framework is never presented as implemented (abstract, Ch1, Ch3, Ch4, Ch5, Ch7).
- [x] Post-hoc reasoning-trace limitation present in abstract, Ch3, Ch5, Ch6 **and** Ch7.
- [x] All Chapter 6 numbers reproduce from the clean 270-cell sweep; xlsx pilot tabs not used.
- [x] Seven unfaithful runs kept distinct from, and located within, the 34 unsuccessful B2 cells.
- [x] Statistics qualified (no "equivalence"; effect sizes added; discordant-pair `n` explained).
- [x] MinIO corrected to filesystem; ports verified; 13-category taxonomy and 4-table schema verified.
- [x] Ch7.1 cross-reference fixed; PRN typo fixed; guide designation standardized; "nine repos" fixed.
- [x] Publication/patent/grant status not overclaimed; nothing fabricated.
- [ ] **PDF compile + visual QA** — pending (no toolchain here; compile on Overleaf).
- [ ] **Figures 3.1 / 4.1 / 4.2 / 4.3 / 6.1 / 6.2 / 6.3** — pending user.
- [ ] **Reference + appendix (CO list, templates) confirmation** — pending user.

---

# Second correction pass — red-flags brief (same date)

A second, more detailed review brief was applied. It confirmed the first pass and added a
deeper statistical reframing. No diagrams were created, edited, or redrawn; no screenshots were
fabricated; `main` remained the only implementation source (the `feat/rir-pir-verifier` branch
was never read).

## A. Red flags fixed
1. **Raw execution vs verified yield (§3).** Introduced the distinction everywhere: **B2 raw
   execution = 63/90 = 70.0%** (produced a pipeline + score) vs **verified yield = 56/90 =
   62.2%** (also passed the faithfulness check). Added a four-outcome definition to the setup, a
   restructured Table 6.2 (verified yield / raw execution / mean score / iters), and a new
   per-backend reconciliation table (Table `tab:yield`): gemma 24/5/1→29; gpt-oss 24/1/5→25;
   nemotron 8/1/21→9; total 56/7/27→63. B0/B1 marked verifier = **not applicable**.
2. **Predictive-score survivorship (§4).** Recomputed the predictive analysis over **executed**
   runs (B2 = 63, including the 7 scored-but-unfaithful), with verifier outcome as a separate
   variable. Table 6.4 (per-dataset) and Table 6.6 (Wilcoxon) now use the executed basis.
3. **Trace completeness (§6).** Corrected "all 90 B2 cells produced a parseable trace" → **the 63
   that executed** did; the 27 that failed before output store a `null` trace. (The `reasoning_trace`
   column is literally `"null"` for those 27 — verified.)
4. **±0.02 claim (§7).** Replaced with "differ by at most ~0.03 (largest gap ≈0.025)".
5. **Token multiplier (§7).** "5–25×" → "~5–27× (≈5–8× over B1, ≈18–27× over B0)", baseline stated.
6. **Symbol-presence ≠ use (§2).** Added to Threats (construct validity): the presence check
   passes on a symbol that appears only in an import; a `PASS` means the implemented checks passed,
   not semantic faithfulness; false positives acknowledged.
7. **Service wording (§9).** "seven small services behind a gateway" → "five FastAPI backend
   services (incl. gateway) plus a Redis Queue worker and two front-ends".
8. **FlowPipe reference (§12).** Converted to an `@misc` arXiv preprint (2026); the SIGMOD 2027
   DOI is noted as "reported but not independently verified". No longer labelled "to appear".
9. **SPDX typo (§10).** `an SPDX SPDX-License-Identifier` → `an SPDX-License-Identifier`.
10. **Table 6.7 clipping (§10).** Status column changed from `l` to `p{4cm}` so it wraps.

## B. Numbers recalculated (source: `experiment_sweeps (1).xlsx` → `run_results`, seeds 3001–3005)
- B2 raw execution 63/90; by backend 29 / 25 / 9. Verified 56; by backend 24 / 24 / 8.
- Per-condition mean score over executed: B0 0.8432 (n=90), B1 0.8468 (90), B2 **0.8542** (63).
- Per-dataset B2 over executed: wdbc 13/0.9706, breast-w 11/0.9542, diabetes 12/0.7130,
  Titanic 6/0.7670, spambase 12/0.9348, Telco 9/0.7028.
- Wilcoxon over executed: B0–B1 n=64 (+0.0044, z −1.67, p 0.095, r 0.21); **B0–B2 n=60 (+0.000,
  z −0.21, p 0.831, r 0.03); B1–B2 n=58 (+0.0007, z −0.16, p 0.874, r 0.02)**.
- Token record counts: nemotron B2 = 14/30 (others 30/30); total 74/90.
- Real reasoning traces: 63 (the executed B2 runs); 27 stored `null`.
- Max per-dataset score gap ≈ 0.025 (Telco B0–B1, Titanic B1–B2).

## C. Figure-data updates (briefs in §3 above still apply, with these values)
- **Fig 6.1** should show both verified yield and raw execution per backend
  (gemma 80%/96.7%, gpt-oss 80%/83.3%, nemotron 26.7%/30.0%), or at least label the bars as
  verified yield; overall B2 62.2% verified / 70.0% raw.
- **Fig 6.2** = the 34 end-to-end-unsuccessful cells (counts unchanged); keep the 7
  `reasoning_unfaithful` marked as verifier rejections that executed.
- **Fig 6.3** now plots executed runs (B2 n=63); denominators B0 90 / B1 90 / B2 63.

## D. "Implemented" → "proposed/not-implemented" (unchanged from first pass, for reference)
Abstract, Ch1 (objectives, contributions, status), Ch3 (scope banner, metrics, baselines),
Ch4 (verifier module, data flow), Ch5 (status table, RIR/PIR prototype paragraph, proposed-
components section, algorithm prose), Ch7 (three-levels paragraph). All feature-branch
"implemented" claims removed; RIR/PIR framework, graph PIR, full taxonomy, source-span
localisation, baseline/mutation evaluation, and B2R all labelled proposed/future.

## E. Unresolved (in addition to §6 above)
- The executed-basis predictive analysis assumes the 7 unfaithful runs' scores are valid
  predictive scores — they are present and finite in `run_results`; confirm this is the intended
  treatment for your write-up (verifier outcome is kept as a separate variable, as the brief asked).
- FlowPipe acceptance status still needs your confirmation (you may hold evidence the prior
  session relied on).

## F. Confirmations
- **Source of truth:** only `main` was used; `feat/rir-pir-verifier` was never checked out or read.
- **Diagrams:** none created, redrawn, edited, or added; placeholders left untouched; no SVG/Mermaid/etc.
- **No fabrication:** no invented screenshots, metrics, schema, citations, or admin/publication status.
- **Not submission-ready:** figures, PDF compile/QA, the appendix CO list and institutional
  templates, and the reference confirmations remain open (see §6 and §E).
