# Evaluation: keep, reshape, or abandon

**Use case:** Hypertensive disorders of pregnancy → lifetime cardiovascular risk

Thresholds are set before go-live and recorded in governance minutes. This pathway has a safety arm, so it also carries suspension triggers that override the review calendar.

## Instrumentation plan

**Data taps**
- Epic: `Condition` (O11–O16), delivery documentation, BP `Observation` series, `MedicationAdministration` (magnesium sulfate, IV antihypertensives), labs, `ServiceRequest`, problem list writes, encounters, readmissions and ED visits.
- Remote/home BP: device transmissions with timestamp, source, and validation status.
- Coverage feed: Medicaid status and postpartum end date.
- Claims / HIE: external prevention visit detection.
- Twin event log: every stage transition with timestamp, trigger, prior state, new state, actor.
- Worklist and outreach telemetry: contact attempts, modality, outcome, language used.

**Logging and tracing**
- Structured evaluation log per twin run: inputs referenced, rule version, subtype, stage output, escalation decision.
- Trace ID from data ingestion through recommendation to clinical action.
- **Escalation events logged separately and reviewed weekly** — this is the safety arm and it gets its own audit stream.
- Rule version pinned per evaluation.
- PHI discipline: identifiers excluded from application logs; identity joins only inside the covered environment; strict separation from the infant record.

**Comparison design**
- **Preferred: stepped-wedge by delivery site or pod.** This use case suits it well — deliveries are continuous, sites are naturally separable, and it gives an internal control against the objection that improvement came from the new navigator rather than the twin.
- Minimum acceptable: pre/post with a 12-month retrospective baseline on an identically defined cohort, plus contemporaneous normotensive-delivery comparison to detect secular trend in postpartum engagement generally.
- **Do not** use a concurrent untreated HDP control group at the same site. Withholding a guideline-recommended BP surveillance pathway from a population with documented readmission risk is not defensible.

**Dashboards** — per [`kpis.md`](./kpis.md): weekly acute, monthly operational, quarterly decision-grade, plus the week-6 go/no-go page.

## Review cadence

| Cadence | Forum | Authority |
|---|---|---|
| Weekly | Acute safety huddle (navigation lead, OB champion, analyst) | Escalation latency, lapses, device logistics |
| **Week 6** | Leading-indicator gate | Continue / fix modality / halt |
| Monthly | Operational review | Thresholds, outreach modality, coverage workflow |
| Quarterly | Service line + prevention + governance + equity | Formal keep / reshape / abandon |
| Ad hoc | Governance committee | Immediate suspension |

Decision points: **week 6** (leading-indicator gate), **month 6** (interim, reshape-or-continue), **month 12** (binding).

## Week-6 gate

Distinct from the keep/reshape/abandon decision and deliberately blunt:

- **Continue** if day-14 BP engagement ≥60%, or clearly above local in-person baseline and trending up.
- **Fix modality** if engagement is 35–60%: device logistics, issuance timing, and language access are the first suspects — not the twin logic.
- **Halt and rebuild** if engagement <35%. The acute arm is the delivery vehicle for the lifetime arm. If she is not engaged at day 14, the 6-month handoff will not happen, and running the pilot to month 12 to confirm that wastes two quarters.

## Keep criteria

All of the following at month 12:

- **Primary KPI ≥35% absolute** and **≥+15 percentage points** over measured visit-only baseline.
- **Equity ratio ≥0.85**, no stratum below its own baseline. Given that telehealth-based postpartum follow-up has eliminated racial disparities elsewhere ([AJOG](https://pubmed.ncbi.nlm.nih.gov/35121193/)), a persistent gap here is a fixable design defect, not an acceptable result.
- **42-day hypertension readmission not increased** versus baseline.
- **Zero unresolved safety events** attributable to the twin — specifically, no missed BP escalation and no case where remote surveillance substituted for needed in-person evaluation.
- **Escalation latency** median under 4 hours in-hours, with a documented and tested after-hours path.
- **Cohort precision ≥0.85** on chart review.
- **Handoff detection sensitivity ≥70%** — the KPI must be measurable, not just favorable.
- Prevention capacity absorbing volume without unbookable referrals, capacity-planned jointly with [`endometriosis-cvd-risk`](../endometriosis-cvd-risk).

## Reshape criteria

Any of these means iterate — with a named hypothesis, one changed variable, and a fresh 6-month clock:

- Primary KPI improves but misses target, with the leak localized to a fixable stage (most likely device→transmission or coverage→referral).
- Acute arm succeeds (engagement strong, readmissions down) but the lifetime handoff fails. This is the predictable failure mode: the urgent work crowds out the durable work. Reshape toward making the problem-list write and referral automatic at day 42 rather than navigator-dependent.
- Equity ratio below 0.85 with identified cause — language, device access, coverage, rurality.
- Coverage lapse is the dominant blocker. Reshape toward eligibility intervention; this may also be an advocacy finding worth escalating above the service line.
- Handoff detection below 70% — fix measurement before judging the intervention.

**Cap: two reshape cycles.** A third means the mechanism does not work in this environment.

## Abandon criteria

- Primary KPI **<+5 percentage points after 12 months** with no localizable, fixable defect.
- **Equity ratio worsens** versus baseline and is not corrected within one reshape cycle. Non-negotiable — this pathway exists substantially to close a documented disparity.
- Any **confirmed patient harm** attributable to twin-driven surveillance or triage that is not fully mitigable by design change.
- **42-day readmission increases** and remote surveillance is implicated.
- Device logistics or coverage gaps cannot be resolved and the organization will not fund resolution. Document as an honest organizational abandon, not a twin failure — the retrospective should say so plainly.
- Handoff completion cannot be detected above 60% after remediation. Unmeasurable is unfundable.

**Immediate suspension triggers** — do not wait for a review cycle:
- Any missed or delayed BP escalation attributable to the twin.
- Confirmed PHI exposure, including any cross-contamination with the infant record.
- Systematic non-enrollment of an identifiable population discovered mid-pilot.
- Device malfunction or systematic measurement error affecting clinical decisions.

## Documentation and communication

- Every decision point produces a one-page decision record: prediction, result, decision, change, owner, next review.
- Records are appended to the governance file and retained for abandoned use cases. The abandon records are the proof that the stop rules are real.
- **Abandon means an orderly wind-down with explicit clinical handoff.** Every woman currently in the acute surveillance window transitions to standard postpartum protocol with documented notification to her and her obstetric team. Devices are recovered or written off deliberately. No silent decommissioning in a safety pathway — this is the single highest-risk aspect of stopping this twin.
- Lessons learned written back as `retrospective.md` in this folder.
