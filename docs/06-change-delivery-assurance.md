# 06 — Knowledge change, delivery, assurance and reconciliation lifecycle

**Status:** Proposed normative workflow model. This is deliberately *not* a rigid waterfall: stages are resumable, iterative, partially parallel, scoped, and policy gated.

## 1. Separate but related lifecycles

Do **not** collapse all work into a single epic status. Maintain at least four linked state machines:

1. **Opportunity/ChangeCase lifecycle** — whether an intended business outcome is worth pursuing.
2. **Knowledge ChangeSet lifecycle** — whether exact semantic revisions are proposed, reviewed and authoritative.
3. **Delivery/Realization lifecycle** — whether code, configuration, data migration and contracts implement a designated approved target.
4. **Assurance/Operation lifecycle** — whether requirements were verified, a release is acceptable and observed reality remains aligned.

A single ChangeCase may contain multiple ChangeSets, variants, slices, builds, deployments and drift cases. No one-to-one assumptions.

## 2. ChangeCase state machine

~~~mermaid
stateDiagram-v2
  [*] --> Captured
  Captured --> Triaged
  Triaged --> Exploring
  Triaged --> Rejected
  Exploring --> HypothesesEvaluated
  HypothesesEvaluated --> Deferred
  HypothesesEvaluated --> Rejected
  HypothesesEvaluated --> Committed: commitment gate
  Committed --> Designing
  Designing --> Implementing: approved target/spec slices
  Implementing --> Assuring
  Assuring --> Implementing: failed proof/correction
  Assuring --> ReadyForRelease: gate passed
  ReadyForRelease --> Operating: deployed + release recorded
  Operating --> Reconciling: drift or review
  Reconciling --> Operating: discrepancy disposition
  Operating --> Closed: outcome and knowledge closure
  Rejected --> [*]
  Deferred --> Exploring
  Closed --> [*]
~~~

A production feature can be delivered in slices; “Operating” may coexist with “Designing” for later slices. Track per-slice state and aggregate completeness rather than forcing a false linear total order.

## 3. Stage contract: signal → knowledge-aligned operation

| Stage / gate | Entry | Required knowledge | Exit / authorized decision |
|---|---|---|---|
| 0 Capture | Any signal | Source, timestamp, evidence pointer, initial scope and classification | Triaged, duplicate, rejected |
| 1 Explore intent | Problem accepted for investigation | Impact hypothesis, owner/sponsor, outcome, assumptions | Candidate hypotheses |
| 2 Evaluate hypotheses | ≥1 viable hypothesis | Alternatives, falsifiable claims, costs, risk, evidence, rejection rationale | Select, combine, defer or reject |
| **G1 Commitment** | Selected approach | Problem/outcome, high-level boundaries, capability/process/data/control impact, accountable owners, success measure, principal unknowns, budget/timebox if applicable | Commit scope *without pretending detailed designs are complete* |
| 3 Elaborate target | Committed scope | Requirements and acceptance criteria, architecture viewpoints, process/decision/information/interface deltas, threat and migration analyses | ChangeSets ready for scoped review |
| **G2 Knowledge review** | Frozen semantic diff | Expected previous revisions, types, impact/owner resolution, policy checks, cross-capability reviews and effective date | Approve target revisions or request changes |
| 4 Realize slices | Approved target and constraints | Code/config/API/data migration, contract/test plan, traceability, pinned knowledge | Build candidates + evidence |
| **G3 Verification/assurance** | Immutable build candidate | Satisfied requirements, test reports, conformance checks, evidence freshness, deviations, exceptions and independent review | Release recommendation/decision |
| 5 Release | Approved assurance | Signed build, exact baseline/target, environment, rollout/rollback plan, authorized deployment | DeploymentManifest, operation begins |
| 6 Observe | Running deployment | Telemetry, traces, incidents, contracts/config snapshot, quality metrics | Conformance evidence or DriftCase |
| **G4 Reconcile/close** | Evidence from operation | Known deviations triaged, target/implemented/actual align or scoped exceptions accepted | Knowledge reconciliation, change closure, learning |

**Commitment quality bar (G1)** is *fitness to invest responsibly*, not “all requirements detailed.” Record which decisions may be deferred, by whom, until which subsequent gate, and which uncertainty is disqualifying for commitment.

## 4. Hypothesis and alternative model

Each Hypothesis includes:
- asserted value/outcome and falsifiable predicate;
- scope, affected capabilities and target customer/user group;
- predicted effect and measurement plan;
- expected changes to processes, decisions, information, services, controls, architecture;
- assumptions with confidence/uncertainty and source;
- dependencies/feasibility/risks, effort range and reversibility;
- trial/experiment results with quality/provenance;
- selection status, reason, approver and effective time.

Selection is an immutable decision; rejected hypotheses remain queryable. Multiple hypotheses may survive until experiment evidence is sufficient. Agents may produce candidate alternatives, but distinguish analysis from committed decision.

## 5. ChangeSet lifecycle and semantics

