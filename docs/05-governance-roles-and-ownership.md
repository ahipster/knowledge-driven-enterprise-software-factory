# 05 — Federated governance, capability roles, ownership and RACI

**Status:** Proposed operating model. Role names are logical and should map to local HR/organizational roles through time-valid assignments; no particular individual or bank is implied.

## 1. Core governance principle

**Authority follows knowledge semantics and affected scope, not ticket ownership or the person who edits a diagram.**

- Enterprise governance establishes *rules of the game*: ontology core, architectural principles, enterprise policies and exception procedures.
- Capability authorities own the *meaning, commitments and changes* within their business mandate.
- Information/decision/process/control authorities retain their specific domain rights even when the change is sponsored by another capability.
- Delivery teams own *how to realize* approved semantics, while technical designs and contracts may still require domain review.
- Independent risk/security/compliance roles review *designated control-relevant changes*, not all edits.
- Agents can draft, validate, summarize, simulate and request; they are not accountable authorities.

A work item can bundle many knowledge revisions; each revision needs precisely its own resolved accountability and materiality checks. This limits bureaucracy without making approvals meaningless.

## 2. Governance hierarchy and bounded ownership

~~~mermaid
flowchart TD
  E[Enterprise architecture and policy governance] --> F[Federated domain and capability governance]
  F --> C1[Capability A profile and accountable owner]
  F --> C2[Capability B profile and accountable owner]
  C1 --> P[Process ownership]
  C1 --> D[Decision / policy ownership]
  C1 --> I[Information / data ownership]
  C1 --> S[Business service / logical interface ownership]
  C1 --> R[Solution / realization / operations]
  C2 --> X[Shared information or contract]
  I --> X
  S --> X
  X --> G[Coordinating authority + affected co-approvers]
  E --> G
~~~

Hierarchies may be matrix-based and many-to-many; the figure represents governance relationships, not strict managerial reporting lines.

### Ownership resolution

For each element:
1. Determine **home scope** (enterprise, value stream, business domain, capability, bounded context, application and jurisdiction).
2. Assign one **accountable role assignment** for its authoritative meaning *in that scope*.
3. Declare **stewards**, **contributors**, **consulted stakeholders** and **mandatory co-approvers** by element type/risk.
4. If the element is shared, register **coordination authority** and explicit conflicting-semantics escalation, not fake duplication.
5. Evaluate time validity and delegation, classification and purpose at decision time.
6. Resolve ownership from the **affected facts** (e.g. information definition vs API transport) rather than only the parent document.

For example, changing an “Address” concept's business meaning requires the information owner, even when a payments team changes the API that carries it. Altering an HTTP retry header may require only the API owner and applicable platform standards reviewer.

## 3. Governance-level roles

