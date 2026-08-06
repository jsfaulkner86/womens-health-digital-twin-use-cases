# Digital twin and digital thread

**Use case:** Hypertensive disorders of pregnancy → lifetime cardiovascular risk

## Twin description

**Whose twin:** the patient. Instantiated automatically at delivery discharge for every qualifying HDP case — no human has to remember to create it. Auto-instantiation is the design decision that most determines whether this works.

**Core state:**

| State | Type | Meaning |
|---|---|---|
| `hdp_subtype` | gestational HTN / preeclampsia / preeclampsia with severe features / eclampsia / HELLP / superimposed | Severity drives surveillance intensity and long-term risk weighting |
| `case_confidence` | 0.0–1.0 | Probability of true HDP given coding, BP values, magnesium and antihypertensive administration |
| `bp_trajectory` | timeseries + trend | Postpartum BP series from any source (clinic, home device, remote); the acute safety signal |
| `surveillance_stage` | not-started / device-issued / actively-reporting / lapsed / resolved | Where she is in the 6-week acute window |
| `handoff_stage` | none / diagnosis-written / referral-placed / scheduled / **completed** / declined / uninsured-blocked / lost | Where she is on the long-term pathway |
| `coverage_status` | active / lapsing-in-N-days / lapsed / unknown | Gates whether a referral is actionable at all |
| `risk_carry_flag` | boolean + provenance | Durable problem-list marker that survives the obstetric episode |
| `days_since_delivery` | int | Drives every timing rule |
| `staleness` | days | Time since last BP or contact; the primary early-warning metric |

**Two clocks, one twin.** The acute clock (0–42 days: BP safety, readmission avoidance) and the lifetime clock (6 weeks–forever: CVD prevention handoff) run simultaneously with different owners and different escalation paths. Conflating them is the most common design error in postpartum programs — the acute work is urgent and crowds out the durable work, and the durable work is the entire point of the twin.

**Update cadence:** near-real-time during the acute window (new BP reading triggers immediate evaluation, because this one is a safety pathway); daily sweep during the lifetime window.

**Terminal state:** `handoff_stage = completed` plus `risk_carry_flag` written to the problem list. Not "referral placed." A placed referral is a task, not an outcome.

## Digital thread

- **Epic / obstetric record (primary).** Delivery summary, `Condition` (O11–O16), BP `Observation` series antepartum through discharge, `MedicationAdministration` for magnesium sulfate and IV antihypertensives, labs (platelets, creatinine, LFTs, protein/creatinine ratio), gestational age at onset and at delivery.
- **Home BP monitoring / remote patient monitoring.** Device-transmitted readings during the 6-week window. Given that in-person postpartum BP visits are poorly attended and drive disparate outcomes, while home monitoring improves ascertainment and reduces racial disparities ([Circulation](https://www.ahajournals.org/doi/10.1161/CIRCULATIONAHA.124.073302)), this stream is load-bearing, not optional.
- **Coverage and eligibility feed.** Medicaid status and postpartum coverage end date. This is a clinical data element in this pathway, not an administrative one.
- **Ambulatory and primary care encounters.** Internal encounters plus, where available, HIE or claims to detect handoff completion outside the delivering organization.
- **Patient-reported.** Symptom checks (headache, visual change, epigastric pain) during the acute window; attestation of outside primary care attendance during the lifetime window.
- **SDOH and access.** Transportation, language, childcare, distance. Used to select outreach modality and to detect inequity — never to deprioritize.

**Thread integrity:** every stage transition reconstructible from source events. Handoff completion specifically must carry evidence — encounter ID, claim, or documented attestation — because this is the number leadership will be asked to defend.

## Interaction with clinical workflows

**Acute window (days 0–42)** — owner: postpartum nurse navigator / maternal telehealth
1. **Discharge instantiation.** Twin created, home BP device or program enrollment issued *before discharge*. Enrollment at discharge, not by callback — the callback is where the disparity enters.
2. **Remote BP worklist.** Readings stream in; twin escalates on threshold breach or on `surveillance_stage = lapsed`. Lapse is treated as a finding, not an absence of data.
3. **Escalation path.** Threshold breach routes to the standard urgent obstetric pathway immediately. The twin never holds an acute BP finding in a population health queue.

**Lifetime window (6 weeks onward)** — owner: population health / prevention coordinator
4. **Durable risk write.** At the postpartum visit or at day 42, the twin writes the HDP diagnosis to the problem list as a cardiovascular risk modifier with provenance. This single write is the highest-value, lowest-cost action in the entire pathway.
5. **Coverage-aware handoff.** If coverage is active, generate the prevention referral with a scheduling path. If lapsing or lapsed, route first to eligibility/financial counseling — a referral into a coverage gap is a manufactured no-show.
6. **Confirmation and re-contact.** Twin tracks to completion, with defined re-contact attempts at 3 and 6 months, and flags terminal failure to the worklist.
7. **Patient-facing.** Plain-language framing at discharge and again at 3 months: what the pregnancy revealed about her heart, what to do, scheduling link. This is a decision-maker communication, not a discharge instruction.

## Assumptions and constraints

**Assumptions that must be tested:**
- **A1:** BP values, magnesium administration, and antihypertensive orders identify ≥85% of true HDP cases, outperforming diagnosis codes alone. *Test:* chart review of 100 deliveries against clinical ground truth.
- **A2:** Device issuance at discharge yields ≥60% actively-reporting at day 10 — a large improvement over the ~13.7% in-person attendance benchmark ([Journal of Women's Health via Healio](https://www.healio.com/news/womens-health-ob-gyn/20211129/most-atrisk-women-skip-blood-pressure-screenings-after-giving-birth)). *Test:* measured directly; below ~35% and the modality is not the fix.
- **A3:** A durable problem-list flag plus a coverage-aware referral produces meaningfully higher 6-month prevention attendance than the ~18% observed baseline ([JAHA](https://pmc.ncbi.nlm.nih.gov/articles/PMC7660757/)). *Test:* the primary KPI.
- **A4:** Handoff completion is detectable for ≥70% of the cohort. *Test:* run detection against a retrospective cohort before go-live — if completion is unmeasurable, the pilot cannot conclude anything.
- **A5:** Prevention capacity can absorb the volume. *Test:* project annual HDP deliveries against open slots before go-live.

**Constraints:**
- **PHI, live, plus a newborn record adjacency.** Full covered-environment handling; strict separation from the infant record. No identifiers to third-party model APIs.
- **Safety classification.** The acute arm influences an urgent clinical pathway. It gets a higher review tier than the lifetime arm and must be designed fail-safe: if the twin is down, standard postpartum protocol continues unchanged and staff are notified.
- **No autonomous action.** Recommend, route, escalate. A human orders, a human calls.
- **Transparent logic.** Subtype and severity rules, not a learned model. The clinical logic here is already codified in guidelines; a model adds governance burden and no discrimination.
- **Device logistics are a real constraint.** Cuff inventory, validated devices for larger arm circumference, and connectivity are pilot-blocking operational issues. Solve them before writing code.

## Relationship to other systems

- **`ehr-mcp`** — FHIR read of obstetric and BP data, and the problem-list write-back that makes the risk durable.
- **`clinical-triage-agent`** — natural fit for symptom triage during the acute postpartum window.
- **`clinical-rag-agent`** — guideline grounding (ACOG postpartum BP surveillance, AHA hypertension-in-pregnancy statement) for recommendation text.
- **`womens-health-reimbursement-agent`** — RPM and postpartum care coding pathways; this use case has a genuinely fundable billing model, which the endometriosis pathway does not.
- **`healthtwin-digital-avatar`** — patient-facing explanation layer for the "what your pregnancy revealed about your heart" conversation.
- **[`endometriosis-cvd-risk`](../endometriosis-cvd-risk)** — same architectural pattern (risk signal stranded at a service line boundary), cleaner cohort, faster readout. Shared prevention-pathway capacity means the two must be capacity-planned together, not separately.
