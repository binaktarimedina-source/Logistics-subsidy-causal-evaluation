# Logistics Subsidy Causal Evaluation
### Comparing RCT, DiD and RDD on Simulated Data

## Motivation

Program evaluation courses teach RCT, Difference-in-Differences, and
Regression Discontinuity as separate tools. In practice, a single policy
often gets evaluated with *different* designs at *different* stages of its
rollout — a small randomized pilot, then a staggered regional expansion,
then a national rollout with an eligibility cutoff.

This project simulates exactly that lifecycle for a hypothetical policy: a
subsidy that helps small exporting firms upgrade logistics capacity
(warehousing, cold-chain, customs-clearance technology). Because the data
is simulated, the **true causal effect is known** (a 0.18 log-point increase
in exports, ≈ +19.7%), so each method's estimate can be checked against
ground truth — something impossible with real-world data.


## The three designs

| Phase | Design | Identifying assumption | Estimate | 95% CI |
|---|---|---|---|---|
| 1 — Pilot | RCT | Random assignment (no confounding) | **0.336** | [0.095, 0.578] |
| 2 — Regional rollout | DiD (TWFE) | Parallel trends absent treatment | **0.167** | [-0.007, 0.342] |
| 3 — National rollout | RDD (local linear) | No manipulation/smoothness at cutoff | **0.251** | [0.083, 0.418] |

**True simulated effect: 0.18.** All three 95% CIs contain the truth; DiD's
point estimate lands closest, while RCT and RDD are noisier here simply
because of smaller effective sample sizes near the identifying variation
(pilot size, and the RDD bandwidth window) — a useful, realistic reminder
that "more assumption-free" doesn't automatically mean "more precise."

## What each script actually checks

- **RCT**: a balance check confirms `baseline_size` is uncorrelated with
  treatment (p = 0.31) — evidence the randomization worked — before taking
  the simple mean-difference estimate at face value.
- **DiD**: an event-study-style plot of mean log(exports) by region group
  and period is used to visually inspect the parallel-trends assumption
  before the policy start.
- **RDD**: a binned scatterplot with separate local-linear fits on each side
  of the cutoff shows the discontinuity directly; the bandwidth is a simple
  fixed window (±15) chosen for transparency rather than an automated
  bandwidth selector.

## How to reproduce (Google Colab or local)

```bash
pip install pandas numpy statsmodels matplotlib scipy
python data/generate_data.py
python analysis/01_rct_analysis.py
python analysis/02_did_analysis.py
python analysis/03_rdd_analysis.py
python analysis/04_comparison.py
```

## Notes / limitations

- All data is synthetic; no real firms, regions, or government program are
  represented.
- The RDD bandwidth and DiD fixed-effects specification are kept simple on
  purpose, for readability — a natural "next step" for discussion is
  bias-variance tradeoffs in bandwidth choice, or robustness checks with
  alternative bandwidths / placebo cutoffs.

## Skills demonstrated

- Causal inference: RCT, Difference-in-Differences (two-way fixed effects),
  Regression Discontinuity Design (local linear regression)
- Data simulation with known ground truth for method validation
- Python: pandas, numpy, statsmodels, matplotlib
