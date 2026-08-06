# Governance and trust

**Use case:** Hypertensive disorders of pregnancy → lifetime cardiovascular risk
**Risk classification:** **Elevated** — live PHI, an acute safety surveillance arm influencing urgent clinical response, remote monitoring devices, and a population with documented outcome disparities. This is a higher tier than [`endometriosis-cvd-risk`](../endometriosis-cvd-risk) and should not reuse its approval pathway.

## Governance stance

**Two arms, two autonomy levels.**

| Arm | Autonomy | Rationale |
|---|---|---|
| Acute BP surveillance (days 0–42) | Detect, escalate, notify — **never** triage away | Safety-critical; the twin may raise urgency, never lower it |
| Lifetime handoff (day 42+) | Recommend, route, write durable flag | Non-urgent; failure mode is omission, not harm |

**Asymmetric authority is deliberate.** The twin can move a patient toward human attention on its own. It can never move a patient away from human attention. A reading it cannot interpret escalates; it does not default to normal.

**Human oversight:**
- Every BP escalation is reviewed by a licensed clinician within the defined latency target.
- Every referral is ordered by a clinician.
- Patient-facing content is template-based and clinically reviewed; per-patient generated text requires review before send in v1.
- The problem-list write is the one action the twin performs directly. It is a documentation write with provenance, reversible, logged, and reviewable — treated as automatable because the alternative (relying on human memory at day 42) is the failure this pathway exists to fix.

**Fail-safe requirement:** if the twin is unavailable, standard postpartum protocol continues unchanged and staff are actively notified of the outage. Degradation must be loud. A silent failure in a safety pathway is worse than no pathway, because staff will have stopped doing the manual work.

**Escalation:** BP threshold breach, or concerning symptom report, routes immediately to the standard urgent obstetric pathway with human contact. The twin never holds an acute finding in a population health queue.

**Policy alignment and classification.** The lifetime arm is clinical decision support. **The acute arm may not be** — remote physiologic monitoring with alerting logic can approach device territory depending on configuration and claims made. Obtain a written regulatory determination before go-live. Do not infer the classification from the endometriosis use case; the analysis is genuinely different here.

## Risk and equity considerations

**Clinical safety**
- **Dominant risk: a missed or delayed escalation.** A severe-range BP transmitted and not acted on is a stroke risk in a young woman. Every design tradeoff resolves in favor of over-escalation.
- **Second risk: false reassurance.** Remote surveillance must never substitute for indicated in-person evaluation. Patient-facing language must state explicitly that a normal home reading does not override symptoms.
- **Device error.** Validated cuffs only, including appropriate sizing for larger arm circumference; documented calibration and validation posture; systematic error is an immediate suspension trigger.
- **After-hours coverage** must be defined and staffed before the first device is issued. Not after.

