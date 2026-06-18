# Phase 1.5 Clean GNN-LPD-DQN Traffic Engineering Report

**Subtitle:** Main comparison subset N = 3,288 including Tiscali; full internal protocol N = 3,976 reported separately

---

> **Source-scope lock:** Tiscali is included in the main N = 3,288 comparison subset and in all CDF
> plots. However, **no source-locked FlexDATE reference row for Tiscali exists in the original report.**
> The audit-script constants are method-internal configuration, not valid FlexDATE source evidence.
> Tiscali is therefore reported as an internal / runtime-limitation row and is **not** claimed as a
> FlexDATE win/loss. The four topologies with a genuine source-locked FlexDATE reference
> (Abilene, CERNET, GEANT, Sprintlink) win both PR and DB.
> Full N = 3,976 internal benchmark results are reported separately and are not used to establish
> the main comparison claim.

> **Method lock:** The final clean method is named **Clean GNN-LPD-DQN Traffic Engineering**.
> A DB-budgeted LP-distilled GNN-LPD selector ranks critical/improvable OD pairs. A DQN controller
> chooses the routing action and DB budget. A one-stage selected-flow DB-budgeted LP computes
> routing for selected ODs, while noncritical ODs remain on ECMP or previous routing.
> The clean method excludes legacy post-processing/finalization components.

---

## Method Identity

```
Traffic matrix + topology
→ DB-budgeted LP-distilled GNN-LPD critical-OD selector
→ DQN controller chooses K / action / budget
→ selected critical ODs → one-stage selected-flow DB-budgeted LP
→ noncritical ODs remain on ECMP or previous routing
→ capped K escalation if PR/MLU guard fails (K30 → K40 → K50)
→ full-OD fallback only after selected-K cap fails
→ evaluation
```

**Mandatory compliance statement:**
The reported clean run uses:
- GNN-LPD selector trained from DB-budgeted full-OD LP oracle labels
- DQN controller (9-action K/budget space)
- One-stage selected-flow DB-budgeted LP
- No heuristic criticality
- No RandomForest gate
- No sticky gate / sticky reuse
- No Stage-2 DB LP
- No disturbance-finalization LP
- No legacy finalization components (details in audit section)

---

## Section 1 — Dataset Protocol

**Table 1. Dataset and TM protocol**

| Topology | Role | TM source | Training TMs | Internal eval TMs | Model Train | Validation | Internal Test | Window |
|---|---|---|---|---|---|---|---|---|
| Abilene | Seen training | Real | 2,016 | 2,016 | 1,612 | 201 | 203 | 2016–4031 |
| GEANT | Seen training | Real | 672 | 672 | 537 | 67 | 68 | 672–1343 |
| CERNET | Seen training | MGM synthetic | 200 | 200 | 160 | 20 | 20 | 200–399 |
| Sprintlink | Seen training | MGM synthetic | 200 | 200 | 160 | 20 | 20 | 200–399 |
| Tiscali | Seen training | MGM synthetic | 200 | 200 | 160 | 20 | 20 | 200–399 |
| Ebone | Seen training | MGM synthetic | 200 | 200 | 160 | 20 | 20 | 200–399 |
| Germany50 | Zero-shot evaluation | Real | 0 | 288 | 0 | 0 | 0 | 0–287 |
| VtlWavenet2011 | Zero-shot evaluation | MGM synthetic | 0 | 200 | 0 | 0 | 0 | 0–199 |
| **Total seen training** | | | **3,488** | | | | | |
| **Main comparison subset** | | | | **3,288** | | | | |
| **Full internal evaluation** | | | | **3,976** | | | | |

**Table 1b. Source-scope map**

| Scope | Source file | N | Topologies |
|---|---|---|---|
| Main comparison subset | `final_N3976/per_cycle.csv` (filtered) | 3,288 | Abilene, CERNET, GEANT, Sprintlink, Tiscali |
| CDF source | `final_N3976/per_cycle.csv` (filtered) | 3,288 | Abilene, CERNET, GEANT, Sprintlink, Tiscali |
| Full internal clean evaluation | `final_N3976/per_cycle.csv` | 3,976 | All 8 topologies |
| Clean audit | `final_N3976/method_audit.json` | — | Method compliance flags |
| Label provenance | `labels/label_provenance.json` | — | DB-budgeted LP oracle provenance |

---

## Section 2 — Main Comparison Table

**Comparison scope:** Main comparison subset is N = 3,288 over five topologies: Abilene, CERNET,
GEANT, Sprintlink, and Tiscali. **Only four of these (Abilene, CERNET, GEANT, Sprintlink) have a
source-locked FlexDATE reference row in the original report.** Tiscali has no source-locked
FlexDATE reference row, so its FlexDATE columns are marked N.A. and it is reported as an
internal / runtime-limitation row rather than as a FlexDATE win/loss.

**Table 2. Main comparison subset including Tiscali (N = 3,288)**

| Topology | Rows | Our PR | FlexDATE PR | PR Result | Our DB | FlexDATE DB | DB Result | Overall | PR≥0.95 | Min PR | P95 ms |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Abilene | 2,016 | 99.669% | 95.800% | WIN | 0.613% | 5.130% | WIN | WIN BOTH | 100.000% | 96.038% | 7.2 |
| CERNET | 200 | 99.420% | 97.500% | WIN | 0.321% | 1.830% | WIN | WIN BOTH | 100.000% | 97.721% | 29.0 |
| GEANT | 672 | 99.984% | 99.500% | WIN | 0.378% | 2.960% | WIN | WIN BOTH | 100.000% | 99.621% | 15.3 |
| Sprintlink | 200 | 100.000% | 99.900% | WIN | 0.471% | 5.100% | WIN | WIN BOTH | 100.000% | 100.000% | 1,082.2 |
| Tiscali | 200 | 100.000% | N.A. | n/c | 0.714% | N.A. | n/c | reported / runtime limitation | 100.000% | 99.950% | 9,255.5 |
| **Weighted (N=3,288)** | **3,288** | **99.758%** | — | — | **0.544%** | — | — | **4/4 FlexDATE-reference topologies WIN BOTH + Tiscali internal/runtime limitation** | **100.000%** | **96.038%** | **1,252.4** |

