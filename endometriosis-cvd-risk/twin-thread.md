# Digital twin and digital thread

**Use case:** Endometriosis → long-term cardiovascular risk

## Twin description

**Whose twin:** the patient. One twin instance per woman in the endometriosis cohort.

**Core state the twin maintains:**

| State | Type | Meaning |
|---|---|---|
| `case_confidence` | 0.0–1.0 | Probability this patient truly has endometriosis, given conflicting coding, surgical, and claims evidence |
| `cvd_risk_tier` | low / elevated / high | Composite lifetime-oriented risk tier, not a 10-year ASCVD score |
| `risk_drivers` | list | The specific contributing factors (BP, lipids, early menopause / oophorectomy, smoking, BMI trajectory, inflammatory burden proxies) |
| `pathway_stage` | not-flagged / flagged / referred / seen / in-program / declined / lost | Where she actually is in the prevention pathway |
| `last_action` | event | What the twin last recommended, to whom, and what happened |
| `staleness` | days | Time since last meaningful data update — drives whether the twin is trusted |

**Update cadence:** event-driven on new relevant data (encounter, lab, vitals, med change, surgical event), with a nightly sweep for staleness and threshold re-evaluation. Not real-time; there is no clinical need for sub-daily latency and it inflates cost and audit volume.

**Explicit design choice:** the twin holds *state plus provenance*, never a bare score. Every tier assignment carries the drivers and the source events that produced it. A score without provenance will not survive contact with a skeptical cardiologist.

## Digital thread

Streams that stitch into the twin over time:

- **EHR / Epic (primary).** Problem list and encounter diagnoses (N80.x), surgical and procedure history (laparoscopy, hysterectomy, oophorectomy), medication history (GnRH agonists, hormonal therapy), vitals and BP series, lipid panels, A1c, BMI trajectory, tobacco history. Accessed via FHIR R4 — `Condition`, `Procedure`, `Observation`, `MedicationRequest`, `Encounter`.
- **Claims / payer feed (where available).** Fills the gap where care happened outside the system; important because endometriosis patients are high-utilizers across multiple organizations.
- **Patient-reported outcomes.** Symptom burden, pain interference, and — critically for this pathway — whether she actually attended the prevention referral.
- **Wearables (optional, later phase).** Resting HR, HRV, activity, sleep. Useful for engagement and adherence signal; **not** load-bearing for the risk tier in v1. Do not make the pilot depend on device data.
- **SDOH and access data.** Geography, insurance status, transportation and appointment-availability proxies — used to detect and correct inequitable flagging, not to down-weight anyone's risk.

**Thread integrity requirement:** every state transition in the twin must be reconstructible from the thread. If you cannot replay how a patient became `high`, you cannot defend the flag in a governance review or an adverse-event review.

## Interaction with clinical workflows

The twin surfaces in three places, in descending order of clinical value:

1. **Registry / worklist (primary surface).** A managed list of flagged patients, worked by a nurse navigator or population health coordinator between visits. This is where the pathway actually converts, because it does not depend on a physician having spare attention during a 15-minute visit.
2. **In-encounter summary (secondary).** One line in the gynecology encounter: risk tier, top two drivers, recommended action, and a single-click order/referral. No modal, no interruptive alert. Interruptive alerting in a non-urgent risk pathway is how you get the twin turned off.
3. **Patient-facing message (tertiary).** Plain-language explanation of why cardiovascular screening is being recommended, with a scheduling link. This is where the ~80%-of-decisions framing becomes operational rather than rhetorical.

**Action routing:** flag → nurse navigator queue → outreach → prevention referral order → scheduling → attendance confirmation written back to twin state. The write-back is the part teams skip and the reason most of these pathways cannot prove impact.

## Assumptions and constraints

**Assumptions that must be tested, not asserted:**
- A1: Structured endometriosis data identifies ≥70% of the true cohort. *Test:* chart review of 100 records against surgical and pathology ground truth.
- A2: Flagged patients accept prevention referral at ≥40%. *Test:* measured directly in pilot; if it lands below ~25%, the mechanism is broken and the twin should be reshaped toward patient-facing engagement rather than clinician flagging.
- A3: Prevention capacity can absorb the flagged volume. *Test:* model expected flag volume against actual open slots *before* go-live.
- A4: Clinicians find the flag credible. *Test:* structured trust survey at weeks 4 and 12, plus dismissal-rate telemetry.

**Constraints:**
- **PHI.** This twin operates on live PHI in Epic. Cohort build, risk computation, and worklist generation stay inside the covered environment. No identifiers into third-party LLM APIs; any generative component (e.g. drafting patient-facing explanations) runs against de-identified or templated input, or against a BAA-covered endpoint with logging that excludes identifiers.
- **Sandbox first.** All development and evaluation runs against synthetic or de-identified cohorts in a digital twin sandbox. Promotion to live Epic requires the criteria in `governance.md`.
- **Model class.** Risk tiering should start as a transparent rule/score composite, not a learned model. A learned model here buys marginal discrimination and costs you the governance conversation.
- **No autonomous action.** The twin recommends and routes; a human orders. Non-negotiable for v1.

## Relationship to other systems

- **`ehr-mcp`** — the interoperability and tool layer through which the twin reads FHIR resources and writes back pathway state.
- **`clinical-rag-agent`** — supplies guideline and evidence grounding for the recommendation text and for clinician-facing "why this patient" explanations.
- **`clinical-triage-agent`** — adjacent, not upstream; useful if symptom-driven intake becomes an entry point for cohort identification.
- **`womens-health-reimbursement-agent`** — models the cross-service-line financial case and coding path for the prevention arm.
- **`healthtwin-digital-avatar`** — candidate patient-facing surface for the explanation and engagement layer once the clinical pathway is proven.
