# Problem this digital twin use case solves

**Use case:** Hypertensive disorders of pregnancy → lifetime cardiovascular risk
**Status:** Drafted
**Decision owner:** Maternal health service line lead, jointly with primary care and cardiology prevention

## Summary

A hypertensive disorder of pregnancy (HDP) is the single best cardiovascular risk-stratification event most women will ever receive, and the health system discards it. Obstetric care ends at six weeks, the risk signal ends with it, and the woman re-enters the system a decade later as an undifferentiated primary care patient.

This twin supports one decision: **for a woman with a hypertensive disorder of pregnancy, is she being carried into ongoing cardiovascular risk management — and did the handoff actually complete?**

## Population and stakeholders

**Population.** Women with preeclampsia, eclampsia, gestational hypertension, HELLP, or superimposed preeclampsia in the index pregnancy. Tracked from delivery through 12 months postpartum, then transitioned to a durable risk flag.

**Who decides:**
- The patient — postpartum, with a newborn, often uninsured or about to be, and making a return-visit decision against enormous competing demand. Every design choice has to respect that this is the hardest possible moment to ask someone to attend an appointment about a future risk.
- The obstetric team — owns the delivery and the six-week visit, does not own the next forty years.
- Primary care / cardiology prevention — owns the next forty years, usually never learns the pregnancy happened.
- Maternal health and quality leadership — accountable for postpartum readmission and increasingly for postpartum quality measures.

**Where in the journey:** delivery discharge → early postpartum BP surveillance (days 7–10) → six-week postpartum visit → **the handoff cliff** → primary care. The twin's entire reason to exist is that cliff.

## Current state

**The risk is large, durable, and well quantified.** Adjusted meta-analysis puts preeclampsia at roughly 4-fold future heart failure (RR 4.19, 95% CI 2.09–8.38), 2.5-fold coronary heart disease (RR 2.50, 95% CI 1.43–4.37), 2.2-fold cardiovascular death, and 1.8-fold stroke ([Circulation: Cardiovascular Quality and Outcomes](https://www.ahajournals.org/doi/10.1161/circoutcomes.116.003497)). An updated systematic review confirms the pattern — cardiovascular death ES 2.08, coronary artery disease ES 2.04, heart failure ES 2.47, stroke ES 1.75 after adjustment ([European Heart Journal – Quality of Care and Clinical Outcomes](https://academic.oup.com/ehjqcco/article/10/1/4/7424969)). The classic BMJ synthesis found a 3.70 relative risk of later hypertension at ~14 years of follow-up ([systematic review and meta-analysis](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2072042/)). The AHA states plainly that affected women are at increased cardiovascular risk later in life, independent of traditional risk factors ([AHA Scientific Statement on hypertension in pregnancy](https://www.ahajournals.org/doi/10.1161/CIRCULATIONAHA.124.073302)).

**The follow-up does not happen.** Only 13.7% of women diagnosed with HDP attended a blood pressure screening within 7–10 days of delivery, despite that being the explicit recommendation ([Journal of Women's Health, reported via Healio](https://www.healio.com/news/womens-health-ob-gyn/20211129/most-atrisk-women-skip-blood-pressure-screenings-after-giving-birth)). Single-center series do better but not well — 51.1% and 57.9% attendance in two cohorts ([Obstetrics & Gynecology](https://pubmed.ncbi.nlm.nih.gov/33239217/), [second cohort study](https://pubmed.ncbi.nlm.nih.gov/34775586/)). Longer term is worse: at 6 months postpartum, only 18% of women with HDP had a primary care visit, versus 13% of normotensive women — a 5-point difference for a population carrying 2–4x cardiovascular risk ([Journal of the American Heart Association](https://pmc.ncbi.nlm.nih.gov/articles/PMC7660757/)).

**The gap is inequitable.** A systematic review found postpartum follow-up rates ranging from 5.7% to 95.4%, with disparities tied to Black race, Hispanic ethnicity, lower education, and comorbid mental health conditions ([systematic review of postpartum follow-up](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6485948/)). Black women are readmitted for postpartum hypertension at roughly twice the rate of white women ([ChristianaCare improvement program](https://www.healthcatalyst.com/learn/success-stories/improving-detection-and-management-of-postpartum-hypertension)).

**No one owns the handoff.** The obstetric record closes. The HDP diagnosis may or may not reach the problem list. Primary care receives no signal. There is no failure alarm because there is no defined success state.

## Desired future state

- Every HDP delivery instantiates a twin at discharge, before anyone has to remember to act.
- Early BP surveillance is pushed to the patient (home BP monitoring, remote capture) rather than requiring her to travel with a newborn. Postpartum home BP monitoring improves ascertainment and reduces racial disparities in BP measurement ([Circulation](https://www.ahajournals.org/doi/10.1161/CIRCULATIONAHA.124.073302)) — the twin should route to the modality that works, not the one that's traditional.
- The HDP diagnosis is written durably to the problem list as a cardiovascular risk modifier, so it survives the closure of the obstetric episode.
- A named handoff to primary care or cardiology prevention is generated, routed, and **confirmed complete** — with the confirmation written back to twin state.
- If the handoff fails, someone is told. The failure is visible, not silent.
- Leadership can see, within months rather than years, whether the cliff is being closed.

**Constraints to design against:**
- **Insurance discontinuity.** Pregnancy-related Medicaid coverage may lapse postpartum depending on state policy. A referral to a visit she cannot afford is not a handoff. The twin must know coverage status and route accordingly.
- **Attention economics.** The postpartum period is the lowest-availability moment in a woman's life. Any design requiring more than minimal patient effort will fail on exactly the population most at risk.
- **Diagnosis capture.** Gestational hypertension is frequently under-coded or resolved-and-forgotten. Cohort identification cannot rely on the problem list alone; it needs BP values, magnesium sulfate administration, antihypertensive orders, and delivery documentation.
- **Attribution across organizations.** Delivery and primary care are often different organizations. Handoff confirmation may require claims, HIE, or patient attestation rather than internal encounter data.
- **Equity is the point, not a side constraint.** A pathway that lifts the average while leaving Black and Medicaid-covered women where they are has failed on its primary rationale.

## Why this matters

- **Magnitude and specificity.** HDP affects a large share of deliveries and confers a risk multiple that dwarfs most factors in a standard risk calculator. Few risk signals in medicine are this strong, this early, and this ignored.
- **Decision leverage.** Women drive roughly 80% of household healthcare decisions. Reaching a woman at the moment she is already navigating the system for her infant is the highest-leverage engagement window that exists in adult prevention.
- **Time-to-signal.** Unlike most lifetime-risk pathways, this one has a **14-day leading indicator** (BP check attendance) and a 6-month primary endpoint. It can be evaluated on a pilot timeline, which makes it the strongest candidate in this catalog for proving the twin model works.
- **Institutional alignment.** Postpartum readmission for hypertension is already a tracked, penalized outcome. The prevention case and the finance case point the same direction for once.
- **Strategic.** This is the cleanest demonstration of the general claim behind this catalog: that a digital twin's job is to carry a risk signal across an organizational boundary that no human role currently owns.