| Logical role | Owns/decides | Cannot do without another authority |
|---|---|---|
| Enterprise Architecture Authority | Enterprise principles, meta-model policy, target coherence, architectural exceptions | Unilaterally redefine capability-local concepts or approve risk acceptance outside mandate |
| Enterprise Ontology Custodian | Ontology namespace/schema versions, common link vocabulary, migration | Override authoritative business meaning |
| Capability Owner | Purpose, capability boundaries, outcomes, priorities, service-level commitments | Change another capability's data meanings or independent controls |
| Value Stream Owner | End-to-end outcome/coordination, shared journey | Approve local semantics without delegated rights |
| Domain/Business Architect | Structural coherence, process/service/capability cross-mapping, target architecture | Approve data/security policies by inference |
| Process Owner | Process boundary, variants, control placement, operational responsibilities | Alter independent DMN rules without Decision Owner |
| Decision/Rule Owner | Rule meaning, decision tables, effective dates and thresholds | Publish unapproved regulatory exceptions |
| Information Owner / Data Owner | Semantic definitions, master-data policy, SOR, sharing policy, attribute meaning | Approve application deployment details outside domain |
| Data Steward | Data quality, dictionary and mapping curation, evidence, delegated reviews | Exercise ungranted information ownership |
| Business Service Owner | Offering, promise, consumer contract and lifecycle | Change other capabilities' business rules |
| API / Logical Interface Owner | Logical operation, compatibility, technical API contract, consumer evolution | Redefine business semantics or security exceptions |
| Solution Architect | Realization design, dependencies, quality attributes and technical alignment | Treat as-built code as automatic approved enterprise truth |
| Product/Change Owner | Intent, hypothesis selection, backlog, committed scope and tradeoffs | Override per-element semantic authority |
| Engineering Lead | Code, builds, engineering evidence and developer controls | Self-approve normative semantic modification |
| SRE / Operations Owner | Observed behavior, reliability, incident, runtime control and drift triage | Recast observed defects as approved business policy |
| Security Architecture / IAM Owner | Identity, authZ standards, threat controls and exception review | Grant business data use rights not owned by them |
| Privacy / Data Protection | Personal-data processing requirements, minimization, privacy review | Override law/organizational mandates via tool |
| Risk and Compliance Control Owner | Control obligations, risk acceptance within delegation | Approve unrelated design specifics |
| Assurer / Independent Reviewer | Assurance strategy, claim/evidence acceptability, gate recommendation | Circumvent mandated independent reviewer rules |
| Platform Operator | Platform uptime, connector integrity and audit infrastructure | Edit content to make dashboards green |
| Agent Supervisor | Agent identity, tool scopes, evaluations, approvals for tool actions | Grant missing business ownership to model |

**Separation of duties (SoD):** policies must test actual actor identity across roles. A developer who also holds a capability-role assignment may still be barred from independently approving their own high-risk ChangeSet. Role overlap alone is not always prohibited; conflicts are defined by policy.

## 4. Artifact-family accountability matrix

Symbols: **A** accountable meaning/decision; **R** responsible editing/maintenance; **C** consulted; **V** independent/mandatory verifier as policy requires. A column may contain multiple R/C but A is resolved per element/scope, not globally. The table is the *default rule*, not a fixed corporate RACI.

| Artifact / knowledge item | A | R | Typical C / V |
|---|---|---|---|
| Enterprise principles / ontology policy | EA Authority | Enterprise Architect / Ontology Custodian | Capability and control representatives |
| Capability charter, boundary, goals, KPIs | Capability Owner | Business Architect | Value Stream/Domain Owners |
| ValueStream and cross-capability journey | Value Stream Owner | Business Architect | Affected Capability Owners |
| ProcessFamily and subprocess boundary | Process Owner | Process Analyst | Business Architect, SRE/control owner |
| ProcessVariant and applicability | Scoped Process Owner | Process Analyst | Decision/Control Owner |
| DMN decision, rule and hit policy | Decision Owner | Business Analyst / Rules Engineer | Process Owner, Risk/Compliance |
| BusinessConcept/invariant/attribute | Information Owner | Data Steward / Domain Analyst | API Owner, Process Owner, Privacy |
| InformationRelationship/state machine | Information Owner | Information Architect | Decision and process owners |
| System of record / data quality policy | Data Owner | Data Architect / Steward | Producer and consumer owners |
| Semantic mapping / context map | Coordinating semantic owner | Two domain architects | Both information authorities (V for identity claims) |
| BusinessService promise | Business Service Owner | Service Architect | Consumer/capability owners |
| LogicalInterface operation meaning | Service/API Owner | API Architect | Information Owner, consumers |
| Technical API/event contract | API Owner | Engineers | Security, producer/consumer representatives |
| Application/technology architecture | Solution Architecture Authority | Solution Architect | EA, Security, Ops |
| Requirement/acceptance criteria | Business/Requirement Owner | Product/Business Analyst | Subject-matter owners, testers |
| ChangeCase, hypothesis selection | Change Sponsor / Product Owner | Change Lead | Affected owners, architect |
| Per-element semantic ChangeSet | Each affected semantic authority | Proposer | Material risk/control reviewers |
| Exception to normative control | Delegated Risk/Control Authority | Requester + Risk Analyst | EA, Security, affected owner |
| Test plan/test evidence | Quality/Test Owner | Engineer/Test Lead | Requirement Owner/Assurer |
| Assurance claim/argument | Designated Assurer | Assurance Analyst | Evidence Owner, Control Owner |
| Release/deployment manifest | Release Authority | Delivery/Ops | Security, assurance gate |
| Observation and drift case | Operations Owner (triage) | SRE/Monitoring | Relevant semantic owner for disposition |
| Source/model mapping fidelity | Source Model Owner | Adapter/Integration Team | Semantic owner of mapped result |
| Agent policy and tool bundle | Platform/Agent Governance | Agent Engineer | Information Security, knowledge owners |
| Audit event retention/access | Audit/Records Policy Owner | Platform Team | DPO, Security |

