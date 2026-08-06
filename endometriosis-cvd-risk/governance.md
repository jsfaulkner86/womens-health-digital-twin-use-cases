# Governance and trust

**Use case:** Endometriosis → long-term cardiovascular risk
**Risk classification:** Moderate — operates on live PHI, influences clinical referral decisions, no autonomous clinical action.

## Governance stance

**Autonomy level: recommend and route only.** The twin may compute risk state, generate a flag, populate a worklist, and draft outreach language. It may **not** place orders, close referral loops, or communicate a clinical recommendation to a patient without human review in v1.

**Human oversight:**
- Every flag is worked by a named nurse navigator before any patient contact.
- Every referral is ordered by a licensed clinician.
- Patient-facing message templates are clinically reviewed and version-controlled; generated per-patient text is reviewed before send in v1, moving to template-with-variables in v2.

**Escalation:** if the twin surfaces a finding suggesting higher acuity than the prevention pathway addresses (e.g. concerning BP, symptomatic presentation), it routes out of the pathway to the standard urgent process. The twin must never be the reason an acute finding sat in a population health queue.

**Policy alignment:** governed under the organization's existing clinical decision support and AI governance policy. Classified as clinical decision support, not a medical device function — it stratifies risk and prompts guideline-consistent screening, with the clinician's independent basis for the recommendation preserved and displayed. Confirm this classification with regulatory counsel before go-live; do not assume it.

## Risk and equity considerations

**Clinical safety**
- False negatives are the dominant clinical risk: a woman with real elevated risk who is never flagged is left exactly where she is today. This is not a new harm, but it must not be described as a benefit.
- False positives consume finite prevention capacity and erode clinician trust.
- Anchoring risk: a "low" tier must never be displayed in a way that discourages a clinician from acting on their own judgment. Frame output as "prevention pathway eligible / not eligible," never as "cardiovascular risk: low."

**Bias and disparities — the central risk in this use case**
- The cohort is biased at the door. Endometriosis diagnostic delay is longer for Black, low-income, and non-English-speaking women, so a twin keyed to *having a diagnosis* will preferentially serve women who already had better access.
- Mitigation: (1) include a probable-case arm based on symptom and utilization patterns, not diagnosis codes alone; (2) monitor flag rate and conversion by stratum as a hard guardrail KPI, not a report-out; (3) explicitly review whether outreach modality disadvantages any group.
- Prohibited: using race as a model input, or using SDOH variables to down-weight risk or deprioritize outreach. SDOH data is used only to detect and correct inequity.

**Privacy, security, data residency**
- Live PHI. All cohort construction, risk computation, and worklist generation occur inside the covered, BAA-bound environment.
- No PHI to third-party model APIs. Any generative use runs on de-identified or templated inputs, or against a BAA-covered endpoint with identifier-free logging.
- Traces and application logs carry resource references and trace IDs, not names, MRNs, or free-text clinical content.
- Data residency and subprocessor list reviewed by privacy before go-live and re-reviewed on any vendor change.
- Minimum necessary: the twin reads only the resource types enumerated in [`twin-thread.md`](./twin-thread.md).

## Controls and guardrails

| Control | Implementation |
|---|---|
| Volume cap | Hard daily/weekly ceiling on new flags, tuned to prevention capacity; excess queues rather than fires |
| Threshold config | Risk thresholds are configuration, changeable without redeploy, every change logged with approver |
| Kill switch | Single documented action disables flag generation and hides the encounter surface; tested before go-live and quarterly thereafter |
| Rollback | Rule/model versions are pinned and reversible; prior version restorable within one business day |
| Sandbox/live separation | Distinct environments, distinct credentials, no live-PHI copies in sandbox; synthetic or de-identified cohorts only |
| Access control | Worklist access limited to named pathway roles; break-glass access logged and reviewed |
| Rate and cost limits | Per-run budgets on any LLM-backed component, with alerting on anomaly |
| Change control | Any change to case definition, risk logic, or thresholds requires governance sign-off and a fresh equity check |

## Audit and traceability

- **Full replay.** Any flag can be reconstructed: what data existed, which rule version ran, what tier resulted, which drivers, who acted, what happened. Spot-checked on 10 random flags per quarter per [`kpis.md`](./kpis.md).
- **Versioning.** Case definition, risk logic, thresholds, and message templates are all versioned in source control with dated release notes.
- **Immutable event log** of state transitions and clinical actions, retained per organizational record retention policy.
- **Postmortem capability.** Any safety event or complaint can be traced to the exact twin state and displayed content at the time of the encounter.

## Committees and stakeholders

| Stakeholder | Role |
|---|---|
| AI / digital governance committee | Approves go-live, owns suspension authority, reviews quarterly |
| Women's health service line lead | Accountable executive; owns keep/reshape/abandon recommendation |
| Cardiology / primary care prevention lead | Co-owner; owns capacity and downstream pathway |
| Clinical quality | Owns KPI definitions and safety event review |
| Privacy, security, compliance | Owns PHI handling, BAA posture, data residency, minimum-necessary review |
| Health equity | Owns the S1 guardrail; has standing to force a reshape |
| Nurse navigation lead | Owns operational execution and frontline feedback |
| Patient / community advisory | Reviews patient-facing language and consent framing before launch |

## Promotion criteria

**Sandbox → limited pilot** (all required):
1. Cohort precision ≥0.80 on chart review of synthetic/de-identified validation set.
2. Baseline KPIs measured retrospectively over 12 months.
3. Equity stratification measurable and reported before first flag fires.
4. Kill switch tested and documented.
5. Full replay demonstrated end to end on at least 10 cases.
6. Volume projection reconciled against actual prevention capacity.
7. Privacy and security sign-off; regulatory classification confirmed.
8. Governance committee approval with keep/reshape/abandon thresholds recorded in the minutes.

**Limited pilot → service line rollout:** month-12 keep criteria in [`evaluation.md`](./evaluation.md) met, plus sustained equity guardrail and documented navigator workflow that does not depend on the pilot team.

**Rollout → enterprise / multi-site:** re-validate cohort precision and equity at each new site. Performance does not transfer across populations, coding practices, or referral capacity; treat every site as a new pilot with a shorter runway.
