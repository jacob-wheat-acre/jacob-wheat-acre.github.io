---
title: "Motor Flicker Study Tool"
date: 2026-02-23
draft: false
summary: "A deep technical walkthrough of a motor-start voltage-change model, including equations, assumptions, sensitivities, and validation limits."
---

## Overview
This project is an engineering screening tool for motor-start voltage-change risk on distribution systems.
The goal is to estimate rapid voltage change at the point of common coupling and determine whether a proposed application is likely to violate flicker/voltage-step criteria.

This is intended for fast planning decisions:
- early-stage feasibility checks
- comparing mitigations (conductor upgrades, transformer size, VFD vs ATL, capacitor placement)
- triaging projects that need detailed utility study

It is not intended to replace a full utility planning/protection study.

## Problem Statement
Given:
- motor size and starting method
- upstream short-circuit/Thevenin source strength
- transformer size/impedance
- feeder conductor and length (including mixed segments)
- capacitor bank near the load

Estimate:
- event voltage-change magnitude `abs_deltaV_percent`
- margin to utility screening thresholds
- whether the case is likely acceptable, review-level, or likely violating

## Modeling Framework
The model uses a per-phase Thevenin equivalent:

`Z_th = Z_upstream + Z_xfmr + Z_line`

where:
- `Z_upstream` is user-supplied upstream Thevenin impedance (MV side, ohm/phase)
- `Z_xfmr` is derived from transformer nameplate `percent_Z` and `X_over_R`
- `Z_line` is accumulated from feeder segments

`V_th_ph = V_LL / sqrt(3)`

The bus solution is:

`V_load = V_th - Z_th * I_total`

Reported voltage change:

`deltaV_percent = 100 * (1 - abs(V_load)/abs(V_th))`

Screening metric:

`abs_deltaV_percent = abs(deltaV_percent)`

The model classifies by `abs_deltaV_percent`, not signed sag alone, since rapid voltage rise can also be problematic.

## Detailed Equations

### 1) Upstream + Transformer + Line Impedance

#### Upstream
User-entered:
`Z_upstream = R_upstream + jX_upstream` (ohm/phase at MV)

This value is expected to come from a utility short-circuit/sequence-network tool (for example, CAPE-derived Thevenin equivalent).

#### Transformer
Given `V_LL`, `S_3ph`, `percent_Z`, and `X_over_R`:

- `Z_base = V_LL^2 / S_3ph`
- `abs(Z_xfmr) = (percent_Z/100) * Z_base`
- `R_xfmr = abs(Z_xfmr) / sqrt(1 + (X_over_R)^2)`
- `X_xfmr = R_xfmr * X_over_R`
- `Z_xfmr = R_xfmr + jX_xfmr`

#### Feeder Line
For mixed conductor segments:

`Z_line = sum_i( miles_i * (R_i + jX_i) )`

Conductor `R` and `X` are library values in ohm/mile.

### 2) Capacitor Bank Convention
Input capacitor is always **3-phase total kVAr**:

- `Q_cap_total_var = cap_kvar * 1000`
- `Q_cap_phase_var = Q_cap_total_var / 3`

Capacitor current at bus voltage:
`I_cap = +j * Q_cap_phase_var / conj(V)`

### 3) Start-Method Current Models

#### ATL / SoftStart
ATL uses locked-rotor current basis from NEMA code-letter kVA/HP band midpoint:
`S_LR_kVA = HP * midpoint(kVA_per_HP_band)`

Then:
`I_LRA_HV = (S_LR_kVA * 1000)/(sqrt(3)*V_HV_LL)`

Motor start current phasor:
`I_motor = k_start * I_LRA_HV * (pf - j*sqrt(1-pf^2))`

- ATL: `k_start = 1.0`
- SoftStart: `k_start < 1` (current implementation uses `0.5`)

With capacitor:
`I_total = I_motor + I_cap`

Voltage is solved iteratively:
`V_new = V_th - Z_th * I_total`

#### VFD
VFD start is represented as current-limited:

