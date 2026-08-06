# Evaluation: keep, reshape, or abandon

**Use case:** Endometriosis → long-term cardiovascular risk

Criteria are set **before** go-live and recorded in the governance minutes. Thresholds negotiated after seeing the data are not stop rules; they are rationalizations.

## Instrumentation plan

**Data taps**
- Epic: `Condition`, `Procedure`, `Observation`, `MedicationRequest`, `Encounter`, `ServiceRequest` (referrals), scheduling and attendance records.
- Twin event log: every state transition with timestamp, triggering event, prior state, new state, actor (system or named user).
- Worklist telemetry: open, dismiss, act, time-in-view, outcome disposition.
- Patient-facing message telemetry: sent, opened, link-clicked, scheduled.

**Logging and tracing**
- Structured event log for every twin evaluation: inputs referenced (resource IDs, not values, where identifiers are avoidable), rule version, tier output, drivers.
- Trace ID propagated from data ingestion through recommendation to clinical action, so a single flag can be reconstructed end to end.
- Model/rule version pinned per evaluation. Retrospective analysis is worthless if you cannot tell which logic produced a given flag.
- PHI discipline: identifiers excluded from application logs and traces; join to identity happens only inside the covered data environment.

**Dashboards** — as specified in [`kpis.md`](./kpis.md): monthly operational, quarterly decision-grade, plus the one-page exec view.

**Comparison design**
- Minimum acceptable: pre/post with a retrospective 12-month baseline on an identically defined cohort, plus a concurrent non-flagged low-risk comparison group to detect secular trend.
- Preferred: stepped-wedge rollout by clinic or pod. Materially stronger causal claim, minimal added operational cost, and it gives you an internal control when someone asks whether the improvement was just the new navigator.

## Review cadence

| Cadence | Forum | Decision authority |
|---|---|---|
| Monthly | Operational huddle (navigator lead, analyst, GYN champion) | Tune thresholds, volume, outreach scripts |
| Quarterly | Service line + cardiology + AI governance | Formal keep / reshape / abandon call |
| Ad hoc | Governance committee | Immediate suspension on safety or equity trigger |

First formal decision point: **month 6** (interim, reshape-or-continue only). Binding decision point: **month 12**.

## Keep criteria

All of the following, at month 12:

- Primary KPI improves by **≥50% relative and ≥5 percentage points absolute** over measured baseline.
- **S1 equity ratio ≥0.80** and no stratum worse than its own baseline. An overall gain purchased by widening a disparity is a fail, not a trade-off.
- **S2 dismissal rate <35%** at week 12 and sustained, with trust survey median ≥3 of 5.
- **Cohort precision ≥0.80** on chart review — the flags are landing on patients who actually have endometriosis.
- **Zero unresolved safety events** attributable to the twin (missed higher-acuity finding, inappropriate reassurance, incorrect med implication).
- Flag volume sustainable within prevention capacity without generating unbookable referrals.

## Reshape criteria

Any of these means iterate rather than kill — with a **named hypothesis, a changed variable, and a new 6-month clock**:

- Primary KPI moves but misses target, and funnel analysis localizes the leak to a fixable stage (e.g. outreach reached only 40% of patients → fix outreach, not the risk model).
- Dismissal rate high but trust survey indicates the *threshold* is wrong rather than the concept — tighten to high-risk-only.
- Screening completion rises but prevention attendance does not — the referral and scheduling path is the defect, not the twin.
- Equity ratio below 0.80 with an identified, addressable cause (language, transportation, clinic distribution).
- Cohort precision below 0.80 due to coding noise — improve case definition before touching the risk logic.

**Reshape is capped at two cycles.** A third reshape means the mechanism does not work in this environment; convert to abandon.

## Abandon criteria

Any of these, and the twin is sunset:

- Primary KPI shows **<10% relative improvement after 12 months** with no localizable, fixable funnel defect.
- Equity ratio **worsens vs. baseline** and cannot be corrected within one reshape cycle.
- Any confirmed **patient harm** attributable to twin-driven recommendation that is not fully mitigable by design change.
- Dismissal rate **>60%** sustained at month 6 — clinicians have already decided, and continuing burns credibility for every future twin.
- Prevention capacity cannot absorb the pathway, and the organization will not fund it. This is an honest abandon, not a failure of the twin, and should be documented as such.
- Cohort cannot be identified with precision ≥0.60 — the data substrate is not there yet.

**Immediate suspension triggers** (do not wait for a review cycle): confirmed safety event, PHI exposure incident, or discovery that the twin is systematically flagging one population and not another.

## Documentation and communication

- Every decision point produces a **one-page decision record**: what we predicted, what happened, what we decided, what changes, who owns it, next review date.
- Decision records are appended to the governance file and retained even for abandoned use cases. The abandon records are the most valuable artifacts in this catalog — they are the proof that the stop rules are real.
- Abandon means: worklist retired, flags removed from encounter surface, patients already in the pathway explicitly transitioned to standard care with documented handoff. No silent decommissioning that leaves patients mid-pathway.
- Lessons learned are written back to this repo as a `retrospective.md` in the use case folder, so the next twin does not relearn the same lesson.
