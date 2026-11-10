# 01 — Product requirements document (PRD)

**Product:** Knowledge-Driven Enterprise Software Factory (KDESF)  
**Status:** Proposed reference architecture and product specification, not an implemented system  
**Version:** 0.1.0 · **Date:** 2026-11-10  
**Audience:** Enterprise and solution architects, capability and process owners, data and information stewards, product owners, engineers, risk/compliance/security teams, platform teams, and AI-agent developers  
**Primary setting:** Large, regulated, federated enterprises; examples use a *fictional* European bank.

## 1. Executive decision

Build an **enterprise knowledge operating system** in which business, architecture, information, delivery, assurance, and operational knowledge are durable, typed, versioned, interrelated, governed assets. The software factory consumes **approved, contextually authorized knowledge** to discover opportunities, formulate hypotheses, design and build solutions, prove conformity, release safely, operate them, detect divergence, and propose governed corrections.

**Software is a realization of enterprise knowledge, not its system of record.** Git/code, processes, APIs and running systems remain essential, but they must link to intentions, decisions, capabilities, policies, semantics and evidence. No AI agent is authorized to silently rewrite enterprise truth.

**Critical architectural decisions:**
1. Distinguish **normative/approved**, **proposed/target**, **implemented**, and **observed** states; they may disagree without data loss.
2. Treat **knowledge revisions**, **deliveries**, and **assurance cases** as separate but traceable aggregates.
3. Apply governance to **individual knowledge changes and affected scopes**, not only to epics, pull requests, documents or entire releases.
4. Represent standard models in their *native language and source format*, projecting shared concepts into a canonical typed graph without destroying their original meaning.
5. Support decentralized ownership by **enterprise capability**, including business, information, operational and engineering authorities.
6. Require machine-checkable quality, evidence and authorization at policy-defined gates, while allowing progressive elaboration and exceptions with expiry.
7. Make AI agents constrained participants with named identities, bounded tool rights, auditable provenance, and a mandatory human decision for policy-defined changes.

## 2. Problem

Modern enterprises typically maintain partially overlapping representations of reality: strategy in presentations, capability maps in EA repositories, processes in BPMN tools, definitions in spreadsheets, decisions in wikis, API contracts in Git, requirements in backlog tools, controls in GRC systems, tests in CI, runtime behavior in telemetry, and agent context in prompts.

This causes:
- semantic drift between business intent, documented architecture, implementation and real behavior;
- duplicated or contradictory terms, rules, process variants and domain interfaces;
- slow impact analysis for cross-capability change;
- approvals without a clear inventory of *what knowledge changed* and *who owns it*;
- loss of rationale, hypotheses, assumptions and rejected alternatives;
- optimistic AI-generated implementations based on obsolete or unauthorized context;
- manual evidence gathering and brittle traceability after release;
- centralized governance bottlenecks or, conversely, ungoverned local autonomy.

The solution is **not** a single universal diagram, replacement for BPMN/UML/ArchiMate, or a prompt repository. It is a controlled knowledge lifecycle linking **motivation → semantics → architecture → specification → realization → verification → observation → reconciliation**.

## 3. Product goals and outcomes

| ID | Goal | Observable outcome |
|---|---|---|
| G-01 | Shared, usable enterprise semantics | Authorized users find a definition, its owner, provenance, valid scope and effective version |
| G-02 | Governed changes to knowledge | Each production-relevant semantic change has a typed diff, impact set, disposition and authorized approvals |
| G-03 | AI-native delivery grounded in truth | Agents can retrieve policy-filtered, version-pinned knowledge and propose rather than self-approve changes |
| G-04 | Traceability without manual spreadsheet reconciliation | From business signal to deployed behavior and evidence, traversable in both directions |
| G-05 | Continuous conformance | Detect changes between approved, implemented and observed states; route them to accountable people |
| G-06 | Federated ownership | Capabilities own their semantics; shared/cross-cutting concerns have explicit stewardship |
| G-07 | Proportionate assurance | Gate strictness depends on classification, risk, affected assets and change scope |
| G-08 | Interoperability | Source models remain exportable to their standards/tools; no vendor-specific ontology lock-in |