When a ChangeSet spans several rows, approvals are a **set of scoped decisions** against an exact frozen diff, not one broad “architecture approved” flag. The same human can satisfy multiple required roles only where SoD permits.

## 5. Capability Knowledge Profile contract

**Every governed capability must have one current profile**, separate from any generated view:

~~~yaml
capabilityId: urn:kdesf:capability:customer-onboarding
profileRevision: v3
homeDomainId: urn:kdesf:domain:retail-banking
accountability:
  capabilityOwnerRole: urn:kdesf:role:capability-owner-onboarding
  enterpriseArchitectRole: urn:kdesf:role:enterprise-architect
  processOwnerRole: urn:kdesf:role:onboarding-process-owner
  informationOwnerRoles:
    - urn:kdesf:role:party-information-owner
  apiServiceOwnerRoles:
    - urn:kdesf:role:onboarding-service-owner
governance:
  classificationPolicy: urn:kdesf:policy:information-v1
  artifactProfile: urn:kdesf:policy:regulated-customer-journey-v1
  assurancePolicy: urn:kdesf:policy:high-impact-change-v1
  reviewCadenceDays: 90
  localDelegationRules: urn:kdesf:policy:delegation-v1
scope:
  jurisdictions: [SE, EU]
  segments: [retail]
  businessOutcomes: [urn:kdesf:outcome:customer-established]
  processFamilies: [urn:kdesf:process-family:onboarding]
  informationDomains: [urn:kdesf:information-domain:party]
  consumedCapabilities: [urn:kdesf:capability:identity-verification]
  referenceMappings: [urn:kdesf:ref:bian-14:mapping-example]
requiredKnowledge:
  - type: CapabilityCharter
    gate: governed-baseline
  - type: ProcessFamily
    when: capabilityRunsProcesses
  - type: BusinessConcept
    when: capabilityHandlesInformation
  - type: LogicalInterface
    when: capabilityExposesOperations
  - type: ControlObligation
    when: regulatedOrMaterialRisk
quality:
  maximumStalenessDays: 90
  evidenceReviewCadenceDays: 90
~~~

This is illustrative YAML, **not** a deployed schema or real bank profile. Actual assignments and local ownership must be verified. Avoid storing personal names in shared metamodel templates.

### Capability readiness bands

- **Discovered:** ID, candidate scope and uncertain owner.
- **Owned:** confirmed capability charter, accountable owner, sensitivity and dependencies.
- **Described:** key processes, information, rules, services and interfaces cataloged proportionately.
- **Governed:** approved baseline, quality checks, scoped change approvals and control obligations.
- **Assured:** realizations, verification and release evidence are traceable.
- **Observed:** runtime conformity, drift triage and ongoing freshness are operational.

These are *custom maturity bands*, not claimed TOGAF/BIAN/Essence maturity levels. Readiness is per concern; capabilities can be mature in architecture and immature in evidence.

## 6. Approval resolution policy

~~~text
INPUT:
  changedElementTypes, before/after scopes, materiality, affected links,
  lifecycle stage, policy effective time, risk/classification, actor,
  valid role assignments, source provenance, affected control obligations

