# Phase 1.5 Clean GNN-LPD-DQN Traffic Engineering Report

**Subtitle:** Main comparison subset N = 3,288 including Tiscali; full internal protocol N = 3,976 reported separately

---

> **Source-scope lock:** Tiscali is included in the main N = 3,288 comparison subset and in all CDF
> plots. Where a locked FlexDATE reference value is unavailable or inconsistent, the Tiscali row is
> reported as an internal comparison/limitation row rather than removed.
> Full N = 3,976 internal benchmark results are reported separately and are not used to establish
> the main comparison claim.

> **Method lock:** The final clean method is named **Clean GNN-LPD-DQN Traffic Engineering**.
> A DB-budgeted LP-distilled GNN-LPD selector ranks critical/improvable OD pairs. A DQN controller
> chooses the routing action and DB budget. A one-stage selected-flow DB-budgeted LP computes
> routing for selected ODs, while noncritical ODs remain on ECMP or previous routing. The reported
> clean run does not use heuristic criticality, RandomForest gate, sticky gate, Stage-2 DB LP, or
> disturbance-finalization LP.

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
- No `solve_selected_path_lp_min_db(...)`

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
GEANT, Sprintlink, and Tiscali. A locked FlexDATE reference for Tiscali is not available in the
locked source material; its FlexDATE columns are marked N.A. and its result is reported as an
internal/limitation row.

**Table 2. Main comparison subset including Tiscali (N = 3,288)**

| Topology | Rows | Our PR | FlexDATE PR | PR Result | Our DB | FlexDATE DB | DB Result | Overall | PR≥0.95 | Min PR | P95 ms |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Abilene | 2,016 | 99.669% | 95.800% | WIN | 0.613% | 5.130% | WIN | WIN | 100.000% | 96.038% | 7.2 |
| CERNET | 200 | 99.420% | 97.500% | WIN | 0.321% | 1.830% | WIN | WIN | 100.000% | 97.721% | 29.0 |
| GEANT | 672 | 99.984% | 99.500% | WIN | 0.378% | 2.960% | WIN | WIN | 100.000% | 99.621% | 15.3 |
| Sprintlink | 200 | 100.000% | 99.900% | WIN | 0.471% | 5.100% | WIN | WIN | 100.000% | 100.000% | 1,082.2 |
| Tiscali | 200 | 100.000% | N.A. | n/c | 0.714% | N.A. | n/c | reported / limitation | 100.000% | 99.950% | 9,255.5 |
| **Weighted (N=3,288)** | **3,288** | **99.758%** | — | — | **0.544%** | — | — | **4/4 WIN + 1 limitation** | **100.000%** | **96.038%** | **1,252.4** |

> Tiscali: No consistent locked FlexDATE reference row exists for Tiscali. Its result is reported
> honestly as an internal evaluation with high PR (100.000%) and moderate DB (0.714%), but with a
> severe runtime limitation: 81.5% full-OD fallback, mean 4,340 ms, P95 9,255 ms.
>
> The four topologies with locked FlexDATE references (Abilene, CERNET, GEANT, Sprintlink) all
> achieve PR and DB wins. Sprintlink P95 exceeds 500 ms due to safety full-OD fallback on 11.5%
> of cycles; its mean decision time is 155.5 ms.

---

## Section 3 — Final-Method Summary (Main Subset N = 3,288)

**Table 3. Final-method summary on the main comparison subset (N = 3,288)**

| Topology | N | Mean PR | PR≥0.95 | PR≥0.90 | Min PR | Mean DB | P95 DB | P50 ms | P95 ms | Full-OD% | Scope |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Abilene | 2,016 | 99.669% | 100.000% | 100.000% | 96.038% | 0.613% | 1.111% | 6.7 | 7.2 | 2.3% | Main subset |
| CERNET | 200 | 99.420% | 100.000% | 100.000% | 97.721% | 0.321% | 0.184% | 25.7 | 29.0 | 1.5% | Main subset |
| GEANT | 672 | 99.984% | 100.000% | 100.000% | 99.621% | 0.378% | 0.750% | 13.6 | 15.3 | 3.1% | Main subset |
| Sprintlink | 200 | 100.000% | 100.000% | 100.000% | 100.000% | 0.471% | 0.290% | 32.2 | 1,082.2 | 11.5% | Main subset |
| Tiscali | 200 | 100.000% | 100.000% | 100.000% | 99.950% | 0.714% | 1.212% | 4,297.6 | 9,255.5 | 81.5% | Main subset / limitation |
| **Weighted (N=3,288)** | **3,288** | **99.758%** | **100.000%** | **100.000%** | **96.038%** | **0.544%** | **1.000%** | **6.9** | **1,252.4** | **7.8%** | **Main subset pooled** |

