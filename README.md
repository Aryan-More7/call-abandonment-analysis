# Call Abandonment Analysis — UK Water Utility Contact Centre

A Workforce Management (WFM) analysis investigating why customers abandon calls in a
contact-centre queue, and where to intervene to reduce it. Built on a simulated 12-month
dataset modelled on a UK water utility (South West Water), covering ~50,000 interval-level
records across 7 queues.

**Live interactive dashboard:**
https://public.tableau.com/app/profile/aryan.more3059/viz/SouthWestWater-CallAbandonmentAnalysis/Dashboard1

---

## The question

*"Why are we losing calls out of the queue, and how do we reduce them?"*

This project answers it end to end:
generating a realistic dataset, diagnosing the drivers in Python, and presenting the findings
in a Tableau dashboard aimed at an operational decision.

## Headline result

Out of **752,972** calls offered over the year, **80,253 were abandoned — a blended rate of
10.7%**. The analysis shows this is **not** caused by agents being overworked. It is driven by
**forecast accuracy** (primary) and **shrinkage** (secondary). The fix is better interval-level
forecasting and tighter shrinkage control around predictable demand surges.

## The three-part finding

**1. Occupancy is not the constraint.**
The centre runs cool on average (mean occupancy ~40%, 99th percentile ~71%). Counter-intuitively,
abandonment is *higher* at low occupancy — because 30-minute interval averages hide the
intra-interval demand bursts that actually cause callers to wait and give up.

**2. Forecast accuracy is the primary driver.**
Grouping every interval by how far actual demand missed the forecast produces a clean, monotonic
curve: intervals where demand was over-forecast abandon under 1%, while the most badly
under-forecast intervals abandon **~25%**. Correlation between forecast variance and abandonment
is **+0.66**. Mechanism: under-forecast → too few agents scheduled → a surge arrives → the queue
builds faster than it clears → callers abandon.

**3. Shrinkage is the secondary driver.**
Roughly **30% of scheduled agent time** never reaches the phones (breaks, sickness, training,
meetings) — about 134,000 agent-intervals lost over the year. Abandonment climbs cleanly from
~2.7% in low-shrinkage intervals to ~12% in high-shrinkage ones.

Supporting evidence: abandonment concentrates in high-volume "surge" intervals and in specific
windows (Monday mornings, and the four seasonal demand events), which is exactly what a
forecast-accuracy problem looks like — and what makes the fix *targeted* rather than expensive.

## The dashboard

Six views telling the story top to bottom — scale, timing, demand drivers, root cause, and where
to act:

- **KPI strip** — blended abandonment rate, calls abandoned, calls offered
- **Daily abandonment line** — abandonment across the year, with seasonal spikes
- **Daily volume by queue** — which queue drives each spike (freeze-thaw burst mains in Feb,
  autumn storms in Oct, the annual bill in April)
- **Forecast variance vs abandonment** — the primary driver (the staircase to ~25%)
- **Shrinkage vs abandonment** — the secondary driver
- **Day × hour heatmap** — where losses concentrate (Monday mornings glow red), justifying
  targeted staffing over mass hiring

## The data

A synthetic dataset designed so that abandonment **emerges from queue dynamics** — a per-call
caller-patience threshold tested against modelled wait times — rather than being randomly
assigned. Real water-sector demand drivers are built into the data:

- Freeze-thaw burst mains (late Jan/Feb) — the signature spike of the year
- The annual regulated bill issue (early April)
- Summer heatwave / hosepipe demand (Jul/Aug)
- Autumn storms and sewer flooding (Oct/Nov)

Forecasts are deliberately imperfect in specific windows so abandonment has a *diagnosable* root
cause, and ~30% shrinkage is applied so scheduled headcount differs from available headcount.

## Repository structure

```
├── data/         interval and daily aggregate CSVs
├── notebooks/    exploratory analysis (Python / pandas)
├── dashboard/    packaged Tableau workbook (.twbx) + screenshots
└── docs/         detailed project notes
```

## Method

WFM metrics used throughout: abandonment rate, ASA, service level, occupancy, AHT, shrinkage,
and forecast accuracy. Abandonment rate is computed as total abandoned ÷ total offered
(volume-weighted), so high-volume intervals are represented correctly rather than each interval's
rate being averaged equally.

## Note on modelling assumptions

The dataset models standard utility opening hours (Mon-Fri full service, Sat reduced, Sun/bank
holidays emergency-only), which is why the heatmap shows no weekend-evening activity. In reality
the centre runs 24/7 for emergencies with normal hours for non-urgent queues; a future iteration
would reflect that split. The findings — forecast accuracy as the primary driver, shrinkage as
secondary — hold within operating hours regardless of the exact opening-hours model.

## Tools

Python (pandas, NumPy, Matplotlib), Tableau Public.