1. Compute semantic diffs and change class:
   cosmetic | editorial | semantic | control-relevant | breaking |
   data-sensitive | architectural | production-critical.
2. Expand impact only along configured typed/dependency links.
3. Resolve accountable semantic owner(s) for each changed element.
4. Add cross-capability co-approvers for changes to shared commitments.
5. Add required specialist reviewers (risk/privacy/security/EA) by policy.
6. Require stronger controls when source mapping confidence is low.
7. Apply delegation, materiality thresholds, approval batching and SoD.
8. Freeze exact diff hash + policy revision + expected element revisions.
9. Collect decisions; invalidate if semantics/impact/revision or authority changes.
10. Promote only when all required decisions valid at effective time.
OUTPUT:
  role/scope/actor requirements, checklist, reason and escalation path.
~~~

**Policy notes:**
- *Editorial:* can be steward-approved when assertions and relationships are unchanged and a semantic check confirms that claim.
- *Semantic:* approved by semantic owner with affected specialist reviewers.
- *Breaking interface:* API Owner + affected consumers under compatibility policy; Information Owner if business meaning changes.
- *Sensitive data/control:* specific control owner + privacy/security reviewer as applicable.
- *Urgent incident remediation:* preauthorized emergency workflow, limited blast radius, post-implementation assurance and retrospective reconciliation; no invisible bypass.
- *Approval changes:* reviewer departure, revoked delegation, expired mandate or modified diff invalidates only decisions that policy determines no longer applicable.

## 7. Cross-capability conflicts and escalation

Disputes can concern:
- same business term with conflicting definitions;
- incompatible rules for shared information;
- dual claims of SOR authority;
- provider/consumer API evolution;
- regulatory policy overriding local process design;
- enterprise architecture principles vs local constraints.

Resolve by: **detect → identify independent domains of meaning → propose translation or reference authority → collect affected owner decisions → escalate unresolved semantic dispute to designated council → record decision, rationale, scope and review date**. Never resolve by silently merging two concepts just because labels match.

## 8. Change and governance ceremonies

- **Monthly capability knowledge review:** readiness gaps, stale/ambiguous items, shared contract version risks and material drift.
- **Change case commitment review:** approve business outcome, boundaries, priorities and unresolved concerns; permit later refinement.
- **Per-element change review:** asynchronous, diff-focused, attributable; batch related decisions with preserved granular scope.
- **Architecture/assurance checkpoints:** model quality, structural consistency, evidence and release readiness based on materiality.
- **Post-release reconciliation:** actual vs intended, lessons learned, closure of drift/waivers.
- **Quarterly enterprise ontology council:** schema/relationship evolution and cross-domain mapping conflicts.

Cadence is configurable. Governance effectiveness is measured by prevented defects, semantic stability, time to approved change and audit quality, **not number of meetings**.

## 9. Delegation and accountability

A delegation record includes grantor, grantee principal/group, delegated role, element types, scope, maximum risk/classification, allowed decision verbs, effective dates, revocation status, SoD constraints and reason. Agent roles may be assigned for *proposal/analysis execution*, never automatically to mandatory human accountable reviewer slots.

**Default fail-closed cases:** no resolved owner; conflicting exclusive owners; reviewer lacks live role/scope; material diff changed after approval; evidence past expiry; unauthorized graph disclosure; unfulfilled mandatory regulatory/control approvals.

## 10. Audit questions this model must answer

1. Who could authorize the customer concept revision on a specific day?
2. What did the approving reviewer actually see (diff, baseline, policy and classification)?
3. Which capabilities, processes and systems inherited an approved change?
4. What was assumed but not yet fully specified when commitment was granted?
5. Which exception justified a release despite a known inconsistency, and when did it expire?
6. Was an agent acting under a valid bounded delegation; what sources and tools did it use?
7. Which facts were known from observation versus approved by the business?
8. Was an independent reviewer actually independent of the proposal author?

See [07 — Security](07-security-and-controls.md) and [06 — Lifecycle](06-change-delivery-assurance.md).