> Weighted pooled rows computed directly from `per_cycle.csv` filtered to the five main-subset
> topologies (N=3,288), not by averaging table rows. P95 ms of 1,252.4 ms is dominated by
> Tiscali's high full-OD fallback rate.

---

## Section 4 — Full Internal Evaluation

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
> selected-flow DB-budgeted LP run.

---

## Section 9 — Operational Validation

Operational SDN/Mininet validation was not regenerated for this clean-method rerun.
The previous operational validation belongs to the legacy report artifact and is therefore
not reused as evidence for the clean method.

---

## Section 10 — Reproducibility Metadata

| Parameter | Value |
|---|---|
| Git commit (local working tree) | `b98caba` |
| GitHub push commit | `5858d88` → `github.com/moahaimen/final_trafic` main |
| Main method script | `scripts/phase1_5/gnn_lpd_dqn_selective_db_lp.py` |
| Label script | `scripts/phase1_5/build_dbbudget_oracle_labels.py` |
| Audit script | `scripts/phase1_5/audit_gnn_lpd_dqn_clean_method.py` |
| GNN checkpoint | `results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector.pt` |
| DQN checkpoint | `results/gnn_lpd_dqn_selective_db_lp/dqn_best.pt` |
| Label file | `results/gnn_lpd_dqn_selective_db_lp/labels/oracle_labels.csv` |
| Total label rows | 279,360 |
| CERNET label rows | 65,600 (40 cycles × 1,640 ODs, all LP-Optimal) |
| Evaluation per-cycle file | `results/gnn_lpd_dqn_selective_db_lp/final_N3976/per_cycle.csv` |
| Evaluation rows | 3,976 |
| Main comparison subset | 3,288 (Abilene, CERNET, GEANT, Sprintlink, Tiscali) |
| Clean audit result | PASSED — 10/10 blocks |
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

---

## Section 11 — Claim Boundary

> The main comparison subset contains five topologies: Abilene, CERNET, GEANT, Sprintlink,
> and Tiscali, for a total of N = 3,288 evaluation cycles. Tiscali is included in the reported
> tables and CDFs. A locked FlexDATE reference for Tiscali is unavailable; its row is reported
> as an internal comparison/limitation row rather than removed.
> Full N = 3,976 internal benchmark results are reported separately.
> The clean method passes the compliance audit and should not be confused with the legacy
> RandomForest/sticky/finalization artifact.

---

## Final Safe Claims

On the main N = 3,288 comparison subset including Tiscali, the clean GNN-LPD-DQN selected-flow
DB-budgeted LP method achieves very high PR and low DB across all five topologies. It wins both
PR and DB on the four topologies with locked FlexDATE references (Abilene, CERNET, GEANT,
Sprintlink), and reports Tiscali explicitly as a limitation/runtime-heavy case rather than
excluding it. On the full N = 3,976 internal protocol, the method achieves **99.743% mean PR**,
**0.545% mean DB**, **246.6 ms mean decision time**, and **657.2 ms P95 decision time**.
Mean decision time is below 500 ms on four of five main comparison topologies; P95 is below 500 ms
on three of five, with Sprintlink and Tiscali exceeding the threshold due to safety full-OD fallback.

---

## Final Checks (completed before report submission)

| Check | Result |
|---|---|
| Main comparison subset row count = 3,288 | ✓ Confirmed (2016+200+672+200+200) |
| Main comparison subset includes Tiscali | ✓ Confirmed |
| Table 2 includes Tiscali | ✓ Confirmed |
| Table 3 includes Tiscali | ✓ Confirmed |
| PR/DB/decision-time CDFs include Tiscali | ✓ Confirmed (figures/ directory) |
| No figure title says N=3,088 | ✓ Confirmed |
| No text says Tiscali is excluded | ✓ Confirmed |
| Runtime claim says 4/5 mean <500 ms and 3/5 P95 <500 ms | ✓ Confirmed |
| Tiscali FlexDATE reference marked N.A. (not silently removed) | ✓ Confirmed |
| Full internal N=3,976 table unchanged | ✓ Confirmed |