> Tiscali has **no source-locked FlexDATE reference row** in the original report. The
> `tiscali` constant in `scripts/phase1_5/audit_gnn_lpd_dqn_clean_method.py` is method-internal
> audit configuration, **not** valid FlexDATE source evidence, and is not used to claim a win/loss.
> Tiscali FlexDATE PR/DB are therefore N.A. and its PR/DB results are not computable (n/c).
> The four FlexDATE-reference topologies (Abilene, CERNET, GEANT, Sprintlink) win both PR and DB.
> Tiscali's own PR=100.000% and DB=0.714% are strong internal results, but its runtime is a
> limitation: 81.5% full-OD fallback, mean 4,340 ms, P95 9,255 ms.
>
> Sprintlink P95 exceeds 500 ms due to safety full-OD fallback on 11.5% of cycles;
> mean decision time is 155.5 ms.

---

## Section 3 — Final-Method Summary (Main Subset N = 3,288)

**Table 3. Final-method summary on the main comparison subset (N = 3,288)**

| Topology | N | Mean PR | PR≥0.95 | PR≥0.90 | Min PR | Mean DB | P95 DB | P50 ms | P95 ms | Full-OD% | Scope |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Abilene | 2,016 | 99.669% | 100.000% | 100.000% | 96.038% | 0.613% | 1.111% | 6.7 | 7.2 | 2.3% | Main subset |
| CERNET | 200 | 99.420% | 100.000% | 100.000% | 97.721% | 0.321% | 0.184% | 25.7 | 29.0 | 1.5% | Main subset |
| GEANT | 672 | 99.984% | 100.000% | 100.000% | 99.621% | 0.378% | 0.750% | 13.6 | 15.3 | 3.1% | Main subset |
| Sprintlink | 200 | 100.000% | 100.000% | 100.000% | 100.000% | 0.471% | 0.290% | 32.2 | 1,082.2 | 11.5% | Main subset |
| Tiscali | 200 | 100.000% | 100.000% | 100.000% | 99.950% | 0.714% | 1.212% | 4,297.6 | 9,255.5 | 81.5% | Main subset / runtime limitation |
| **Weighted (N=3,288)** | **3,288** | **99.758%** | **100.000%** | **100.000%** | **96.038%** | **0.544%** | **1.000%** | **6.9** | **1,252.4** | **7.8%** | **Main subset pooled** |

> Weighted pooled rows computed directly from `per_cycle.csv` filtered to the five main-subset
> topologies (N=3,288), not by averaging table rows. P95 ms of 1,252.4 ms is dominated by
> Tiscali's high full-OD fallback rate.

---

## Section 4 — Full Internal Evaluation (N = 3,976)

**Table 4. Final clean method on full internal evaluation protocol (N = 3,976; Scope B)**

| Topology | N | Mean PR | PR≥0.90 | PR≥0.95 | Min PR | Mean DB | P95 DB | Mean ms | P95 ms | Full-OD% |
|---|---|---|---|---|---|---|---|---|---|---|
| Abilene | 2,016 | 99.669% | 100.000% | 100.000% | 96.038% | 0.613% | 1.111% | 7.1 | 7.2 | 2.3% |
| CERNET | 200 | 99.420% | 100.000% | 100.000% | 97.721% | 0.321% | 0.184% | 23.3 | 29.0 | 1.5% |
| Ebone | 200 | 100.000% | 100.000% | 100.000% | 100.000% | 0.125% | 0.193% | 14.2 | 14.9 | 0.0% |
| GEANT | 672 | 99.984% | 100.000% | 100.000% | 99.621% | 0.378% | 0.750% | 18.9 | 15.3 | 3.1% |
| Germany50 | 288 | 99.814% | 100.000% | 100.000% | 95.483% | 1.061% | 1.823% | 73.7 | 426.7 | 5.9% |
| Sprintlink | 200 | 100.000% | 100.000% | 100.000% | 100.000% | 0.471% | 0.290% | 155.5 | 1,082.2 | 11.5% |
| Tiscali | 200 | 100.000% | 100.000% | 100.000% | 99.950% | 0.714% | 1.212% | 4,340.4 | 9,255.5 | 81.5% |
| VtlWavenet2011 | 200 | 99.134% | 100.000% | 100.000% | 98.818% | 0.230% | 0.082% | 128.3 | 120.8 | 0.5% |
| **Weighted total (N=3,976)** | **3,976** | **99.743%** | **100.000%** | **100.000%** | **95.483%** | **0.545%** | **1.000%** | **246.6** | **657.2** | **6.9%** |

> Weighted total computed directly from `per_cycle.csv` (3,976 rows), not by averaging table rows.

---

## Section 4b — Additional Full Metrics

**Table 4b. Extended metrics — full internal evaluation (computed from per_cycle.csv)**

| Topology | N | Median PR | PR≥0.99 | Max DB | Max ms |
|---|---|---|---|---|---|
| Abilene | 2,016 | 100.000% | 88.988% | 15.040% | 189.7 |
| CERNET | 200 | 99.600% | 74.000% | 53.202% | 656.8 |
| Ebone | 200 | 100.000% | 100.000% | 9.980% | 16.0 |
| GEANT | 672 | 100.000% | 100.000% | 69.681% | 340.7 |
| Germany50 | 288 | 100.000% | 93.403% | 86.589% | 1,415.8 |
| Sprintlink | 200 | 100.000% | 100.000% | 78.809% | 2,113.7 |
| Tiscali | 200 | 100.000% | 100.000% | 54.825% | 10,842.5 |
| VtlWavenet2011 | 200 | 99.026% | 52.500% | 39.605% | 1,881.6 |
| **All (N=3,976)** | **3,976** | **100.000%** | **90.241%** | **86.589%** | **10,842.5** |

> Max DB values reflect rare per-cycle outliers; headline DB stability is assessed using mean and P95 DB.
> MLU diagnostic fields (Mean MLU, P95 MLU, MLU/strict, MLU/ECMP) were not present in the
> clean run per-cycle artifact and are therefore not re-reported here. PR and DB remain the
> primary headline metrics.

---

## Section 5 — Internal Baselines and Ablations

**Table 5. Internal baseline / ablation rows and clean final method (N = 3,976; not direct claim)**

| Method | N | Mean PR | PR≥0.95 | Mean DB | P95 DB | Mean ms | P95 ms | Notes |
|---|---|---|---|---|---|---|---|---|
| **Clean GNN-LPD-DQN selected-flow DB-budgeted LP** | **3,976** | **99.743%** | **100.000%** | **0.545%** | **1.000%** | **246.6** | **657.2** | **Final clean method** |

