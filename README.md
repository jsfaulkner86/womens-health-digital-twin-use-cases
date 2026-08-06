# Women's Health Digital Twin Use Cases

Catalog of healthcare digital twin use cases in women's health, focused on chronic conditions. Each use case states the problem it solves, the single decision it supports, the KPI it must move, and the criteria for keeping, reshaping, or abandoning it.

Women drive roughly 80% of household healthcare decisions. Every use case here is framed around a decision someone actually has to make — not around what data happens to be available.

## Why this catalog exists

Most digital twin material in healthcare stops at capability: what a twin *could* model. That is not the decision maker's problem. Their problem is: *which one problem does this solve, how will I know in twelve months whether it worked, and when am I allowed to stop paying for it?*

This catalog answers those questions per use case. It is a decision instrument, not a landscape survey.

## Who this is for

- **Service line leaders** — OB/GYN, cardiology, primary care, pain medicine — deciding where to place a twin pilot.
- **Digital, AI, and data leaders** and governance committees setting autonomy, oversight, and stop rules.
- **Product and clinical teams** building women's health digital twin solutions who need a defensible evaluation design before they build.

## How the catalog is organized

Use cases are grouped by condition cluster. Each lives in its own folder with the same five documents, so any two use cases can be compared side by side in a steering committee without translation.

| File | Answers |
|---|---|
| `problem.md` | What problem, whose decision, where in the journey, why it matters |
| `twin-thread.md` | What state the twin holds, what data feeds it, how it appears in workflow, what we're assuming |
| `kpis.md` | One primary KPI, two secondary, baselines, targets, funnel instrumentation |
| `evaluation.md` | Instrumentation, review cadence, and explicit keep / reshape / abandon thresholds |
| `governance.md` | Autonomy level, PHI posture, equity guardrails, controls, promotion criteria |

Reusable blanks live in [`templates/`](./templates). Start there for a new use case.

## Use case index

| Use case | Problem it solves | Primary KPI | Status |
|---|---|---|---|
| [Endometriosis → CVD risk](./endometriosis-cvd-risk) | Elevated long-term cardiovascular risk in women with endometriosis is documented in the literature but never carried across the service line boundary into prevention decisions | Prevention pathway enrollment within 180 days of flag | **Exemplar — complete** |
| [Hypertensive pregnancy → lifetime CVD](./hypertensive-pregnancy-cvd-risk) | A hypertensive disorder of pregnancy is the strongest early cardiovascular risk signal most women will ever receive, and it is discarded when obstetric care ends at six weeks | Prevention handoff completed + durable risk flag within 180 days of delivery | **Drafted — complete** |
| Chronic pelvic pain pathway | Patients loop through imaging, referrals, and procedures for years without reaching multidisciplinary care; no one owns detecting the loop | Time to multidisciplinary care enrollment | Planned |
| Gestational diabetes → type 2 diabetes | Same handoff cliff as HDP, different endpoint; postpartum glucose screening is recommended and routinely missed | Postpartum glucose screening completion within 12 weeks | Planned |
| PCOS → cardiometabolic risk | Cardiometabolic screening and intervention are inconsistently applied in PCOS despite established risk | Appropriate metabolic screening and intervention rate | Planned |
| Breast cancer survivorship | Cardiotoxicity, bone health, and menopause management fragment across oncology, cardiology, and primary care after active treatment ends | Survivorship care plan completion with owned follow-up | Planned |
| Perimenopause / midlife transition | Symptom burden and cardiometabolic change are managed reactively across disconnected visits | Documented management plan and symptom-burden improvement | Planned |
| Chronic anemia / heavy menstrual bleeding | Repeat presentations treated episodically without escalation to definitive workup | Time to definitive diagnosis and treatment | Planned |
| Autoimmune conditions in women | Female-predominant autoimmune disease carries cardiovascular and pregnancy risk that is rarely coordinated | Coordinated risk-management plan rate | Planned |

Status values: **Planned** → **Drafted** → **Complete** → **Piloted** → **Retired (with retrospective)**.

**Where to start reading.** [`endometriosis-cvd-risk`](./endometriosis-cvd-risk) is the cleanest illustration of the catalog structure. [`hypertensive-pregnancy-cvd-risk`](./hypertensive-pregnancy-cvd-risk) is the stronger pilot candidate: a documented 2–4x risk multiple, a 14-day leading indicator that gates the pilot before you spend a year on it, an existing reimbursement path, and a disparity that peer institutions have already demonstrated is closable.

Retired use cases stay in the catalog with their retrospective attached. The abandon records are the evidence that the stop rules are real.

## Design principles

1. **One decision per use case.** If a twin supports three decisions, it is three use cases, or it is a platform pitch.
2. **One primary KPI.** Two or more and the quarterly review becomes a data tour instead of a decision.
3. **Stop rules before go-live.** Thresholds set after seeing the data are rationalizations, not criteria.
4. **Absolute floors, not just relative gains.** 2% → 3% is a 50% relative improvement and operationally meaningless.
5. **Equity is a guardrail, not a report-out.** A gain purchased by widening a disparity is a fail.
6. **Simplest thing that works.** A transparent composite score beats a learned model when the learned model costs you the governance conversation.
7. **Sandbox, then live.** Digital twin sandboxes and the live EHR are separate environments with explicit, written promotion criteria.
8. **Replayability is non-negotiable.** If you cannot reconstruct why a patient was flagged, you cannot defend it in a governance or adverse-event review.
9. **Measurability before intervention.** If you cannot detect the outcome on a retrospective cohort, the pilot cannot conclude anything. Prove the measurement first.
10. **A twin's job is to carry a signal across a boundary no human role owns.** Where an existing role already owns the handoff, you have a process problem, not a twin problem.

## Ecosystem

This catalog defines *what to build and how to judge it*. The implementation layers live in adjacent repos:

- **`ehr-mcp`** — interoperability and agentic tool layer for twins reading and writing Epic / FHIR.
- **`clinical-rag-agent`** — guideline and evidence grounding for recommendations and clinician-facing explanations.
- **`clinical-triage-agent`** — symptom-driven intake, a candidate entry point for cohort identification.
- **`womens-health-reimbursement-agent`** — coding and reimbursement pathways for the cross-service-line financial case.
- **`healthtwin-digital-avatar`** — patient-facing surface for twin explanation and engagement.

## Contributing a use case

1. Copy the five files from [`templates/`](./templates) into a new `condition-outcome/` folder.
2. Fill `problem.md` first. If you cannot name the single decision in one sentence, stop — the use case is not ready.
3. Set the primary KPI and the stop rules before writing anything about architecture.
4. Add the row to the use case index above with the problem it solves.
5. Cite evidence inline with links. Claims about risk elevation, prevalence, or delay need a source.

## Maintainer

Maintained by [John Faulkner](https://www.linkedin.com/in/johnathonfaulkner), The Faulkner Group — agentic AI and digital twin architecture for women's health technology. Clinical review in partnership with Dr. Nicole Faulkner, OB/GYN.