- `P_out = HP * 746`
- `FLA_LV = [P_out/(eff*pf_run)] / (sqrt(3)*V_LV_LL)`
- `I_lim_LV = vfd_i_limit_pu_fla * FLA_LV`
- `I_lim_HV = I_lim_LV * (V_LV_LL / V_HV_LL)`

At each iteration, motor current is constructed at bus angle with assumed displacement PF:

`I_motor = rect(I_lim_HV, angle(V)-phi)` where `phi = acos(pf_vfd)`

Then:
`I_total = I_motor + I_cap`
`V_new = V_th - Z_th * I_total`

This iterative phasor formulation avoids nonphysical artifacts from fixed-voltage approximations.

## Screening Heuristic
Limit is selected from event-frequency logic (`starts/hour`), then:

- `review_threshold = 0.8 * limit_threshold`
- `limit_threshold = allowable rapid voltage-change limit`

Classification:
- `OK` if `abs_deltaV_percent < review_threshold`
- `REVIEW` if `review_threshold <= abs_deltaV_percent < limit_threshold`
- `LIKELY VIOLATION` if `abs_deltaV_percent >= limit_threshold`

The tool also estimates:
- `Max HP (No Review)`
- `Max HP (Within Limit)`

for the same network assumptions.

## Inputs and Units (Important)
- Upstream `R`, `X`: ohm/phase (MV side)
- Conductor values: ohm/mile
- Segment lengths: miles
- Transformer: kVA, `percent_Z`, `X_over_R`
- Capacitor bank: **3-phase total kVAr**
- Motor size: HP
- Voltage levels: line-line volts

## Outputs
- Signed `deltaV_percent` (indicates sag vs rise)
- `abs_deltaV_percent` (screening metric)
- Classification (`OK`, `REVIEW`, `LIKELY VIOLATION`)
- Margin to limit
- Study plots (overview/compare/custom case)
- ASCII one-line summary for case documentation

## Assumptions
- Balanced three-phase operation
- Per-phase Thevenin reduction
- Fixed-frequency ATL/soft-start representation
- VFD represented by current limit plus assumed displacement PF
- No detailed harmonic network or control-loop transient model
- No explicit unbalance, fault, or time-domain EMT behavior

## Sensitivity and Dominant Drivers
In practice, `abs_deltaV_percent` is most sensitive to:
1. `abs(Z_th)` (upstream strength + transformer + line)
2. start current magnitude and PF (technology-dependent)
3. capacitor-bank size and local voltage interaction
4. motor size and event-frequency assumptions

Interpretation:
- stronger source / lower feeder impedance -> lower `abs_deltaV_percent`
- VFD current limit usually reduces `abs_deltaV_percent` vs ATL
- large capacitors can shift signed `deltaV_percent` toward rise; screening still uses `abs_deltaV_percent`

## Validation Approach (What to Check)
To validate in your utility context:
1. Compare model `Z_upstream` assumptions against utility-calculated Thevenin at study bus.
2. Benchmark one or two known historical motor-start events.
3. Compare against detailed utility study outputs for representative cases.
4. Tune:
- soft-start multiplier
- VFD PF/current-limit assumptions
- conductor `R/X` basis (spacing/temperature basis consistency)
5. Ensure thresholds match your utility approved flicker/step criteria and event rates.

## Known Limitations / Possible Inaccuracy Sources
- Simplified VFD front-end behavior (no full converter dynamics)
- Lumped equivalent network rather than full feeder topology
- Single-point upstream Thevenin assumption
- Capacitor modeled as static shunt var source
- No stochastic load diversity or time-varying pre-start voltage
- No explicit harmonic-voltage coupling in flicker metric

These are acceptable for screening but not final interconnection approval.

## Recommended Use in Workflow
1. Intake screen with this tool.
2. If `REVIEW` or `LIKELY VIOLATION`, escalate to detailed utility analysis.
3. Use this tool to compare mitigation options before final study:
- start method changes
- transformer sizing
- feeder reconductoring
- capacitor strategy
- upstream system-strength assumptions

## Tech
- Python
- NumPy
- Matplotlib

## Links
- GitHub: https://github.com/jacob-wheat-acre/motor-sim