> Historical baselines (ECMP, OSPF, Top-K, Bottleneck, GNN-only, LPD-only, legacy
> Reward-Gated GNN-LPD) were not regenerated in this clean run and are not mixed into
> the final clean-method claim. The legacy reward-gated artifact is retained only as a
> historical report reproduction reference under the separate legacy audit
> (`audit_legacy_reward_gated_gnn_lpd_report.py`).

Tables 6 and 7 retain the original fixed-K GNN+LPD sensitivity rows for context, but the
final-method row is updated to the current clean GNN-LPD-DQN method evaluated on the full
N=3,976 protocol. The old Reward-Gated GNN-LPD final rows are legacy rows and are not the
final method in this report.

**Table 6. Internal K-sensitivity rows (N=3976; not direct FlexDATE claim)**

| Method/Budget | N | Mean PR | PR≥0.90 | PR≥0.95 | Mean DB | P95 DB | Mean ms | P95 ms |
|---|---|---|---|---|---|---|---|---|
| GNN+LPD fixed K10 | 3976 | 87.222% | 53.295% | 39.336% | 10.928% | 29.134% | 64.2 | 424.5 |
| GNN+LPD fixed K20 | 3976 | 94.294% | 81.665% | 65.116% | 14.284% | 36.248% | 68.0 | 457.5 |
| GNN+LPD fixed K30 | 3976 | 98.377% | 97.485% | 92.178% | 18.488% | 48.224% | 73.7 | 422.2 |
| GNN+LPD fixed K40 | 3976 | 99.075% | 99.346% | 95.020% | 20.529% | 52.123% | 144.6 | 604.8 |
| GNN+LPD fixed K50 | 3976 | 99.310% | 99.774% | 95.674% | 22.987% | 52.244% | 74.6 | 406.9 |
| **Clean GNN-LPD-DQN Traffic Engineering (final clean method, adaptive DQN K30/K40/K50, no sticky/RF/Stage-2)** | **3976** | **99.743%** | **100.000%** | **100.000%** | **0.545%** | **1.000%** | **246.6** | **657.2** |

**Table 7. Gate/final-method contrast with scope separated**

| Method/Budget | N | Mean PR | PR≥0.90 | PR≥0.95 | Mean DB | P95 DB | Mean ms | P95 ms |
|---|---|---|---|---|---|---|---|---|
| GNN+LPD fixed K30 | 3976 | 98.377% | 97.485% | 92.178% | 18.488% | 48.224% | 73.7 | 422.2 |
| GNN+LPD fixed K40 | 3976 | 99.075% | 99.346% | 95.020% | 20.529% | 52.123% | 144.6 | 604.8 |
| GNN+LPD fixed K50 | 3976 | 99.310% | 99.774% | 95.674% | 22.987% | 52.244% | 74.6 | 406.9 |
| **Clean GNN-LPD-DQN Traffic Engineering (final clean method)** | **3976** | **99.743%** | **100.000%** | **100.000%** | **0.545%** | **1.000%** | **246.6** | **657.2** |

> The final-method row in Tables 6 and 7 is the **Clean GNN-LPD-DQN Traffic Engineering**
> method (adaptive DQN over K30/K40/K50 with selected-flow DB-budgeted LP; no sticky reuse,
> no RandomForest gate, no Stage-2 LP). The legacy Reward-Gated GNN-LPD final rows from the
> original accepted report are **not** presented here as the final method; any such rows
> appear only in Appendix B (historical legacy) and are clearly labelled as not the final
> clean method.

---

## Section 6 — Clean DQN Policy

**Table 8. Observed clean DQN action distribution (N = 3,976)**

| Action | Count | Share |
|---|---|---|
| OPTIMIZE_K30_DB_0.01 | 3,269 | 82.2% |
| OPTIMIZE_K40_DB_0.03 | 405 | 10.2% |
| OPTIMIZE_K30_DB_0.03 | 193 | 4.9% |
| KEEP_PREVIOUS_ROUTING | 97 | 2.4% |
| OPTIMIZE_K40_DB_0.01 | 12 | 0.3% |
| K50 / FULL_OD actions (DQN-selected) | 0 | 0.0% |
| **Total** | **3,976** | **100%** |

> K50 and explicit FULL_OD_FALLBACK actions were part of the action space but were not
> selected directly by the trained DQN in this run. Full-OD fallback was activated only
> by the safety controller after selected-K optimisation failed the PR/MLU guard.

**Per-topology action distribution**

| Topology | KEEP | K30\_DB0.01 | K30\_DB0.03 | K40\_DB0.01 | K40\_DB0.03 | Total |
|---|---|---|---|---|---|---|
| Abilene | 0 | 1,704 | 156 | 0 | 156 | 2,016 |
| CERNET | 93 | 36 | 6 | 0 | 65 | 200 |
| Ebone | 0 | 200 | 0 | 0 | 0 | 200 |
| GEANT | 0 | 662 | 3 | 0 | 7 | 672 |
| Germany50 | 4 | 274 | 2 | 2 | 6 | 288 |
| Sprintlink | 0 | 165 | 9 | 9 | 17 | 200 |
| Tiscali | 0 | 30 | 16 | 1 | 153 | 200 |
| VtlWavenet2011 | 0 | 198 | 1 | 0 | 1 | 200 |
| **Total** | **97** | **3,269** | **193** | **12** | **405** | **3,976** |

**Table 9. Clean DQN settings**

| Parameter | Value |
|---|---|
| Controller | Double DQN (ε-greedy training) |
| Selector | DB-budgeted LP-distilled GNN-LPD (GNNFlowSelector, 72,886 params) |
| GNN checkpoint | `results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector.pt` |
| Label oracle | Full-OD DB-budgeted LP (`solve_selected_path_lp_dbbudget`, db=0.10) |
| Heuristic labels | No |
| RandomForest | No |
| Sticky gate / reuse | No |
| Stage-2 / disturbance finalization | No |
| Action family | KEEP / OPTIMIZE\_K30\_DB{0.01,0.03} / OPTIMIZE\_K40\_DB{0.01,0.03} / OPTIMIZE\_K50\_DB{0.01,0.03} / FULL\_OD\_PR\_SAFE / FULL\_OD\_LOW\_MLU |
| Reported selected actions | KEEP, K30 DB budgets, K40 DB budgets |
| Fallback trigger | PR/MLU guard after selected-K cap (K50) fails |
| Training topologies | Abilene, CERNET, GEANT, Ebone |
| DQN val PR | 1.0000 (200 episodes) |
| FULL\_OD excluded from exploration | Yes (speed guard) |

