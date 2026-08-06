# KPIs and measurement

**Use case:** Endometriosis → long-term cardiovascular risk

One primary KPI. Two secondary. Anything more and the quarterly review becomes a data tour instead of a decision.

## Primary KPI

**Prevention pathway enrollment rate.**

> Of women with endometriosis flagged as elevated or high cardiovascular risk, the proportion who complete a cardiovascular prevention visit within 180 days of flag.

- **Numerator:** flagged patients with a completed prevention encounter (primary care or cardiology prevention visit, or documented enrollment in a prevention program) within 180 days of first flag.
- **Denominator:** all patients first flagged elevated/high during the measurement window, excluding those with pre-existing ASCVD, active pregnancy, hospice, or who moved out of network.
- **Attribution window:** 180 days from flag date; patients flagged in the last 180 days of the window roll to the next period.
- **Data sources:** Epic encounters and referral records; twin `pathway_stage` transitions as the audit trail.

**Why this and not a clinical outcome:** cardiovascular events are years out and a pilot cannot power on them. Enrollment is the closest measurable point where the twin's mechanism either worked or did not, and it is the last step the twin can actually influence.

## Secondary KPIs

**S1 — Equity of flagging and conversion.** Flag rate and conversion rate stratified by race, ethnicity, insurance type, and primary language. Expressed as the ratio of the lowest-performing to highest-performing stratum. This is a guardrail KPI: it can fail the pathway on its own even if the primary KPI improves.

**S2 — Clinician trust and burden.** Composite of flag dismissal rate, median time spent per flagged patient in the worklist, and a 3-item trust survey at weeks 4 and 12. If dismissal rate exceeds 50%, the twin is generating noise regardless of what the primary KPI shows.

Optional tracking metric (not a decision KPI): **screening completion** — proportion of flagged patients with a lipid panel and BP recorded within 90 days. Useful diagnostic for where the funnel leaks.

## Baseline and targets

Baselines must be measured retrospectively over the 12 months before go-live, using the same cohort definition. Do not estimate them.

| Metric | Baseline (measure, don't guess) | Pilot target | Horizon |
|---|---|---|---|
| Primary: prevention enrollment within 180d | Expect low single digits to ~15% | +50% relative, floor of +5 percentage points absolute | 12 months |
| S1: equity ratio (lowest/highest stratum conversion) | Measure | ≥0.80, and no worsening vs. baseline | 12 months |
| S2: flag dismissal rate | n/a | <35% sustained at week 12 | 12 months |

**Absolute floor matters.** A relative improvement on a tiny base (2% → 3%) is statistically real and operationally meaningless. The absolute floor is what protects you from declaring victory on noise.

## Funnel instrumentation

Track the funnel explicitly, because "the KPI didn't move" is not actionable but "we lose 70% between referral and attendance" is:

```
cohort identified
  → risk tier assigned (elevated/high)
    → flagged to worklist
      → outreach attempted
        → outreach successful (patient reached)
          → referral placed
            → appointment scheduled
              → appointment attended        ← primary KPI numerator
                → in prevention program
```

Each arrow gets a conversion rate and a median time-in-stage. Each stage records a twin state transition with timestamp and actor.

## Twin performance metrics

Separate from clinical KPIs — these tell you whether the twin itself is healthy:

- **Cohort precision / recall** against chart-reviewed ground truth (sample of 100 records at baseline, 50 quarterly).
- **Tier stability** — proportion of patients whose tier flips without a new clinical event. High flip rates mean the composite is unstable and clinicians will stop trusting it.
- **Staleness distribution** — share of active twins with data older than 90 days.
- **Drift** — quarterly comparison of tier distribution and driver mix; investigate shifts >10 percentage points.
- **Replayability spot-check** — for 10 random flags per quarter, reconstruct the tier assignment from the thread. If it cannot be reconstructed, that is a governance defect, not a nice-to-have.

## Reporting and dashboards

- **Monthly, operational:** funnel conversion, flag volume vs. prevention capacity, worklist aging, dismissal rate. Owner: nurse navigator lead + population health analyst.
- **Quarterly, decision-grade:** primary KPI vs. target, both secondary KPIs, twin performance metrics, and an explicit keep / reshape / abandon recommendation per `evaluation.md`. Audience: women's health service line, cardiology partner, AI governance committee.
- **One-page exec view:** baseline, current, target, trajectory, and the stop-rule status. If the one-pager cannot be produced from the dashboards automatically, the instrumentation is incomplete.

Evidence base for the risk premise and the cohort definition is cited in [`problem.md`](./problem.md).