Suggested pilot success measures (targets to validate, not factual baselines):
- ≥95% of *pilot-scope* mandatory artifacts have stable IDs, owner, classification and revision provenance.
- ≥90% of pilot changes can generate a reproducible impact report and identify required approvers.
- 100% of high-risk production changes have a recorded assurance decision, linked tests and deployment identity.
- 0 unauthorized cross-classification graph traversals in automated security tests.
- Median time to establish impact and required sign-offs decreases against a measured pre-pilot baseline.
- All detected material knowledge drifts receive disposition within the organization's configured SLA.

## 4. Scope and boundaries

**In scope:** ontology and registries; enterprise/capability knowledge catalogs; modeling adapters; requirements and change-intent management; hypotheses; impact/consistency checks; logical interfaces; ownership and access policy; governance approvals; agent workbench; delivery/evidence integration; observed-vs-approved reconciliation; dashboards; APIs; federated knowledge promotion; audit and lifecycle controls.

**Not in initial scope:** creating a new BPMN execution engine, replacing Git/CI/CD or ITSM, automatically refactoring all legacy systems, reverse-engineering the entire enterprise in one pass, declaring regulatory compliance by generating documents, assigning final regulatory accountability to autonomous agents, or reproducing proprietary licensed standards content.

**Initial source systems:** EA repository/model exports, BPMN/DMN/UML model exports, BIAN-aligned reference mappings, Git + OpenAPI/AsyncAPI, requirements/issue trackers, identity providers, control catalogs, CI test and deploy results, production telemetry. Adapters must disclose coverage and lossiness.

## 5. Key personas and jobs

| Persona | Job to be done | Primary authority |
|---|---|---|
| Enterprise Architecture Board / Chief Architect | Manage principles, meta-model policy, cross-capability consistency, exceptions | Enterprise-wide architecture policy, not all domain semantics |
| Capability Owner | Maintain capability intent, outcomes, service commitments and change priorities | Capability scope and its business outcomes |
| Domain/Business Architect | Establish capability/process/service relationships and target state | Architecture models under assigned scope |
| Process Owner | Specify lifecycle, variants, controls and handoffs | Named business processes and variants |
| Information Owner / Data Owner | Approve business concept meaning, constraints, ownership and data policy | Assigned information domain/attributes |
| Data Steward | Curate definitions, lineage, quality evidence and proposed mappings | Stewardship; approval if delegated |
| Business Rule/Decision Owner | Define policies and decisions, decision tables, validity and exceptions | Named rules/decisions |
| Product/Change Owner | Prioritize and manage change intent and committed scope | Change case, not unilateral semantic authority |
| Solution/API Architect | Design implementations, logical services/contracts and compatibility | Allocated solution architecture |
| Engineering Lead / Developer | Build code and tests tied to approved specs | Implementation artifacts |
| Security/Privacy/Risk/Compliance | Define controls and approve designated material changes | Control-specific mandatory sign-offs |
| SRE/Operations Owner | Provide evidence of running behavior, incidents, SLOs and drift | Operational facts and dispositions |
| Assurer / Independent Reviewer | Evaluate claims/evidence and issue assurance decision | Assigned assurance case |
| AI Agent / Agent Supervisor | Retrieve, analyze, generate, validate, propose, execute delegated tasks | No inherent policy or business approval authority |
| Knowledge Platform Operator | Operate indexes, pipelines, integrations and integrity controls | Technical platform, not content ownership |

Human role assignments are bound to *scope, time and delegation*; an actor may hold multiple roles but segregation-of-duties policies may forbid self-approval.

## 6. Core end-to-end scenarios

### S1 — Change from signal to governed release