---

## Section 7 — Decision-Time Analysis

**Table 10. Decision-time summary by topology (main N = 3,288 + Ebone/Germany50/VtlWavenet2011)**

| Topology | N | Mean ms | P95 ms | Max ms | Full-OD% | Mean sel-OD | Runtime interpretation |
|---|---|---|---|---|---|---|---|
| Abilene | 2,016 | 7.1 | 7.2 | 189.7 | 2.3% | 32.0 | Fast selected-flow regime |
| CERNET | 200 | 23.3 | 29.0 | 656.8 | 1.5% | 43.5 | Fast selected-flow regime |
| GEANT | 672 | 18.9 | 15.3 | 340.7 | 3.1% | 42.6 | Fast selected-flow regime |
| Sprintlink | 200 | 155.5 | 1,082.2 | 2,113.7 | 11.5% | 244.8 | Mean < 500 ms; P95 affected by safety fallback |
| Tiscali | 200 | 4,340.4 | 9,255.5 | 10,842.5 | 81.5% | 1,923.9 | **Runtime limitation** — fallback-heavy large-OD topology |
| Ebone | 200 | 14.2 | 14.9 | 16.0 | 0.0% | 30.0 | Fast selected-flow regime |
| Germany50 | 288 | 73.7 | 426.7 | 1,415.8 | 5.9% | 108.3 | Mean and P95 < 500 ms except rare max fallback |
| VtlWavenet2011 | 200 | 128.3 | 120.8 | 1,881.6 | 0.5% | 71.7 | Mostly selected-flow regime |
| **Pooled (N=3,976)** | **3,976** | **246.6** | **657.2** | **10,842.5** | **6.9%** | **147.7** | Pooled P95 affected by Tiscali |

> **Direct runtime conclusion:**
> Mean decision time is below 500 ms on **four of five main comparison topologies** (Abilene,
> CERNET, GEANT, Sprintlink). Tiscali exceeds the runtime target due to an 81.5% full-OD fallback
> rate driven by a very large OD count (≈2,352 ODs). P95 decision time is below 500 ms on
> **three of five** main comparison topologies; Sprintlink and Tiscali exceed 500 ms at P95 because
> of safety full-OD fallback. PR and DB quality remain excellent on all five topologies
> (PR ≥ 99.420% on all five, DB ≤ 0.714% on all five).

**Table 11. Timing-scope interpretation**

| Regime | Description | Affected topologies |
|---|---|---|
| Clean selected-flow normal | Selected ODs only; typically < 50 ms | Abilene, CERNET, GEANT, Ebone |
| Safety fallback — moderate | Full-OD LP after PR/MLU guard; affects P95 but mean is manageable | Sprintlink (P95 > 500 ms, mean 155 ms) |
| Safety fallback — severe | Full-OD LP dominates; known limitation | Tiscali (mean 4,340 ms, P95 9,255 ms) |
| Full internal pooled timing | Mean 246.6 ms; P95 657.2 ms; Tiscali fallback dominant | All 3,976 cycles |

**Table LP-1. LP problem-size and runtime by topology (full N = 3,976)**

| Topology | Nodes | Links | OD pairs | Mean sel-OD | K paths | Mean ms | P95 ms | FO rate |
|---|---|---|---|---|---|---|---|---|
| Abilene | 12 | 30 | 132 | 32.0 | 8 | 7.1 | 7.2 | 2.3% |
| CERNET | 41 | 116 | 1,640 | 43.5 | 8 | 23.3 | 29.0 | 1.5% |
| GEANT | 22 | 72 | 462 | 42.6 | 8 | 18.9 | 15.3 | 3.1% |
| Sprintlink | 44 | 166 | 1,892 | 244.8 | 8 | 155.5 | 1,082.2 | 11.5% |
| **Tiscali** | **49** | **172** | **2,352** | **1,923.9** | **8** | **4,340.4** | **9,255.5** | **81.5%** |
| Ebone | 23 | 76 | 506 | 30.0 | 8 | 14.2 | 14.9 | 0.0% |
| Germany50 | 50 | 176 | 2,450 | 108.3 | 8 | 73.7 | 426.7 | 5.9% |
| VtlWavenet2011 | 92 | 192 | 8,372 | 71.7 | 8 | 128.3 | 120.8 | 0.5% |

> Tiscali runtime limitation: with 2,352 OD pairs and a GNN selector that consistently scores
> nearly all ODs as critical (mean selected = 1,923.9), the K=30/40/50 cap is exceeded on
> nearly every cycle, forcing full-OD LP fallback. Full-OD LP with 2,352 ODs × 8 paths is
> computationally expensive. This is a known limitation of the selected-flow approach on
> very dense topologies and does not affect PR or DB quality.

---

## Section 8 — Compliance Audit

**Table. Clean-method compliance audit**

| Field | Value | Compliant |
|---|---|---|
| Method | gnn\_lpd\_dqn\_selective\_db\_lp | — |
| GNN used | 1 (every row) | ✓ |
| LPD used | 1 (every row) | ✓ |
| Criticality backend | gnn\_lpd | ✓ |
| Heuristic used | 0 | ✓ |
| DQN used | 1 (every row) | ✓ |
| Selected OD LP used | 1 (every row) | ✓ |
| Stage-2 used | 0 | ✓ |
| Disturbance finalization used | 0 | ✓ |
| RandomForest gate used | 0 | ✓ |
| Sticky gate used | 0 | ✓ |
| Audit blocks passed | 10/10 | ✓ |
| **Audit result** | **PASS** | ✓ |

> The clean run passes the professor-compliance audit. The result is not the legacy
> RandomForest/sticky/finalization artifact. It is a separate clean GNN-LPD-DQN
> selected-flow DB-budgeted LP run. Detailed forbidden-function names and audit block
> identifiers are documented in `scripts/phase1_5/audit_gnn_lpd_dqn_clean_method.py`.

---

## Section 9 — Clean Failure-Scenario Validation

> **Scope note:** All failure-scenario results in this section are from a dedicated clean
> rerun using the same GNN-LPD-DQN method. Results are NOT from the legacy
> RandomForest/sticky/finalization artifact. Every cycle carries audit flags confirming
> gnn\_used=1, lpd\_used=1, dqn\_used=1, heuristic\_used=0, rf\_gate\_used=0,
> sticky\_used=0, stage2\_used=0, disturbance\_finalization\_used=0.
> Audit result: **PASS** (360 cycles across 2 topologies × 9 scenarios × 20 cycles).

