# Preregistration: H3 eta constant (E1.3)

**Fixed date**: 2026-10-20 (results unseen)
**Hypothesis ID**: H3
**Status**: v0.2 draft. Supersedes `prereg_H3_eta.md` (v0.1, kept for history).

## Purpose

H3-main: test whether fixing Kaczmarz's relaxation η to constants {1.0, 0.5, 0.125} is equivalent to learned η_learned, using TOST.

H3-sub: test whether the substitutability of keynorm Kaczmarz by L2-const depends on budget, using non-inferiority.

## Design

- Base cell: V=300, n_pairs=16, n_dist=32, d_mem ∈ {16, 32}, seed ∈ {0,1,2,3,4}.
- η_const ∈ {1.0, 0.5, 0.125}: 3 levels + η_learned = 4 conditions.
- Total steps: same as E1.1 (1500).
- η_learned is a sigmoid output (as in GDN). η_const is fixed.
- learned-mean is fixed from **seed 0 only** (to prevent information leakage to other seeds).

## Primary endpoint

- Each η_const level vs η_learned: **paired TOST, δ=0.01** (90% CI within ±0.01).
- Primary decision is a **conjunction (AND)**: H3 holds only if all three levels pass TOST.
  - The conjunction is an intersection-union test, so Holm is not applied to the primary.
  - Holm-adjusted p-values are reported as a **conservative sensitivity analysis** (not the primary criterion).
- Additional guard (descriptive): the within-run SD of η_t in the learned run must be smaller than the smallest level gap, 0.125.
  - If this guard fails, H3's claim is downgraded to "accuracy equivalence only".

## Secondary

- post-hoc swap (evaluating the learned mean as a constant) is **secondary evidence**. The primary evidence is the retrained arm.
- Report per level: acc, TOST p, 90% CI, and η_t SD.

## Statistics

- paired TOST (`stats.tost`).
- Conjunction decision. Holm is sensitivity (`stats.holm`).
- δ=0.01 is fixed before the main runs. If pilot SD motivates a δ change, it may be amended only before the main runs, with the reason and value recorded (pilot data are not used in the primary test).

## Interpretation limits

- "All three levels equivalent" is a claim within δ=0.01. It does not claim the absence of differences larger than δ.
- The η_t SD guard is descriptive, not a formal test.
- Small-scale MQAR only.

## H3-sub: keynorm vs L2, budget dependence (redefined, 2026-10-20, before main run)

**Purpose**: test whether substitutability of keynorm Kaczmarz by L2-const depends on budget.

**Exclusion**: normbeta η=0.125 vs L2-const is structurally identical (normalized k, β≈η=0.125), so H3-sub excludes it. The smoke test also agreed (both 0.1030).

### Design (20 new runs; the 1500 side is extracted from E1.1)

- arm: **keynorm Kaczmarz** (Stage 1 frozen) vs **L2-const β=0.125**.
- d_mem: {16, 32}.
- steps: {1500, 3000}.
- **1500-step side**: subset of E1.1 (d_mem {16,32}, n_pairs=16), **0 new runs, 10 seeds**.
- **3000-step side**: 20 new runs (2 arms × 2 d_mem × 5 seeds, seeds 0–4).
- Interaction `Δ_1500 − Δ_3000` is **paired on seeds 0–4** (the 1500 side uses seeds 0–4 of its 10 seeds).
- Measured: 150 sec/run (1500 step, n=16, CPU). 3000 step is about 2x. The 20 new runs take about 25 min at 4-way parallelism.
- **Generalization limit**: the non-inferiority claim is limited to d_mem {16, 32}. Generalization to d≥64 is open. E1.1 also runs d=64/128, but they are not included in H3-sub's NI.

### Primary endpoint (per budget)

- Non-inferiority: `acc(L) − acc(K) > −0.02` by a one-sided 95% lower bound (is L2 worse than K by at least δ=0.02?).
- Prediction:
  - **1500 step**: non-inferiority fails (K > L; L2 is worse by at least δ). The gate helps at low budget.
  - **3000 step**: non-inferiority holds (K ≈ L; L2 is not worse by at least δ). At high budget, L2 can substitute.
- TOST is not used (this is not an equivalence claim).

### Secondary endpoint

- Budget × arm interaction: `Δ_1500 − Δ_3000 > 0` (Δ = acc(K) − acc(L), the gap shrinks) by a one-sided test.
- Per d_mem is descriptive.

### Paper claim (updated)

> KPL's Kaczmarz gate is an optimization for low budgets, and at 3000 steps or more L2 with fixed β can substitute. This is consistent with the LM flatness in 2507.06457 and positions KPL as a design principle under budget constraints, not as a universal component.

### Exclusion record

- normbeta η=0.125 vs L2-const is structurally identical (normalized k, β≈η=0.125). The 20-run comparison would have zero power, so it is not run.

## Result fields (blank, frozen)

- η=1.0 vs learned: Δ, 90% CI, TOST p
- η=0.5 vs learned: Δ, 90% CI, TOST p
- η=0.125 vs learned: Δ, 90% CI, TOST p
- Conjunction decision:
- η_t SD guard:
- H3-sub 1500 (E1.1 subset, 10 seeds): Δ=acc(K)−acc(L), NI lower95, pass/fail
- H3-sub 3000 (20 new runs, seeds 0–4): Δ=acc(K)−acc(L), NI lower95, pass/fail
- Interaction Δ_1500 − Δ_3000 (seeds 0–4): point estimate, one-sided p
- Decision:
