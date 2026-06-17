# PLAN_PHASE15 — Phase 1.5 Repair and Completion

**Date:** 2026-06-17  
**Branch:** `claude/nervous-napier-e49208`  
**Repo:** `moahaimen/s_network_modified`

---

## SEPARATION RULE (never violate)

| Mode | Purpose | Forbidden to claim |
|---|---|---|
| **Part A** — `legacy_reward_gated_gnn_lpd_report_reproduction` | Forensic only; explains what produced the accepted report | "This is the final clean method" |
| **Part B** — `gnn_lpd_dqn_selective_db_lp` | Final professor-compliant method | "This reproduces the legacy report" |

**Do not mix outputs, numbers, or code between the two modes.**

---

## Critical finding (gate before any execution)

The existing checkpoint `gnn_lp_distilled_selector.pt` was trained with:
- Teacher: `solve_full_mcf_min_mlu` (min-MLU, not DB-budgeted)
- Oracle selection seed: **heuristic bottleneck ranking** (`bottleneck_ranking[:K]`)

This does **not** satisfy the clean-method spec which requires labels from the
*frozen full-OD DB-budgeted LP oracle*. **Option A is mandatory.**

---

## Status at plan writing

| Item | Status |
|---|---|
| Part A scripts + audit | ✅ DONE, audit passes (Δ=0.00000) |
| `te/lp_solver.py` upgraded | ✅ DONE |
| `diverse_paths.py` ported | ✅ DONE |
| `gnn_lp_inference.py` ported | ✅ DONE |
| Part B script skeleton | ✅ DONE (K20→K320 action space — to be replaced) |
| Data symlinks in worktree | ✅ DONE |
| DB-budgeted oracle labels | ❌ NOT DONE |
| GNN retrained from oracle | ❌ NOT DONE |
| Pathopt references | ❌ NOT DONE |
| DQN trained | ❌ NOT DONE |
| Evaluation N=3976 | ❌ NOT DONE |
| Clean audit | ❌ NOT DONE |
| Final report | ❌ NOT DONE |

---

## Phase 0 — DB-budgeted oracle label generation + GNN training (GATE)

### Step 0a — Build oracle labels

**Script:** `scripts/phase1_5/build_dbbudget_oracle_labels.py`

Oracle: `solve_selected_path_lp_dbbudget(all_active_ods, db_budget=0.10)`
- Run for all training topologies × training cycles
- For each OD: `reroute_mass = demand[i] * split_distance(lp_split[i], ecmp[i])`
- `label_useful = 1` if OD is in top-50% by reroute_mass among active ODs
- `oracle_score = reroute_mass / max_reroute_mass` (soft label in [0, 1])

**Output:**
```
results/gnn_lpd_dqn_selective_db_lp/labels/
  oracle_labels.csv           # topology, timestep, od_id, demand, reroute_mass, label_useful, oracle_score
  label_provenance.json       # oracle_solver, heuristic_ranking_used=false, etc.
```

**Gate:** `label_provenance.json` must contain:
```json
{
  "oracle_solver": "full_od_db_budgeted_lp",
  "heuristic_ranking_used_for_labels": false,
  "full_mcf_min_mlu_teacher_only": false,
  "db_budgeted_oracle_used": true
}
```

### Step 0b — Train GNN from oracle labels

**Script:** `scripts/phase1_5/train_gnn_dbbudget_selector.py`

- Architecture: `GNNFlowSelector` (node_dim=16, edge_dim=8, od_dim=10, hidden_dim=64, layers=3)
- Loss: 0.7 × ranking_loss + 0.3 × BCE on `label_useful`
- Training: ~20 epochs, patience=5, lr=3e-4
- Checkpoint: `results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector.pt`
- Meta: `results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector_meta.json`

**Hard rule:** If training fails, stop. Do NOT fall back to `gnn_lp_distilled_selector.pt`.

---

## Phase 1 — Precompute PR references

```bash
python scripts/phase1_5/gnn_lpd_dqn_selective_db_lp.py --mode precompute
```
- Writes `results/gnn_lpd_dqn_selective_db_lp/pathopt_ref/pathopt_<topo>.csv`
- Gate: strict-MCF refs resolve for abilene/cernet/geant/sprintlink with `status=Optimal`

---

## Phase 2 — Train DQN

```bash
python scripts/phase1_5/gnn_lpd_dqn_selective_db_lp.py --mode train \
  --gnn_checkpoint results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector.pt \
  --episodes 200
```
- Gate: `dqn_best.pt` written; val PR climbing; script hard-crashes if GNN fails (no fallback)

---

## Phase 3 — Evaluate N=3976

```bash
python scripts/phase1_5/gnn_lpd_dqn_selective_db_lp.py --mode eval \
  --gnn_checkpoint results/gnn_lpd_dqn_selective_db_lp/models/gnn_dbbudget_selector.pt \
  --dqn_checkpoint results/gnn_lpd_dqn_selective_db_lp/dqn_best.pt \
  --tag final_N3976
```
- Gate (hard): `gnn_usage_rate == 1.0`, `lpd_usage_rate == 1.0`
- Gate: `stage2_used=0, random_forest_gate_used=0, sticky_gate_used=0, heuristic_used=0`

---

## Phase 4 — Clean audit

```bash
python scripts/phase1_5/audit_gnn_lpd_dqn_clean_method.py
```
- Validates per-cycle CSV has `gnn_used=1` in every row
- Static grep for forbidden tokens in the method script itself
- Gate: exits 0 only if all clean-method audit fields match spec

---

## Phase 5 — Final report

`results/gnn_lpd_dqn_selective_db_lp/FINAL_REPORT.md`:
- Per-topology PR/DB/decision-time table vs FlexDATE
- Win/tie flags on Abilene, CERNET, GEANT, Sprintlink
- Tiscali reported as limitation if PR < target
- Must state: "This is the clean professor-compliant method. Numbers come from method's own audit, not copied from legacy report."

---

## Clean action space (Part B)

| Action ID | Name | K | DB budget |
|---|---|---|---|
| 0 | KEEP_PREVIOUS_ROUTING | — | — |
| 1 | OPTIMIZE_K30_DB_0.01 | 30 | 0.01 |
| 2 | OPTIMIZE_K30_DB_0.03 | 30 | 0.03 |
| 3 | OPTIMIZE_K40_DB_0.01 | 40 | 0.01 |
| 4 | OPTIMIZE_K40_DB_0.03 | 40 | 0.03 |
| 5 | OPTIMIZE_K50_DB_0.01 | 50 | 0.01 |
| 6 | OPTIMIZE_K50_DB_0.03 | 50 | 0.03 |
| 7 | FULL_OD_FALLBACK_PR_SAFE | all | 0.05 |
| 8 | FULL_OD_FALLBACK_LOW_MLU | all | 1.0 |

K escalation ladder: 30 → 40 → 50 → full-OD fallback.  
No sticky. No REOPTIMIZE names. No RandomForest.

---

## Hard rules

- Never import/call: `RandomForest`, `sticky`, `Stage-2`, `solve_selected_path_lp_min_db`, heuristic OD ranking
- Never edit `reward_policy_selector_st_lam005/` — read-only
- If GNN won't load at eval → fail loudly, do not fall back
- `results/legacy_reward_gated_gnn_lpd_report_reproduction/` and `results/gnn_lpd_dqn_selective_db_lp/` must remain disjoint
- Do not present legacy numbers as the clean method's output