> **Metric note:** Clean failure validation reports PR (performance ratio vs path-optimal MLU),
> DB (disturbance budget), and decision-time metrics for the clean GNN-LPD-DQN method. It is
> **not** the same normalized-MLU ECMP/OSPF failure table used in the original report, and the
> two are not directly comparable row-for-row. Every number below is reproducible from
> `failure_per_cycle.csv`.

Failure validation was executed on Abilene (12 nodes, 30 links) and GEANT (22 nodes, 72 links)
using the test-split TM cycles (Abilene t=2016–2035; GEANT t=672–691). Nine scenarios were
evaluated per topology.

**Script:** `scripts/phase1_5/run_failure_validation_clean.py`
**Output:** `results/gnn_lpd_dqn_selective_db_lp/failure_validation_clean/`

**Failure scenario definitions:**

| Scenario | Description |
|---|---|
| normal | Baseline; original link capacities |
| single\_link\_failure | Highest-capacity directed link zeroed (cap → 0) |
| two\_link\_failure | Two highest-capacity directed links zeroed |
| three\_link\_failure | Three highest-capacity directed links zeroed |
| random\_link\_failure\_1 | Random link zeroed (seed=101) |
| random\_link\_failure\_2 | Random link zeroed (seed=202) |
| spike | Traffic matrix scaled ×3; original caps |
| mixed\_spike\_failure | Traffic matrix scaled ×3 + single link zeroed |
| capacity\_degradation\_50 | All link capacities scaled ×0.5 |

**Table F1. Failure validation summary — Abilene (N=20 cycles per scenario)**

| Scenario | N | Mean PR | Min PR | PR≥0.95 | Mean DB | P95 DB | Mean ms | P95 ms | FO rate | Disconn ODs | Audit |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Normal | 20 | 99.42% | 96.35% | 100% | 1.665% | 5.382% | 18.6 | 48.3 | 15% | 0 | PASS |
| Single-link failure | 20 | 99.12% | 96.07% | 100% | 2.378% | 5.215% | 20.8 | 48.3 | 40% | 0 | PASS |
| Two-link failure | 20 | 99.70% | 97.40% | 100% | 2.776% | 10.023% | 23.6 | 49.6 | 35% | 0 | PASS |
| Three-link failure | 20 | n/a | n/a | n/a | — | — | 39.9 | 40.8 | 100% | 3 | PASS |
| Random failure 1 | 20 | 98.34% | 96.00% | 100% | 2.020% | 5.313% | 18.8 | 49.1 | 25% | 0 | PASS |
| Random failure 2 | 20 | 100.00% | 100.00% | 100% | 0.166% | 0.166% | 13.4 | 29.6 | 10% | 0 | PASS |
| Spike ×3 | 20 | 99.67% | 96.85% | 100% | 1.168% | 3.042% | 17.0 | 33.2 | 15% | 0 | PASS |
| Mixed spike+fail | 20 | 99.12% | 96.07% | 100% | 2.377% | 5.215% | 19.9 | 45.7 | 40% | 0 | PASS |
| Cap degradation 50% | 20 | 99.41% | 97.10% | 100% | 1.259% | 3.100% | 16.5 | 31.9 | 15% | 0 | PASS |

> Three-link failure on Abilene disconnects 3 OD pairs (no path in the candidate library
> avoids all three zeroed links). PR is not reported for disconnected scenarios. This is
> expected behaviour for a 12-node topology with k=8 candidate paths under 3 simultaneous
> link failures. PR and DB remain excellent for all non-disconnecting scenarios.

**Table F2. Failure validation summary — GEANT (N=20 cycles per scenario)**

| Scenario | N | Mean PR | Min PR | PR≥0.95 | Mean DB | P95 DB | Mean ms | P95 ms | FO rate | Disconn ODs | Audit |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Normal | 20 | 99.97% | 99.67% | 100% | 0.263% | 0.702% | 49.8 | 176.8 | 10% | 0 | PASS |
| Single-link failure | 20 | 100.00% | 100.00% | 100% | 0.200% | 0.429% | 46.6 | 196.3 | 10% | 0 | PASS |
| Two-link failure | 20 | 100.00% | 100.00% | 100% | 0.165% | 0.439% | 47.3 | 190.3 | 10% | 0 | PASS |
| Three-link failure | 20 | 100.00% | 100.00% | 100% | 0.164% | 0.407% | 46.1 | 154.3 | 10% | 0 | PASS |
| Random failure 1 | 20 | 99.96% | 99.65% | 100% | 0.254% | 0.796% | 49.0 | 156.5 | 10% | 0 | PASS |
| Random failure 2 | 20 | 99.92% | 99.63% | 100% | 0.260% | 0.765% | 47.9 | 154.6 | 10% | 0 | PASS |
| Spike ×3 | 20 | 99.99% | 99.87% | 100% | 0.287% | 0.853% | 42.6 | 56.6 | 5% | 0 | PASS |
| Mixed spike+fail | 20 | 100.00% | 100.00% | 100% | 0.223% | 0.429% | 39.6 | 41.9 | 5% | 0 | PASS |
| Cap degradation 50% | 20 | 99.97% | 99.61% | 100% | 0.358% | 0.796% | 41.5 | 44.5 | 5% | 0 | PASS |

> GEANT shows strong resilience across all 9 scenarios. The additional path redundancy
> (22 nodes, 72 directed links) allows the clean method to route around failures without
> any disconnected ODs even under 3 simultaneous link failures.

**Key finding:** The clean GNN-LPD-DQN method maintains PR ≥ 95% across all non-disconnecting
failure scenarios on both Abilene and GEANT. Decision times remain well within the 500 ms target
for all GEANT scenarios (mean < 50 ms) and for most Abilene scenarios (mean < 40 ms). DB remains
low across all scenarios (≤ 2.8% mean).

**Output files:**
- `failure_validation_clean/failure_per_cycle.csv` — 360 per-cycle rows with full audit flags
- `failure_validation_clean/failure_summary.csv` — 18 scenario×topology summary rows
- `failure_validation_clean/failure_disconnect_detail.csv` — OD disconnectedness analysis
- `failure_validation_clean/failure_method_audit.json` — audit PASS confirmation
- `failure_validation_clean/failure_cdf_pr.png` — PR CDF by scenario
- `failure_validation_clean/failure_cdf_mlu.png` — MLU CDF by scenario
- `failure_validation_clean/failure_db_by_scenario.png` — DB by scenario bar chart
- `failure_validation_clean/failure_runtime_by_scenario.png` — decision-time by scenario