~~~mermaid
stateDiagram-v2
  [*] --> Draft
  Draft --> Validating
  Validating --> NeedsFix: failed validation
  NeedsFix --> Draft
  Validating --> ImpactAssessed
  ImpactAssessed --> Submitted: freeze diff
  Submitted --> InReview
  InReview --> ChangesRequested
  ChangesRequested --> Draft: changes invalidate signoffs
  InReview --> Rejected
  InReview --> Approved: all required decisions
  Approved --> Promoted: concurrency + policy + evidence checks
  Approved --> Conflicted: stale base
  Conflicted --> Draft: rebase and reassess
  Promoted --> Superseded
  Rejected --> [*]
  Superseded --> [*]
~~~

**Exact review object:** content hash of canonical operations + affected revision set + baseline ID + policy revision + evaluated impact digest + effective date. An approval covers this object, not simply “the ticket.”

**Scope of atomicity:** a coherent ChangeSet's new revisions promote together within one baseline transaction. If a cross-capability change is intentionally split, maintain explicit dependencies so no unsupported partial baseline becomes “active.”

**Target promotion versus current norm:** a target revision may be approved for a future date or a planned transition state without immediately superseding the active operational normative baseline. Release gates bind to the specifically approved target and its activation policy.

## 6. Requirements elaboration and delivery decomposition

### Three independent structures

1. **Motivation/intention hierarchy**: Strategy → Outcome → Opportunity → ChangeCase, optional groupings.
2. **Semantic refinement graph**: Requirement → refined requirements/constraints/scenarios, many-to-many cross-domain links.
3. **Delivery planning**: Portfolio initiative → epic → feature → story → task/slice, as SAFe-like or other team's work-tracking convention.

A single epic may contain changes to many capabilities; a single normative requirement may be implemented across multiple stories/releases. Never make an epic's closure equivalent to acceptance of an information meaning.

### Refinement-ready statuses

- **Candidate**: understandable problem or objective, unknown solution.
- **Commitment-ready**: testable high-level outcome, scope, owner, risk and unresolved refinement plan.
- **Design-ready**: affected semantics and architecture sufficiently detailed for a bounded design decision.
- **Implementation-ready**: precise interface/decision/process/acceptance details for a build slice.
- **Verification-ready**: expected behavior, evidence method, environment and tolerances fixed.
- **Validated**: observed or tested evidence proves specified version within declared applicability.

A requirement can move back if new evidence invalidates assumptions. Maturity depends on scope and risk and must not be inferred solely from length of prose.

### User scenario structure

**Scenario** contains actor/role, trigger, preconditions, business context, process variant, primary flow, alternative/error paths, input/output business concepts, decisions, interface calls, policies, acceptance examples and expected observations. Test design is derived but not exclusively generated from scenario prose.

## 7. Assurance modeled as claims and evidence

Use a SACM-inspired structure:
- **Claim:** “Release R conforms to approved onboarding eligibility rule D version 5 in environment E.”
- **Argument:** “All applicable paths and decision-table scenarios are covered by integration tests and relevant control evidence.”
- **Evidence:** test run/traces, build digest, exact rule/table revision, test input class, result, timestamps, signer, environment.
- **Context/Assumptions:** customer segment, jurisdiction, load/availability conditions, test environment limitations, excluded scenarios.
- **Assurance Decision:** pass, fail, conditionally accept with valid waiver, or needs review.

**No evidence laundering:** an LLM summary of a passing test is not itself the signed test evidence. An old passing test cannot prove a changed executable or rule version without explicit applicability reasoning.

### Assurance gate checklist

At G3, policy must verify:
1. change is bound to an approved target knowledge baseline;
2. all materially changed normative elements have valid owners/sign-offs;
3. requirement acceptance criteria have linked verification evidence appropriate to risk;
4. contract and schema compatibility assessed for producer/consumer versions;
5. security/privacy/control obligations and SoD satisfied;
6. code/config artifacts are immutable, signed/attested when required and traceable to source;
7. vulnerability/quality checks match enterprise policy;
8. deviations and open defects classified with disposition or valid waiver;
9. deployment/rollback, monitoring and migration plans recorded;
10. explicit release authority decision captured.

A gate verdict is **reproducible** from signed inputs, effective policy and time. Store both the verdict and evaluator/policy version.

## 8. Drift model

**Drift** = material discrepancy between expected approved semantics/controls and some implemented or observed evidence in a declared context. Not every mismatch is an error: the approved baseline may be obsolete, a projection stale, an observation statistically weak, or an exception authorized.

### Drift sources

- API schema or event field differs from approved logical semantics.
- A BPMN path or DMN rule differs from deployed behavior.
- Database allows an invalid information state; runtime rule rejects what normative model permits.
- Unauthorized PII exposure or attribute-level sharing.
- Process control step not observed when applicable.
- Infrastructure deployment differs from approved target/retention/region policy.
- Agent-generated change to code without corresponding approved semantic mutation.
- New external requirement/regulation invalidates existing approved knowledge (knowledge staleness).