**Bias and disparities — the central governance concern**
- The disparity is documented and large: postpartum follow-up ranges from 5.7% to 95.4% with gaps tied to Black race, Hispanic ethnicity, and lower education ([systematic review](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6485948/)), and Black women face roughly double the postpartum hypertension readmission rate ([ChristianaCare](https://www.healthcatalyst.com/learn/success-stories/improving-detection-and-management-of-postpartum-hypertension)).
- **Digital access is the new gatekeeper.** A device-and-app pathway can widen the gap it was meant to close. Mitigations are mandatory, not aspirational: audio-only and SMS options, non-smartphone-dependent devices, multilingual content, and provision of connectivity where needed. Telehealth-based follow-up has eliminated racial disparities in a postpartum quality bundle ([AJOG](https://pubmed.ncbi.nlm.nih.gov/35121193/)) — the achievable benchmark is parity.
- **Coverage-blind design is inequitable by construction.** Referring an uninsured woman into a visit she cannot afford produces a documented "declined" that looks like patient choice and is not. Coverage status must gate routing.
- **Prohibited:** race as a model input; SDOH used to down-weight risk or deprioritize outreach; any prioritization rule that advantages commercially insured patients for scarce prevention slots.
- **Health equity has standing to force a reshape or trigger suspension** independent of the primary KPI.

**Privacy, security, data residency**
- Live PHI in a peripartum context. Full covered-environment handling; BAA coverage for every device vendor and RPM platform, reviewed before go-live.
- **Maternal/infant record separation** enforced and tested. Cross-contamination is an immediate suspension trigger.
- No PHI to third-party model APIs. Generative components run de-identified or templated, or against BAA-covered endpoints with identifier-free logging.
- Device vendor data flows explicitly mapped: what leaves the device, where it lands, who else can see it, what the vendor may do with it. Consumer-grade RPM terms are frequently incompatible with HIPAA expectations — read them.
- Minimum necessary: only the resource types enumerated in [`twin-thread.md`](./twin-thread.md).
- Consent and transparency at device issuance: what is monitored, who sees it, what happens to the data, and how to stop.

## Controls and guardrails

| Control | Implementation |
|---|---|
| Escalation floor | Threshold logic can be made more sensitive by config; loosening requires governance approval and clinical sign-off |
| Fail-loud | Outage or data-feed interruption triggers active staff notification and reversion to manual protocol |
| Volume cap | Ceiling on new lifetime-arm referrals tuned to prevention capacity; **no cap on acute-arm escalation, ever** |
| Kill switch | Two-stage: lifetime arm can be disabled independently; disabling the acute arm requires documented clinical handoff of every active patient |
| Rollback | Rule versions pinned and reversible within one business day |
| Sandbox/live separation | Distinct environments and credentials; synthetic or de-identified cohorts only in sandbox |
| Device governance | Validated device list, sizing inventory, calibration policy, recall procedure, recovery process |
| Access control | Worklist access limited to named roles; break-glass logged and reviewed |
| After-hours path | Documented, staffed, and tested before first device issuance |
| Change control | Any change to thresholds, cohort logic, or outreach modality requires governance sign-off plus a fresh equity check |

## Audit and traceability

- **Full replay** of any case: data available, rule version, subtype, stage, escalation decision, who acted, what happened. Ten random cases per quarter.
- **Separate escalation audit stream** reviewed weekly, with latency distribution and any breach investigated individually.
- **Versioning** of thresholds, cohort logic, message templates, and device configuration in source control with dated release notes.
- **Immutable event log** retained per policy, sufficient to support a maternal morbidity review.
- **Postmortem capability** — any safety event traceable to exact twin state and displayed content at the time.

## Committees and stakeholders

| Stakeholder | Role |
|---|---|
| AI / digital governance committee | Approves go-live; owns suspension authority |
| Maternal health service line lead | Accountable executive; owns keep/reshape/abandon recommendation |
| **Maternal safety / OB quality committee** | Co-approver; owns escalation protocol and any safety event review |
| Primary care & cardiology prevention lead | Owns the receiving end and its capacity |
| Health equity | Owns S1 guardrail; standing to force reshape or suspension |
| Privacy, security, compliance | PHI, BAA, device vendor flows, record separation |
| Regulatory / legal | Owns the device-classification determination |
| Nurse navigation & telehealth lead | Operational execution, device logistics, frontline feedback |
| Patient / community advisory, incl. postpartum patients | Reviews patient-facing language, consent, and outreach modality before launch |

## Promotion criteria

**Sandbox → limited pilot** (all required):
1. Cohort precision ≥0.85 on validation set, with clinical-signal identification outperforming codes alone.
2. Baselines measured retrospectively over 12 months, including equity stratification.
3. **Handoff detection validated at ≥70% on a retrospective cohort** — measurability proven before launch.
4. Escalation protocol written, staffed, after-hours path tested.
5. Two-stage kill switch tested and documented.
6. Fail-loud degradation tested — simulate an outage and confirm staff are notified.
7. Full replay demonstrated on ≥10 cases.
8. Device logistics resolved: validated inventory, sizing, connectivity, non-smartphone and audio-only paths live.
9. Coverage-gap workflow operational with named owner.
10. Volume projection reconciled against prevention capacity, jointly with the endometriosis pathway.
11. Privacy, security, and vendor BAA sign-off; maternal/infant separation tested.
12. **Written regulatory classification determination.**
13. Patient advisory review of consent and outreach materials.
14. Governance and maternal safety committee approval with all thresholds recorded in minutes.

**Limited pilot → service line rollout:** month-12 keep criteria in [`evaluation.md`](./evaluation.md) met, equity ratio sustained ≥0.85, zero unresolved safety events, and a navigator workflow that does not depend on the pilot team.

**Rollout → enterprise / multi-site:** re-validate cohort precision, escalation latency, and equity at each site. Prevention capacity, coverage policy, and device logistics vary more across sites than the clinical logic does — treat every site as a new pilot on a shortened runway.
