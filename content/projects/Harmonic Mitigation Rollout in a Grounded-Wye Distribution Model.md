---
title: "Harmonic Mitigation Rollout in a Grounded-Wye Distribution Model"
date: 2026-02-27
draft: false
summary: "A high-resolution OpenDSS study comparing detuned capacitor banks vs cap-bank neutral reactors under Healthy, Degraded, and Stolen Ground scenarios."
---

## Overview
This project started as a practical utility question: what actually moves the needle for harmonic mitigation in a grounded-wye distribution system?

I built a full OpenDSS test feeder with:
- explicit neutral conductor modeling,
- multi-grounded neutral behavior,
- grounded-wye capacitor banks modeled per-phase,
- and rollout simulations for two mitigation strategies:
1. Detuned capacitor banks (per-phase series reactors),
2. Cap-bank neutral grounding reactors.

I also added grounding-integrity scenarios to reflect field reality:
- Healthy multi-grounded neutral,
- Degraded multi-grounded neutral,
- Stolen groundwire (non-multi-grounded neutral).

The result: detuning produced strong phase-voltage harmonic changes in this model, while neutral-reactor rollout showed smaller impact on phase-voltage metrics but measurable neutral-path behavior.

## Why This Matters
This study is meant to be a transparent, repeatable engineering analysis:
- same feeder,
- same load/cap topology,
- same harmonic injection approach,
- only mitigation and grounding assumptions changed.

## Model Highlights
- 230/13.2 kV source + substation transformer
- 4 feeders, 5 segments each
- Explicit neutral network via node `.4` and neutral lines
- Substation NGR + feeder pole grounding
- 1,200 kvar grounded-wye cap banks represented as **3 x 400 kvar phase-to-neutral branches**
- Rollout depth `N=1..4` (first N cap positions per feeder upgraded)

## Mitigation Cases
For each rollout depth (`N=1..4`), I compared:
- Baseline: all caps plain (no added mitigation)
- Detuned rollout: first N cap-bank positions detuned
- Neutral-reactor rollout: first N cap-bank positions receive cap-bank neutral reactor

For each case, I evaluated:
- `Vavg` (phase-voltage harmonic magnitude)
- `|I_NGR|` (substation neutral reactor current)
- `Σ|I_NR|` (sum of cap-bank neutral reactor currents)
- `|Vn|` at a remote feeder neutral node

## Grounding Scenarios
- **Healthy Multi-Grounded-Neutral**
- **Degraded Multi-Grounded-Neutral**
- **Stolen Ground** (majority of local grounds unavailable)

## Results

### 1) Harmonic spectrum comparison by rollout depth
![Healthy N4](/images/harmonic-study/rollout_compare_healthy_N4_log.png)
*Healthy MGN, first 4 cap positions per feeder.*

![Stolen Ground N4](/images/harmonic-study/rollout_compare_stolen_ground_N4_log.png)
*Stolen Ground, first 4 cap positions per feeder.*

### 2) Neutral-mitigation KPI view (key chart)
![Stolen Ground Metrics N4](/images/harmonic-study/rollout_metrics_stolen_ground_N4.png)
*Stacked KPI plot: `Vavg`, `|I_NGR|`, `Σ|I_NR|`, and remote `|Vn|`.*

## Interpretation
What I observed in this model:
- Detuned rollout strongly affects phase-side harmonic response (`Vavg`),
- Neutral-reactor rollout has a smaller effect on phase-voltage metrics,
- Neutral-reactor rollout does show behavior in neutral-path metrics (`Σ|I_NR|`, etc.),
- Ground degradation changes the context, but the detuned-vs-neutral contrast remained clear for phase-voltage outcomes.

## Practical Takeaway
For this modeled feeder and KPI set:
- If the target is phase-voltage harmonic suppression, detuning is the stronger lever.
- If neutral-path stress and neutral displacement are concerns, neutral-focused mitigation still matters and should be judged with neutral KPIs, not phase voltage alone.

## Tech
- OpenDSS
- Python
- pandas
- matplotlib

## Links
- GitHub: https://github.com/jacob-wheat-acre/harmonic-study
