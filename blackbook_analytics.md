# Black Book — Data Analytics Provenance

_Generated 2026-11-10 from the live `Auto-ML-Postgres` container (post Stage-4b Sweep 6) and
cross-checked against `review/stage4b-secondary/` summaries. Every number in Chapter 6 of the
black book traces to this file. No values are invented._

## Source of record

- **Primary data:** 270 rows in `run_results` (PostgreSQL 16, container `Auto-ML-Postgres`),
  seeds 3001–3005, exported read-only to `sweep_raw.csv`.
- **NOT used as results:** `review/results.xlsx` + `review/csv/*.csv` — these are the stale
  **mid-term snapshot (2026-07-14): only 6 runs, all B2, one model, regression**. Superseded by
  the 270-run sweep. Kept only as a record of the project's earlier state.
- Design: 6 datasets × 3 conditions (B0/B1/B2) × 3 backends × 5 seeds = **270 cells**.
  Backends: `gpt-oss:120b-cloud`, `gemma4:31b-cloud`, `nemotron-3-nano:30b-cloud`.
  Wall clock 5 h 17 m; leakage check 0 hits.

## 1. Per-condition overall (n = 90 cells each)

| Condition | Success | Success % | Mean score (successful) | Mean iterations |
|---|---:|---:|---:|---:|
| B0 (naive)        | 90/90 | 100.0 % | 0.8432 | 1.01 |
| B1 (schema)       | 90/90 | 100.0 % | 0.8468 | 1.06 |
| B2 (meta-feature) | 56/90 | **62.2 %** | 0.8549\* | 1.41 |

\* B2's slightly higher mean is **survivorship bias** — only the 56 easiest cells survive;
the 34 failures are excluded. The paired test (§3) is the valid comparison.

## 2. Per-dataset × condition (mean balanced-accuracy on successful runs)

| Dataset | Rows | B0 ok / score | B1 ok / score | B2 ok / score |
|---|---:|---|---|---|
| wdbc     | 569  | 15 / 0.9618 | 15 / 0.9673 | 12 / 0.9707 |
| breast-w | 699  | 15 / 0.9524 | 15 / 0.9492 | 10 / 0.9554 |
| diabetes | 768  | 15 / 0.7215 | 15 / 0.7172 | 11 / 0.7126 |
| Titanic  | 891  | 15 / 0.7874 | 15 / 0.7898 |  6 / 0.7670 |
| spambase | 4601 | 15 / 0.9352 | 15 / 0.9313 | 10 / 0.9350 |
| Telco    | 7043 | 15 / 0.7009 | 15 / 0.7259 |  7 / 0.6974 |

B2 mean scores are within ±0.02 of B0/B1 on every dataset where it lands.

## 3. Paired Wilcoxon signed-rank on test_score (matched dataset,seed,model; both succeeded)

| Pair | n pairs | median(Δ) | z | p (2-sided) | effect r | verdict |
|---|---:|---:|---:|---:|---:|---|
| B0 vs B1 | 64 | +0.0044 | −1.669 | 0.095 | 0.209 | not significant |
| B0 vs B2 | 54 | −0.0022 | −0.637 | 0.524 | 0.087 | not significant |
| B1 vs B2 | 51 | +0.0043 | −0.356 | 0.722 | 0.050 | not significant |

Bonferroni-adjusted α = 0.0167 for 3 tests. **No pair shows a significant score difference.**
Conclusion: meta-feature prompting (B2) does **not** improve predictive score over naive (B0)
or schema (B1), while it costs reliability and tokens.

## 4. Token / cost (from per-model×condition summary)

| Backend | Cond | avg prompt tok | avg completion tok |
|---|---|---:|---:|
| gemma4:31b   | B0/B1/B2 | 348 / 1182 / 9280 | 627 / 611 / 2506 |
| gpt-oss:120b | B0/B1/B2 | 369 / 1170 / 6449 | 1338 / 1346 / 5330 |
| nemotron-nano| B0/B1/B2 | 348 / 1433 / 7373 | 2112 / 2660 / **26244** |

B2 prompts are 5–25× larger than B0; nemotron-nano runs away to 26k completion tokens on B2
(cause of its 19 infrastructure failures / 502s).

## 5. Error taxonomy (only B2 produced failures; B0/B1 were 100 %)

| Backend | error_category | n |
|---|---|---:|
| gemma4:31b   | reasoning_unfaithful | 5 |
| gemma4:31b   | syntax_error | 1 |
| gpt-oss:120b | runtime_other | 4 |
| gpt-oss:120b | api_hallucination | 1 |
| gpt-oss:120b | reasoning_unfaithful | 1 |
| nemotron-nano| infrastructure | 19 |
| nemotron-nano| import_error | 2 |
| nemotron-nano| reasoning_unfaithful | 1 |

## 6. Mechanical verifier (prototype v1) on real B2 runs — the headline evidence

- 90 B2 cells, **all carry a reasoning trace**.
- **7 flagged `reasoning_unfaithful` (7.8 % of B2).**
- **All 7 executed and produced a score** (0.717, 0.721, 0.722, 0.925, 0.942, 0.944, 0.969)
  **but were rejected by the verifier.** These are pipelines that run and score well yet whose
  stated reasoning does not match the code / meta-features — exactly the failure mode that
  execution-only and performance-only checks cannot see. By model: gemma 5, gpt-oss 1, nemotron 1.

This is the project's core thesis demonstrated on real data: **execution success ≠
reasoning–code alignment**, and a mechanical check catches the gap.