1. Capture a **Signal** (incident, regulatory change, customer feedback, telemetry, strategic demand).
2. Establish an **Intent/Opportunity** and scope; open a **Change Case** and one or more **Hypotheses**.
3. Research approved knowledge and evidence, compare hypotheses, select/decline/defer with rationale.
4. Create **Impact Assessment** across capabilities, processes, decisions, concepts, controls, services, interfaces, systems and data.
5. Reach a **Commitment Gate** once business outcome, boundaries, material risks, responsible owners, high-level acceptance and unresolved assumptions are explicit; not every technical detail must be finalized.
6. Progressively elaborate **Requirements, Scenarios, Target Models and Change Sets**; raise *knowledge-level* amendments with typed diffs.
7. Validate semantics, consistency, security policy, dependencies and required approvals by the owners of the *affected knowledge elements*.
8. Generate/implement code, workflow configurations, contracts, migration and tests with pinned approved context.
9. Gather **Verification Evidence** and assurance claims; detect any deviation from the target knowledge baseline.
10. Route deviation decisions (correct code, amend knowledge via a new approved change, accept time-limited exception, or stop release).
11. Deploy and record a **Release/Deployment Manifest** linking exact artifact/revision hashes and assurance outcome.
12. Compare observed behavior against expectations; create **Drift Cases** with triage and reconciliation.

See [06 — Lifecycle](06-change-delivery-assurance.md).

### S2 — Explore current enterprise knowledge

A user asks: “Which customer onboarding variations collect beneficial owner information, and which rule and API implement that?” The platform returns *permission-filtered* capability/process variants, rule/decision nodes, conceptual entities, logical interfaces, API operations, implementing services and evidence with confidence, freshness, baseline and provenance. Unknown relationships are *unknown*, not fabricated.

### S3 — Cross-capability regulatory change

A change to an enterprise policy influences Customer Onboarding and Party Management. Each affected semantic owner reviews the exact diff. One delivery can incorporate multiple capability changes; each semantic change keeps its own authority, approvals and effective date. Enterprise policy approval does not erase downstream ownership.

### S4 — Reverse engineer existing systems

An agent imports repository code, database schema and runtime traces as **observed or inferred knowledge candidates**, labeled with method, confidence, timestamp and source. The agent cannot automatically promote those findings into normative meaning. Owners validate promoted concepts; contradictions become drift cases.

### S5 — Maintain a capability over time

Each capability has a **Capability Knowledge Profile** (scope, owning roles, concerns, required artifacts, approved baseline, exceptions, maturity, dependencies, evidence freshness). Teams use it to see missing knowledge and required stewardship, not to duplicate enterprise-wide models.

## 7. Functional domains

| Domain | Required functionality | Key outputs |
|---|---|---|
| Knowledge registry | Register IDs, classifications, ownership, origin, baselines, effective dates, revisions and controlled links | KnowledgeElement, Revision, typed Relationship |
| Capability workspace | Templates for each capability and federated ownership; readiness and dependency maps | CapabilityProfile, required-artifact matrix |
| Modeling integration | Import/export ArchiMate, BPMN, DMN, UML/XMI, API schemas; preserve native IDs and semantics | SourceModel, parsed typed elements, mapping reports |
| Ontology and semantics | Controlled vocabularies, referential/temporal integrity, aliases, definitions and semantic equivalence assertions | DomainConcept, glossary, Shape/Rule |
| Discovery and impact | Search, graph navigation, bounded traversal, compare baselines, explain impacts | ImpactAssessment, explainable query plan |
| Change/requirements | Signal, intent, hypotheses, commitment, requirements, progressive refinement and semantic diffs | ChangeCase, Hypothesis, Requirement, ChangeSet |
| Governance | Policy-derived approvers, role/scope checks, delegated decisions, segregation, expiry, exception handling | Approval, Waiver, DecisionRecord |
| Agents | Context packaging, tool sandboxing, proposal creation, human intervention, policy-limited execution | AgentRun, provenance, proposed changes |
| Delivery integration | Link architecture to code, APIs, build/test/deploy and operations | Realization, TestEvidence, ReleaseManifest |
| Assurance | Claims/argument/evidence, gate checks, verification freshness and risk decisions | AssuranceCase, GateDecision |
| Runtime reconciliation | Compare design promises with telemetry/deployed contracts/configuration | Observation, DriftCase, disposition |
| Enterprise analytics | Coverage, stale knowledge, risk concentrations, approval latency, semantic debt | KPIs, evidence reports |