### DriftCase fields

Discrepancy ID; expected element revision; implemented artifact/deploy ID; observed evidence/hash/time; scope and affected consumers; severity; confidence and detection method; current owner; deadline; cause hypothesis; linked incident/change; approved exception if any; disposition and closure evidence.

### Dispositions

| Disposition | Required consequence |
|---|---|
| False positive | Explain evidence mismatch/low confidence; retain audit record |
| Fix implementation | Create delivery slice and assurance tests against unchanged approved truth |
| Amend normative knowledge | New ChangeSet with owners and scoped approvals; not retroactive undocumented edit |
| Temporary exception | Specific risk acceptance, compensating controls, expiry and re-review |
| Investigate further | Owner, deadline, additional observation plan |
| Stop/rollback | Restore acceptable state using incident/release authority |

**Convergence invariant:** no DriftCase may be declared “closed” by merely modifying a dashboard field; closure references new proof, correction, authorized semantic change or exception.

## 9. Mapping to AI-DLC and other agent workflows

Adopt **the discipline**, not a forced identical naming convention:
- **Ideation/Initialization** → Signals, ContextManifest, hypothesis research, repository knowledge discovery.
- **Inception** → commitment, requirements, viewpoints, impact, approved knowledge baselines.
- **Construction** → detailed design, code/contracts/tests, semantic conformance checks.
- **Operations** → deployment, observation, drift correction and retrospective knowledge improvement.
- **Guardrails** → source provenance, policy version, agent capability boundaries, gates, human decisions.

Use **Essence/SPEM**-style practices/work products to register method definitions; connect KDESF gates to their outputs. A “change workflow instance” remains separate from “knowledge artifact revision,” even when an agent creates both.

### Agentic task envelope

~~~json
{
  "taskId": "urn:kdesf:agent-task:AT-104",
  "changeCaseId": "urn:kdesf:change:CC-17",
  "allowedActionTypes": ["readAuthorizedKnowledge", "createProposal", "runStaticAnalysis"],
  "deniedActionTypes": ["approveKnowledge", "modifyActiveBaseline", "productionDeploy"],
  "requiredBaselineIds": ["urn:kdesf:baseline:example:v3"],
  "requiredEvidenceRefs": ["urn:kdesf:requirement:EX-12"],
  "humanEscalation": "scoped-owner-review",
  "maxToolCalls": 50,
  "expiresAt": "2026-11-11T00:00:00Z"
}
~~~

The executor must still check tool permissions server-side; a JSON task envelope is descriptive, not a security boundary.

## 10. Change process variations and exceptions

- **Routine editorial update:** semantic check, low-risk delegated review, immutable version.
- **Cross-capability semantic change:** impact propagation, multiple scoped authorities, joint contract tests.
- **Breaking API change:** consumer impact, deprecation and version compatibility policy.
- **Emergency production fix:** preauthorized emergency path; security and business authority post-hoc review under strict deadline; no silent permanent exception.
- **Experimental/prototype work:** sandbox state, never discoverable as normative; promotion requires new formal gate.
- **Retirement/decommission:** reverse dependency checks, data retention, consumer exit, archival/audit proof and correct valid-time transition.
- **Policy change:** changes to *gate policy itself* require separate administrative governance and do not retroactively authorize earlier diffs.

## 11. Reconciliation automation triggers

A scheduler/event system may trigger:
- imported model change without corresponding ChangeSet;
- new main-branch code/release impacting canonical semantic links;
- observed API signature/config drift;
- age-based evidence expiry or exception expiration;
- policy or external standard change that affects mapped assets;
- incident or failed control;
- ontology adapter upgrade yielding different mappings.

These triggers create findings/proposals. No background job receives automatic authority to publish normative knowledge.

## 12. Operational metrics

**Lead:** time from signal to impact visibility; hypothesis cycle time; per-element approval wait; requirement testability before code; % authorized AI context retrievals.  
**Lag:** escaped semantic defects; runtime drift recurrence; time-to-disposition; source/graph mapping error rate; control evidence freshness; production incidents attributable to undocumented knowledge changes.  
**Guardrails:** approval count per material change; independent-review violations; inaccessible knowledge leakage; false-positive drift ratio; emergency exception aging; agent mutation denials and audit completeness.

KPIs must be measured against correct scope and access; avoid exposing restricted elements via aggregate counts.

## 13. Failure and recovery semantics

- Proposer rebases after revision conflict → recompute impact and invalidate affected approvals.
- Source tool unavailable → previously verified snapshot marked stale, mutations requiring fresh source blocked.
- Agent hallucinated a missing artifact → flag unknown, request evidence, not create fictitious approved model.
- Evidence storage unavailable → assurance fails closed; never declare gate passed without evidence persistence.
- Index lag → gates read transaction state; UX displays a projection watermark.
- Rollback of software → update deployed state and relevant assurance/drift status; normative knowledge rollback is an explicit versioned change, not silent Git revert.