---

## Section 10 — SDN / Mininet Live Metrics from Original Operational Validation

> This table preserves the original SDN / Mininet live operational metrics from the accepted
> report artifact. It is reported as operational validation and is separate from the clean
> N=3,976 offline evaluation claim. These values must not be described as LP-derived simulation
> outputs.

These are the original SDN / Mininet **live** operational metrics, preserved exactly from the
accepted report artifact. They are presented as operational validation only and are **separate**
from, and not part of, the clean N=3,976 offline evaluation claim and the FlexDATE comparison.
They were **not** produced by the clean offline rerun.

**Table 13. SDN / Mininet live metrics (operational validation, separate from FlexDATE claim)**

| Topology | Scenario | Throughput | RTT | Jitter | Loss | Rule count | Install ms | Recovery ms | Disconnected | Decision ms |
|---|---|---|---|---|---|---|---|---|---|---|
| Abilene | Normal | 159.7 | 49.1 | 46.3 | 0.036% | 501 | 699.2 | No link-down event | 0 | 20.4 |
| Abilene | Single Link Failure | 131.9 | 18.4 | 35.7 | 0.052% | 530 | 720.6 | 1089.1 | 0 | 20.4 |
| Abilene | Two Link Failure | 173.9 | 15.7 | 50.3 | 0.115% | 534 | 935.9 | 1105.4 | 0 | 20.4 |
| Abilene | Three Link Failure | 139.6 | 18.9 | 46.0 | 0.077% | 548 | 804.8 | 1107.7 | 0 | 20.4 |
| Abilene | Random Link Failure 1 | 109.8 | 15.2 | 18.8 | 0.026% | 463 | 954.0 | 1008.4 | 11 | 20.4 |
| Abilene | Random Link Failure 2 | 58.2 | 24.4 | 9.8 | 0.000% | 519 | 863.4 | Probe path unaffected | 0 | 20.4 |
| Abilene | Spike | 64.2 | 10.2 | 9.4 | 0.000% | 522 | 1003.3 | Traffic spike only | 0 | 20.4 |
| Abilene | Mixed Spike Failure | 54.4 | 13.3 | 23.4 | 0.000% | 540 | 787.7 | Probe path unaffected | 0 | 20.4 |
| Abilene | Capacity Degradation 50% | 62.0 | 11.0 | 9.2 | 0.000% | 517 | 714.9 | Probe path unaffected | 0 | 20.4 |
| GEANT | Normal | 260.2 | 46.1 | 58.6 | 0.016% | 1748 | 1897.3 | No link-down event | 0 | 108.5 |
| GEANT | Single Link Failure | 265.7 | 32.7 | 52.2 | 0.016% | 1753 | 1441.1 | 1968.1 | 0 | 108.5 |
| GEANT | Two Link Failure | 261.5 | 15.8 | 54.7 | 0.044% | 1780 | 1497.5 | 1810.5 | 0 | 108.5 |
| GEANT | Three Link Failure | 202.4 | 10.3 | 48.6 | 0.021% | 1789 | 2632.2 | 3305.9 | 0 | 108.5 |
| GEANT | Random Link Failure 1 | 178.1 | 16.9 | 44.5 | 0.009% | 1787 | 1541.5 | 2369.0 | 0 | 108.5 |
| GEANT | Random Link Failure 2 | 167.4 | 15.3 | 54.9 | 0.006% | 1789 | 1876.8 | 1824.9 | 0 | 108.5 |
| GEANT | Spike | 186.1 | 26.5 | 59.0 | 0.000% | 1743 | 1832.6 | Traffic spike only | 0 | 108.5 |
| GEANT | Mixed Spike Failure | 171.3 | 10.3 | 47.6 | 0.001% | 1811 | 1851.6 | 2249.2 | 0 | 108.5 |
| GEANT | Capacity Degradation 50% | 159.8 | 7.8 | 60.5 | 0.041% | 1749 | 1795.3 | Probe path unaffected | 0 | 108.5 |

> Source separation: Table 13 holds original SDN / Mininet **live** operational metrics preserved
> from the accepted report artifact. It is operational validation only. It is not the clean offline
> N=3,976 evaluation, not a FlexDATE comparison row, and not an LP-derived simulation. The clean
> offline failure validation remains in Section 9. The earlier LP-derived SDN-style simulation
> (clean-method `run_sdn_mininet_clean.py --mode simulate`, 120 runs) is retained for completeness
> in Appendix A and is **not** used as the main operational validation.

---

## Section 11 — Reproducibility Metadata

| Parameter | Value |
|---|---|
| Git commit (local working tree) | `2f893f5` |
| GitHub push commit | `5858d88` → `github.com/moahaimen/final_trafic` main |
| Main method script | `scripts/phase1_5/gnn_lpd_dqn_selective_db_lp.py` |
| Label script | `scripts/phase1_5/build_dbbudget_oracle_labels.py` |
| Audit script | `scripts/phase1_5/audit_gnn_lpd_dqn_clean_method.py` |
| Failure validation script | `scripts/phase1_5/run_failure_validation_clean.py` |
| SDN validation script | `scripts/phase1_5/run_sdn_mininet_clean.py` |
| GNN checkpoint | `results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector.pt` |
| DQN checkpoint | `results/gnn_lpd_dqn_selective_db_lp/dqn_best.pt` |
| Label file | `results/gnn_lpd_dqn_selective_db_lp/labels/oracle_labels.csv` |
| Total label rows | 279,360 |
| CERNET label rows | 65,600 (40 cycles × 1,640 ODs, all LP-Optimal) |
| Evaluation per-cycle file | `results/gnn_lpd_dqn_selective_db_lp/final_N3976/per_cycle.csv` |
| Evaluation rows | 3,976 |
| Main comparison subset | 3,288 (Abilene, CERNET, GEANT, Sprintlink, Tiscali) |
| Clean audit result | PASSED — 10/10 blocks |
| Failure validation result | PASSED — 360 cycles, 2 topologies, 9 scenarios |
| SDN / Mininet operational validation | Section 10 — original live Mininet metrics preserved from accepted report artifact (Table 13) |
| LP-derived SDN simulation (Appendix A) | 120 runs, 2 topologies, 6 scenarios — supplementary only, not the main operational validation |
| Solver | PuLP with HiGHS preferred / CBC fallback |
| Zero-shot topologies | Germany50, VtlWavenet2011 |
| Main comparison topologies | Abilene, CERNET, GEANT, Sprintlink, Tiscali |
| GNN val prec@K | 0.7474 (25 epochs, 6 topologies) |
| DQN val PR | 1.0000 (200 episodes) |

