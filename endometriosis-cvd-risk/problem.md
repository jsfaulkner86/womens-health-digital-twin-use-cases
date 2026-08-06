# Problem this digital twin use case solves

**Use case:** Endometriosis → long-term cardiovascular risk
**Status:** Exemplar (reference pattern for this catalog)
**Decision owner:** Women's health service line lead, in partnership with cardiology / primary care

## Summary

Women with endometriosis carry measurably elevated long-term cardiovascular risk, but that risk is almost never carried forward into cardiovascular prevention decisions. The gynecologic problem is treated; the cardiometabolic consequence is dropped at the service line boundary.

This twin supports one decision: **for a woman with confirmed or probable endometriosis, should she be actively enrolled in a cardiovascular prevention pathway now, and if so, was she?**

## Population and stakeholders

**Population.** Women with a confirmed or strongly probable endometriosis diagnosis, ages 18–55, with no existing atherosclerotic CVD diagnosis.

**Who decides:**
- The patient — who drives the decision to accept referral, screening, and follow-up. Women make roughly 80% of household healthcare decisions, so the twin's output has to be legible to her, not only to the chart.
- The OB/GYN — who owns the encounter where endometriosis is diagnosed and managed, but does not own CVD prevention.
- Primary care / cardiology — who owns prevention but rarely sees endometriosis as a risk-modifying signal.
- Service line and quality leadership — who decide whether the pathway is funded and staffed.

**Where in the journey:** diagnosis or ongoing management visit in gynecology; then registry-level surveillance between visits, which is where the twin does most of its work.

## Current state

- Endometriosis is diagnosed late. Reported delays cluster around 6–10 years from symptom onset, with wide variation by country and care setting ([York review of 22 studies, mean 6.6 years](https://www.york.ac.uk/news-and-events/news/2024/research/diagnosis-endometriosis-delay/), [AHRQ PSNet](https://psnet.ahrq.gov/web-mm/endometriosis-common-and-commonly-missed-and-delayed-diagnosis)). By the time the diagnosis exists as structured data, years of risk accumulation are already invisible.
- CVD risk elevation is real but not operationalized. Pooled cohort evidence shows increased ischemic heart disease (HR 1.50, 95% CI 1.37–1.65) and cerebrovascular disease (HR 1.17, 95% CI 1.07–1.29) ([systematic review and meta-analysis, 6 cohorts, n=254,929](https://pubmed.ncbi.nlm.nih.gov/37075537/)). A larger synthesis of 11 cohorts (n≈3.1M) found a 22% increase in all-cause CVD, with coronary artery disease HR 1.47 ([meta-analysis of cohort studies](https://pmc.ncbi.nlm.nih.gov/articles/PMC13288533/)). Nurses' Health Study II found elevated MI risk in laparoscopically confirmed endometriosis ([Circulation: Cardiovascular Quality and Outcomes](https://www.ahajournals.org/doi/10.1161/circoutcomes.115.002224)), and Danish nationwide registry data confirm long-term cardiovascular signal ([European Heart Journal](https://academic.oup.com/eurheartj/article/45/44/4734/7741666)).
- Standard risk calculators do not carry the signal. Most pooled-cohort-equation-style tools are age-weighted and will score a 34-year-old with endometriosis as low risk, which is technically correct for 10-year risk and wrong for lifetime risk. There is no field in the workflow where "endometriosis" changes a prevention decision.
- The handoff has no owner. Nothing fails loudly. The patient simply never gets flagged, and no one is accountable for the miss.

## Desired future state

- Every woman with endometriosis in the population has a maintained cardiovascular risk state that updates as new data arrives — not a one-time score at a single visit.
- When risk state crosses a defined threshold, the twin produces a **named, routable action** with an owner: prevention referral, lipid and BP screening, or counseling — not a passive banner.
- The gynecologist can see, in one line, why this patient was flagged and what the recommended action is, without leaving the encounter.
- The patient receives an explanation she can act on, in her language, with a scheduling path attached.
- Leadership can answer, quarterly, whether the pathway actually increased prevention enrollment — and can kill it if it did not.

**Constraints to design against:**
- Endometriosis coding in Epic is unreliable; surgical confirmation, problem-list entry, and claims often disagree. The twin must handle probabilistic case definition, not assume a clean cohort.
- Flag fatigue is the primary failure mode. If the twin fires on the whole endometriosis population, it will be ignored within two months.
- Prevention referral capacity is finite. Volume must be tuned to available slots or the pathway generates unbookable referrals.
- Reimbursement for the prevention arm is separate from gynecologic billing; the financial case must be built cross-service-line.
- Equity is not optional here. Diagnostic delay is longer for Black and low-income women, meaning the cohort is already biased at entry — the twin risks compounding an existing access gap if flagging depends on having gotten diagnosed.

## Why this matters

- Chronic burden: endometriosis affects roughly 10% of reproductive-age women ([European Heart Journal](https://academic.oup.com/eurheartj/article/45/44/4734/7741666)), and CVD remains the leading cause of death in women.
- Decision leverage: women drive the large majority of household healthcare decisions. A twin that speaks to the decision maker moves outcomes faster than one that only speaks to the chart.
- Institutional fit: this pathway sits at the intersection of quality (prevention screening rates), access (cross-service-line referral), and financial sustainability (downstream cardiac event avoidance) — the three arguments a service line lead has to make anyway.
- Strategic: it is the cleanest available proof that a digital twin can carry risk across a service line boundary, which is the general capability this catalog is arguing for.
