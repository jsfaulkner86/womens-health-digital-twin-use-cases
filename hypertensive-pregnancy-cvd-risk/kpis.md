# KPIs and measurement

**Use case:** Hypertensive disorders of pregnancy → lifetime cardiovascular risk

One primary KPI, two secondary, plus one leading indicator that reads out in two weeks instead of six months. The leading indicator is what makes this use case pilotable on a realistic timeline.

## Primary KPI

**Cardiovascular prevention handoff completion.**

> Of women with a hypertensive disorder of pregnancy, the proportion with a **completed** cardiovascular risk-management visit (primary care or cardiology prevention) within 6 months of delivery, **and** a durable HDP risk flag on the problem list.

- **Numerator:** completed qualifying ambulatory visit within 180 days of delivery, evidenced by internal encounter, external claim/HIE record, or documented patient attestation — **and** `risk_carry_flag` present on the problem list.
- **Denominator:** all HDP deliveries in the window, excluding maternal death, transfer of care out of region, and hospice.
- **Attribution window:** 180 days from delivery date.
- **Data sources:** Epic encounters and problem list; claims/HIE for external attribution; twin `handoff_stage` transitions as the audit trail.

**Why the flag is part of the numerator.** A visit without the durable flag delivers a one-time interaction and loses the signal again. The flag without a visit delivers a note nobody reads. The pathway only works if both happen, so the KPI requires both. This will make the number look worse than a visit-only measure. That is the correct outcome.

**Baseline anchor:** roughly 18% of women with HDP had a primary care visit by 6 months postpartum, versus 13% of normotensive women ([Journal of the American Heart Association](https://pmc.ncbi.nlm.nih.gov/articles/PMC7660757/)). Local baseline must still be measured — and the flag component will start near zero.

## Leading indicator

**Day-14 BP surveillance engagement.**

> Proportion of HDP deliveries with at least two postpartum BP readings captured (any source: clinic, home device, remote) within 14 days of delivery.

Reads out in two weeks, not six months. Available benchmark for in-person attendance is 13.7% within 7–10 days ([Journal of Women's Health via Healio](https://www.healio.com/news/womens-health-ob-gyn/20211129/most-atrisk-women-skip-blood-pressure-screenings-after-giving-birth)), with single-center in-person cohorts at 51.1% and 57.9% ([Obstetrics & Gynecology](https://pubmed.ncbi.nlm.nih.gov/33239217/), [cohort study](https://pubmed.ncbi.nlm.nih.gov/34775586/)).

This is a **go/no-go gate at week 6 of the pilot**, not a success metric. If day-14 engagement is not clearly above the local in-person baseline, the acute arm is not working and there is no point waiting six months to find out.

## Secondary KPIs

**S1 — Equity of handoff completion.** Primary KPI stratified by race, ethnicity, insurance type (commercial / Medicaid / uninsured), primary language, and rurality. Expressed as lowest-stratum to highest-stratum ratio.

This is the decisive metric for this use case, not a supporting one. Reported follow-up rates range from 5.7% to 95.4% with disparities tied to Black race, Hispanic ethnicity, and lower education ([systematic review](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6485948/)), and Black women are readmitted for postpartum hypertension at roughly twice the rate ([ChristianaCare](https://www.healthcatalyst.com/learn/success-stories/improving-detection-and-management-of-postpartum-hypertension)). Telehealth-based postpartum BP follow-up has eliminated racial disparities in at least one quality bundle ([American Journal of Obstetrics & Gynecology](https://pubmed.ncbi.nlm.nih.gov/35121193/)), so closing this gap is demonstrably achievable — which means failing to close it is a design failure, not an inherited condition.

**S2 — Postpartum hypertension readmission rate.** 42-day readmission or ED visit with a hypertensive diagnosis. This is the safety-and-finance metric: it is the argument that funds the pathway, and it is also the signal that would reveal harm if remote surveillance replaced in-person contact badly.

Tracking metrics (not decision KPIs): device issuance rate at discharge; problem-list flag write rate; coverage-lapse rate at 60 days; median time from delivery to referral placement.

## Baseline and targets

Measure all baselines retrospectively over 12 months on an identically defined cohort. Do not estimate.

| Metric | Baseline | Pilot target | Horizon |
|---|---|---|---|
| Leading: ≥2 BP readings by day 14 | Measure; expect 14–55% depending on current model | ≥60% | 6 weeks |
| Primary: handoff complete + flag by 180d | Measure; visit-only anchor ~18%, combined near 0% | ≥35% absolute, and ≥+15 percentage points over visit-only baseline | 12 months |
| S1: equity ratio | Measure | ≥0.85, no stratum below its own baseline | 12 months |
| S2: 42-day HTN readmission | Measure | No increase; directional target −20% | 12 months |

**Note the higher equity bar** (0.85 versus 0.80 in the endometriosis pathway). Justified because the disparity here is larger, better documented, and demonstrably fixable with modality change.

## Funnel instrumentation

```
HDP delivery identified
  → twin instantiated at discharge
    → BP device / program issued before discharge
      → ≥1 reading transmitted
        → ≥2 readings by day 14              ← leading indicator
          → acute window completed without escalation failure
            → HDP flag written to problem list
              → coverage verified / gap resolved
                → prevention referral placed
                  → appointment scheduled
                    → appointment attended    ← primary KPI (with flag)
                      → ongoing risk management documented
```

Every arrow gets a conversion rate, a median time-in-stage, and equity stratification. The two arrows that will fail are **device issued → reading transmitted** and **coverage verified → referral placed**. Instrument those most heavily.

## Twin performance metrics

- **Cohort precision / recall** against chart-reviewed ground truth (100 deliveries at baseline, 50 quarterly), reported separately for coding-based versus clinical-signal-based identification.
- **Subtype classification accuracy** — severity drives surveillance intensity, so misclassification has clinical consequence.
- **Escalation latency** — time from threshold-breach reading to human contact. Safety-critical; target under 4 hours during business hours with a defined after-hours path.
- **Handoff detection sensitivity** — proportion of known-completed external visits the system successfully detects. If this is under 70%, the primary KPI is unmeasurable and the pilot cannot conclude.
- **Staleness distribution** and **lapse-detection lag**.
- **Replayability spot-check** — 10 random cases per quarter reconstructed end to end.

## Reporting and dashboards

- **Weekly, acute:** BP surveillance worklist, lapses, escalation latency, device issuance rate. Owner: postpartum navigation lead.
- **Monthly, operational:** full funnel with equity stratification, coverage-gap volume, referral capacity utilization.
- **Quarterly, decision-grade:** primary KPI, both secondary KPIs, twin performance, and an explicit keep / reshape / abandon recommendation per [`evaluation.md`](./evaluation.md). Audience: maternal health service line, primary care and cardiology prevention, AI governance, health equity.
- **Week 6:** leading-indicator go/no-go review. Single page, single decision.