## 8. Product experience and information architecture

The application must offer:
- **Enterprise map:** capabilities, domains, goals, value streams, shared dependencies, owners and access-aware heatmaps.
- **Capability workspace:** scope charter, standard model lenses, approved/target/actual state switch, local glossary, process families/variants, information and interfaces, obligations, rules, linked changes, release health.
- **Knowledge element page:** stable ID, native source artifact, canonical type, meaning, owner, classification, effective version, references, graph neighborhood, revisions, decisions, evidence, discrepancies.
- **Change studio:** signal → alternatives → impact → commitment → scoped semantic diffs → mandatory reviewers → proof → release → operational reconciliation.
- **Model editor/viewer adapters:** preserve model-specific semantics; no requirement to draw every notation in one universal renderer.
- **Assurance workbench:** claims, evidence integrity, unresolved assumptions, control coverage, waivers and approval status.
- **Agent workbench:** ask, investigate, propose, inspect sources/diffs/uncertainty, approve limited actions; all prompts context-filtered and version-pinned.
- **Diff/impact view:** side-by-side native diff and canonical semantic diff, affected owners and dependencies, including confidence/uncertainty.
- **Audit explorer:** “who knew/approved/implemented what, as of date X?” with permissible information only.

Every user-facing AI-generated statement must distinguish **verified fact**, **inference**, **proposal** and **unknown**.

## 9. Hard design invariants

I-01. Knowledge elements have stable identities; revisions are immutable.  
I-02. A diagram, document, model fragment, repository file and semantic element are distinct objects.  
I-03. Each authoritative element has one accountable authority *within a declared scope*; shared elements use a clearly designated coordinating authority and additional mandatory approvers as policy requires.  
I-04. The same real-world concept can be represented in multiple standards; mappings require evidence, transformation version and non-lossy/lossy status.  
I-05. A capability is an ability, not a process; process instances/variants and implementations are not capabilities.  
I-06. BIAN Service Domains are **reference taxonomy**, not automatically one-to-one with capability, business service, bounded context, application or microservice.  
I-07. Commitment does not imply full detailed solution certainty; explicitly track deferred decisions and due gates.  
I-08. Every semantic modification has a change operation, before/after revision, rationale, impact and disposition.  
I-09. AI agents are never implicitly granted human approval rights.  
I-10. Source privacy and security restrictions propagate to derived triples, embeddings, search hits, summaries and artifacts.  
I-11. A deployment cannot silently redefine enterprise semantics; material divergence must be blocked or recorded with authorized disposition.  
I-12. Observation evidence is not, by itself, authorization for normative change.  
I-13. Reproducibility requires exact model, policy, code, test and release versions.  
I-14. Exceptions are explicit, limited in scope/time, monitored and closed.  
I-15. Schema evolution and ontology migration must be versioned and support backward references.

## 10. Key workflows and interaction contracts

**Read query:** Authenticated actor + purpose + scope → authorization filter → retrieval at named baseline/effective time → evidence-based answer with source IDs, permitted redactions and unknown coverage.

**Propose amendment:** Actor/agent submits target IDs, operation, expected revision IDs, rationale, proposed content, impacted scopes → server computes semantic diff and validations → impact owner resolution → approval/commit path.

**Review:** Reviewer receives precisely authorized diff, risk and claims → may approve/reject/request revision/defer; any rebase changing reviewed semantics invalidates relevant approvals.

**Promote baseline:** Verify ownership, required approvals, policy checks, tests and fresh evidence → atomically record immutable promotion event → update active baseline pointers and downstream indexes.

**Release:** CI presents signed build and deployment manifests and tests → assurance gate checks knowledge baseline and dispositions → approved release identity persists; cannot be backfilled silently.

**Drift:** Observation/inferred state contradicts approved expectation → create classified triage item → accountable owner decides false positive, software correction, knowledge amendment, approved exception, or investigation.

## 11. Quality and safety requirements