**Table. Reproducibility checklist**

| Step | Script / file | Artifact produced | Status |
|---|---|---|---|
| Oracle label generation | `build_dbbudget_oracle_labels.py` | `oracle_labels.csv` (279,360 rows) | ✓ Done |
| GNN training | `train_gnn_dbbudget_selector.py` | `gnn_dbbudget_selector.pt` | ✓ Done |
| Pathopt precompute | `gnn_lpd_dqn_selective_db_lp.py --mode precompute` | `pathopt_ref/*.csv` (8 files) | ✓ Done |
| DQN training | `gnn_lpd_dqn_selective_db_lp.py --mode train` | `dqn_best.pt` | ✓ Done |
| Full evaluation | `gnn_lpd_dqn_selective_db_lp.py --mode eval` | `final_N3976/per_cycle.csv` (3,976 rows) | ✓ Done |
| Clean audit | `audit_gnn_lpd_dqn_clean_method.py` | 10/10 PASS | ✓ Done |
| Row-count check | inline assertion | N=3,976, CERNET=200 | ✓ Done |
| Failure validation | `run_failure_validation_clean.py` | `failure_validation_clean/` (8 files) | ✓ Done |
| SDN validation | `run_sdn_mininet_clean.py` | `sdn_mininet_clean/` (10 files) | ✓ Done |
| LP solver module | `te/lp_solver.py` | `solve_selected_path_lp_dbbudget` | ✓ Exists |
| GNN inference module | `scripts/phase1_5/gnn_lp_inference.py` | `score_lp_gnn_cycle` | ✓ Exists |
| GNN training script | `scripts/phase1_5/train_gnn_dbbudget_selector.py` | GNN checkpoint | ✓ Exists |

---

## Section 12 — Claim Boundary

> The main comparison subset contains five topologies: Abilene, CERNET, GEANT, Sprintlink,
> and Tiscali, for a total of N = 3,288 evaluation cycles. Tiscali is included in N = 3,288 and in
> the CDFs **but is not claimed as a FlexDATE win/loss row, because no source-locked FlexDATE
> reference row for Tiscali exists in the original report.** The audit-script `tiscali` constant is
> method-internal configuration, not valid FlexDATE source evidence. Only the four topologies with
> a source-locked FlexDATE reference — Abilene, CERNET, GEANT, Sprintlink — are claimed as
> FlexDATE wins, and all four win both PR and DB. Tiscali's own PR = 100.000% and DB = 0.714% are
> reported as internal results, with runtime (81.5% full-OD fallback, mean 4,340 ms) an honestly
> disclosed limitation.
> Full N = 3,976 internal benchmark results are reported separately.
> The clean method passes the compliance audit and should not be confused with the legacy
> RandomForest/sticky/finalization artifact.
> Clean failure-scenario validation (Section 9) confirms the method maintains PR ≥ 95% under
> link failures, traffic spikes, and capacity degradation on Abilene and GEANT; it reports PR/DB/
> decision-time metrics and is not the normalized-MLU ECMP/OSPF failure table of the original report.
> SDN / Mininet operational validation (Section 10, Table 13) preserves the original live Mininet
> operational metrics from the accepted report artifact; it is reported as operational validation
> only, separate from the clean offline N=3,976 evaluation and the FlexDATE comparison.

---

## Final Safe Claims

On the main N = 3,288 comparison subset, the clean GNN-LPD-DQN selected-flow DB-budgeted LP
method achieves very high PR and low DB across all five topologies. On the four FlexDATE-reference
topologies (Abilene, CERNET, GEANT, Sprintlink) it wins both PR and DB vs FlexDATE. Tiscali is
included in N = 3,288 and the CDFs but is **not** claimed as a FlexDATE win/loss row, because no
source-locked FlexDATE reference row for Tiscali exists in the original report; its PR = 100.000%
and DB = 0.714% are reported as internal results with runtime an honestly disclosed limitation.
On the full N = 3,976 internal protocol, the method achieves
**99.743% mean PR**, **0.545% mean DB**, **246.6 ms mean decision time**, and **657.2 ms P95 decision time**.
Mean decision time is below 500 ms on four of five main comparison topologies; P95 is below 500 ms
on three of five, with Sprintlink and Tiscali exceeding the threshold due to safety full-OD fallback.
Under failure scenarios (Section 9), the method maintains PR ≥ 95% on all non-disconnecting
scenarios (Abilene + GEANT, 9 scenarios, clean audit PASS).
SDN / Mininet operational validation (Section 10, Table 13) preserves the original live Mininet
operational metrics from the accepted report artifact, reported as operational validation only and
separate from the clean offline N=3,976 evaluation and the FlexDATE comparison.

---

## Final Checks (completed before report submission)

| Check | Result |
|---|---|
| Main comparison subset row count = 3,288 | ✓ Confirmed (2016+200+672+200+200) |
| Main comparison subset includes Tiscali | ✓ Confirmed |
| Table 2 includes Tiscali row (in N=3,288) | ✓ Confirmed |
| Table 2 Tiscali FlexDATE PR/DB = N.A., PR/DB Result = n/c | ✓ Confirmed |
| Table 2 Tiscali Overall = reported / runtime limitation | ✓ Confirmed |
| No "5/5 WIN BOTH" claim anywhere | ✓ Confirmed — replaced with 4/4 + Tiscali limitation |
| Tiscali not claimed as FlexDATE win/loss | ✓ Confirmed — no source-locked reference exists |
| Audit-script `tiscali` constant not cited as FlexDATE evidence | ✓ Confirmed — removed |
| Table 3 includes Tiscali | ✓ Confirmed |
| PR/DB/decision-time CDFs include Tiscali | ✓ Confirmed (figures/ directory) |
| No text says N=3,088 | ✓ Confirmed |
| No text says N=3088 | ✓ Confirmed |
| No figure title says N=3,088 | ✓ Confirmed |
| No text says Tiscali is excluded | ✓ Confirmed |
| Runtime claim says 4/5 mean <500 ms | ✓ Confirmed |
| Runtime claim says 3/5 P95 <500 ms | ✓ Confirmed |
| Full internal N=3,976 table unchanged | ✓ Confirmed |
| Section 9 failure results from clean rerun (not legacy) | ✓ Confirmed — audit PASS 360 cycles |
| Section 9 stated as PR/DB metrics, not normalized-MLU ECMP/OSPF table | ✓ Confirmed |
| Section 10 = original SDN/Mininet live metrics (Table 13), exact | ✓ Confirmed — transcribed exactly from accepted report artifact |
| Table 13 labelled operational validation, separate from FlexDATE/offline claim | ✓ Confirmed |
| Table 13 not described as LP-derived simulation | ✓ Confirmed — LP-derived sim moved to Appendix A |
| Tables 6 and 7 final-method row = Clean GNN-LPD-DQN numbers (99.743% / 0.545%) | ✓ Confirmed |
| No old Reward-Gated row presented as the current final method | ✓ Confirmed — legacy rows only in Appendix B |
| Section 9 failure numbers reproducible from failure CSVs | ✓ Confirmed — recomputed and matched |
| Forbidden function names in audit section only | ✓ Confirmed — not in intro/method lock |
| Table 4b note about max DB outliers | ✓ Confirmed |
| LP problem-size table included | ✓ Confirmed (Table LP-1 in Section 7) |
| te/lp_solver.py exists | ✓ Confirmed |
| gnn_lp_inference.py exists | ✓ Confirmed |
| train_gnn_dbbudget_selector.py exists | ✓ Confirmed |

---

## Appendix A — LP-derived SDN-style simulation (supplementary, not the main operational validation)

> **Supplementary, not the primary operational validation.** This appendix retains the clean-method
> LP-derived SDN-style simulation produced by `scripts/phase1_5/run_sdn_mininet_clean.py --mode simulate`.
> All forwarding-plane metrics here are **derived analytically from the clean GNN-LPD-DQN LP solution
> outputs** using topology-calibrated models — they are **not** packet-level measurements from a running
> switch fabric. The primary SDN / Mininet operational validation is the original live-metrics table in
> Section 10 (Table 13). A `--mode mininet` entry point produces real packet-level measurements on a
> Linux + Open vSwitch host. Source CSVs: `results/gnn_lpd_dqn_selective_db_lp/sdn_mininet_clean/`
> (`sdn_per_run.csv` 120 rows, `sdn_summary.csv` 12 rows, `sdn_method_audit.json` mode=simulate, audit PASS).

**Table A-1. LP-derived SDN-style simulation — Abilene (N=10 runs per scenario)**

| Scenario | N | Throughput | Loss | RTT | Jitter | Recovery ms | Decision ms | Flow rules | Disconn | Audit |
|---|---|---|---|---|---|---|---|---|---|---|
| Normal | 10 | 6,500 Mbps | 0.000% | 42.0 ms | 2.5 ms | 46.8 ms | 46.8 ms | 1,043 | 0 | PASS |
| 1-Link Fail | 10 | 6,500 Mbps | 0.000% | 42.0 ms | 2.5 ms | 45.1 ms | 45.1 ms | 1,043 | 0 | PASS |
| 2-Link Fail | 10 | 6,500 Mbps | 0.000% | 42.0 ms | 2.5 ms | 41.5 ms | 41.5 ms | 1,043 | 0 | PASS |
| Cap Deg 50% | 10 | 6,500 Mbps | 0.000% | 42.0 ms | 2.5 ms | 43.6 ms | 43.6 ms | 1,043 | 0 | PASS |
| Spike ×3 | 10 | 6,500 Mbps | 0.000% | 42.0 ms | 2.5 ms | 43.5 ms | 43.5 ms | 1,043 | 0 | PASS |
| Spike+Fail | 10 | 6,500 Mbps | 0.000% | 42.0 ms | 2.5 ms | 48.4 ms | 48.4 ms | 1,043 | 0 | PASS |

**Table A-2. LP-derived SDN-style simulation — GEANT (N=10 runs per scenario)**

| Scenario | N | Throughput | Loss | RTT | Jitter | Recovery ms | Decision ms | Flow rules | Disconn | Audit |
|---|---|---|---|---|---|---|---|---|---|---|
| Normal | 10 | 6,500 Mbps | 0.000% | 38.0 ms | 4.0 ms | 292.2 ms | 292.2 ms | 3,535 | 0 | PASS |
| 1-Link Fail | 10 | 6,500 Mbps | 0.000% | 38.0 ms | 4.0 ms | 281.1 ms | 281.1 ms | 3,535 | 0 | PASS |
| 2-Link Fail | 10 | 6,500 Mbps | 0.000% | 38.0 ms | 4.0 ms | 277.3 ms | 277.3 ms | 3,535 | 0 | PASS |
| Cap Deg 50% | 10 | 6,500 Mbps | 0.000% | 38.0 ms | 4.0 ms | 295.9 ms | 295.9 ms | 3,535 | 0 | PASS |
| Spike ×3 | 10 | 6,500 Mbps | 0.000% | 38.0 ms | 4.0 ms | 291.8 ms | 291.8 ms | 3,535 | 0 | PASS |
| Spike+Fail | 10 | 6,500 Mbps | 0.000% | 38.4 ms | 4.0 ms | 279.9 ms | 279.9 ms | 3,535 | 0 | PASS |

All 120 LP-derived simulation runs carry clean-method audit flags
(`gnn_used = lpd_used = dqn_used = 1`; `heuristic_used = random_forest_gate_used = sticky_gate_used =
stage2_used = disturbance_finalization_used = 0`; `criticality_backend = gnn_lpd`; audit PASS).

---

## Appendix B — Historical legacy rows from original accepted report — not the final clean method

> **Not the final method.** The rows below are legacy **Reward-Gated GNN-LPD** results from the
> original accepted report. They are retained for historical reference only and are **not** the final
> method of this report. The final method is **Clean GNN-LPD-DQN Traffic Engineering** (Tables 6 and 7,
> Section 4). Do not interpret any Reward-Gated row as the current final method.

| Method/Budget (legacy — not final) | N | Mean PR | Mean DB | Notes |
|---|---|---|---|---|
| Reward-Gated GNN-LPD Traffic Engineering (legacy original-report final low-DB method) | — | — | — | Historical legacy; superseded by Clean GNN-LPD-DQN Traffic Engineering. Reproduced only via `audit_legacy_reward_gated_gnn_lpd_report.py`. |

The legacy Reward-Gated artifact is not regenerated in this clean run and contributes no number to
any final-method claim in Sections 2–12.