- **Security:** deny-by-default, capability/attribute-scoped ABAC plus relationship traversal guards and field redaction; SSO/OIDC, service identity and workload provenance; no security guarantees from a natural-language agent alone.
- **Integrity:** optimistic concurrency on element revisions, tamper-evident journal, signed commits/manifests where practical, change replay and backup/restore.
- **Availability:** retrieval remains possible from last validated snapshot during index rebuild; changes fail closed when authorization/approval policy cannot be evaluated.
- **Performance (pilot targets, to validate):** p95 authorized element lookup ≤500 ms; scoped 2-hop impact query ≤3 s for a 100k-element pilot; synchronous policy validation ≤2 s for ordinary changes; larger analysis asynchronous with visible job status.
- **Scale:** separate authoring transactional store from graph/search projections; support millions of elements without global locks.
- **Accessibility:** keyboard and screen-reader-friendly approvals, usable contrast, explanatory error messages.
- **Interoperability:** import/export with stable mapping tables and round-trip reports; explicit handling of unsupported constructs.
- **Observability:** per-request trace IDs, ingestion lag, graph projection watermarks, policy decisions and model/agent evaluation metrics.
- **Resilience:** reconciliation jobs can replay idempotently, poisoned events are quarantined, mutations have idempotency keys.
- **Audit:** record user, role, purpose, policy version, scope, before/after hashes, decisions, evidence, rationale, clock and source; jurisdictional retention configured by policy.

## 12. Operating model

**Central platform team** owns ontology governance procedures, core platform, integration contracts and policy enforcement. **Federated capability teams** own their approved knowledge content. Enterprise-level roles govern cross-cutting standards, classification and architecture coherence. **Assurance/control owners** decide under their mandates. Agents help discover, write, check and propose; they do not replace accountable ownership.

Rollout must be **capability-sliced**, not a mandatory all-enterprise documentation rewrite. Read [05 — Governance](05-governance-roles-and-ownership.md) and [09 — Roadmap](09-roadmap-and-open-decisions.md).

## 13. Risks and mitigations

| Risk | Failure mode | Mitigation |
|---|---|---|
| Ontology overreach | Unusable universal model | Minimal core + extensible bounded-context profiles + typed mappings |
| Documentation theater | Beautiful models, no living relation to code | CI/telemetry links, release gates, drift loop |
| Approval explosion | Every edit requires 20 signatures | Risk-based scope, materiality, delegated stewardship, grouped review of exact diffs |
| Silent AI hallucination | Inferred behavior promoted as policy | Epistemic status, provenance, explicit promotion authority |
| Graph information leakage | Restricted existence inferred through links | Authorization throughout graph, indexes, summaries and counts |
| Vendor lock-in | Canonical layer erases native semantics | Native sources retained, export round-trip tests |
| Stale knowledge | Incorrect agent conclusions | Freshness SLOs, alerts, pinned baselines and confidence reporting |
| Semantics-by-code | Implementations redefine business concepts | Independent semantic owners and conformance gates |
| Invalid assurance | Evidence exists but is unrelated/outdated | Signed/versioned claims-to-evidence with freshness and applicability checks |

## 14. Delivery artifacts and reading order

1. [02 — Canonical ontology and artifact metamodel](02-canonical-metamodel.md)
2. [03 — Standards, mappings and viewpoints](03-standards-and-viewpoints.md)
3. [04 — Logical/platform architecture](04-reference-architecture.md)
4. [05 — Capability ownership, roles and RACI](05-governance-roles-and-ownership.md)
5. [06 — Change lifecycle and assurance](06-change-delivery-assurance.md)
6. [07 — Security and controls](07-security-and-controls.md)
7. [08 — Detailed functional requirements and tests](08-functional-requirements.md)
8. [09 — Delivery roadmap and open decisions](09-roadmap-and-open-decisions.md)
9. [10 — Fictional banking worked example](10-worked-example.md)
10. [11 — Terminology and references](11-glossary-and-references.md)
11. Machine-readable starting points under [spec/](../spec/).

**Document status:** These are prescriptive *design proposals*. No claim is made that the platform has been built, tested, certified or adopted by any bank.
