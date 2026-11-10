# 02 — Canonical ontology, metamodel and artifact taxonomy

**Status:** Proposed KDESF canonical metamodel v0.1. It is an *integration and governance model*, not a replacement for native TOGAF/ArchiMate/BPMN/DMN/UML metamodels.

## 1. Three layers of meaning

1. **Enterprise semantic layer:** what is intended/defined/approved: capability, business concept, policy, decision, process, business service, logical interface, requirement and constraints.
2. **Realization layer:** the executable or deployed expression of that knowledge: application, code, data store, endpoint, workflow, job, build, configuration, test, release and deployment.
3. **Evidence and observation layer:** what was proved, inferred or measured: claim, test result, audit observation, trace, metric, failure, drift case and effective exception.

Each layer has multiple *states*, not just draft/final:
- **Normative approved baseline**: what the enterprise authoritatively commits to.
- **Proposed/target**: alternative or planned future semantics not yet authoritative.
- **Implemented as-built**: what a particular version of the solution demonstrably contains.
- **Observed as-running**: sampled runtime behavior with provenance, confidence, time, scope and limits.
- **Inferred candidate**: a machine/human interpretation awaiting semantic validation.

**Do not overwrite one state with another.** They must be queryable by valid/effective time and recorded/transaction time, with separate identities for a thing and each of its revisions.

## 2. Foundational type system

~~~mermaid
classDiagram
  direction LR
  class KnowledgeElement {
    +id: URI
    +kind: TypeIRI
    +name: string
    +namespace: URI
    +scopeIds: URI[]
    +authorityRoleId: URI
    +classification: 1..4
  }
  class ElementRevision {
    +revisionId: URI
    +contentHash: SHA256
    +validFrom: timestamp
    +validTo: timestamp?
    +recordedAt: timestamp
    +epistemicStatus: enum
    +lifecycle: enum
  }
  class SourceArtifact {
    +artifactId: URI
    +nativeNotation: string
    +format: MIME
    +sourceRef: URI
    +sourceHash: SHA256
    +mappingVersion: string
  }
  class Relationship {
    +id: URI
    +type: TypeIRI
    +subjectRevision: URI
    +objectRevision: URI
    +validTime: interval
    +provenance: URI
  }
  class RoleAssignment {
    +actorId: URI
    +roleId: URI
    +scopeId: URI
    +delegationRef: URI?
    +effectiveTime: interval
  }
  class ChangeSet {
    +id: URI
    +operations: ChangeOperation[]
    +expectedRevisions: URI[]
  }
  class Approval {
    +decision: enum
    +reviewedDiffHash: SHA256
    +policyVersion: URI
  }
  class Evidence {
    +subject: URI
    +claimId: URI
    +captureTime: timestamp
    +integrityHash: SHA256
  }
  KnowledgeElement "1" o-- "*" ElementRevision
  ElementRevision "*" --> "*" SourceArtifact : derivedFrom
  ElementRevision "1" --> "*" Relationship : endpoint
  ChangeSet "1" --> "*" ElementRevision : proposes
  ChangeSet "1" --> "*" Approval : authorizedBy
  Approval "*" --> "*" RoleAssignment : reviewerActsAs
  Evidence "*" --> "*" ElementRevision : supports
~~~

**Important:** the diagram is KDESF's custom integration model. Links to native notations do not imply that these classes are native standard types.

### 2.1 Required common envelope

Every authoritative or proposed first-class element has:
- **Identity:** stable globally unique ID (URI/URN), canonical type, unique namespace-qualified name/aliases, description and optional human-friendly key.
- **Authority:** primary accountable role assignment, home capability/domain, jurisdiction, classification and policy tags.
- **Provenance:** source system, source artifact ID and version, mapped source fragment ID, author/agent, derivation method, confidence where inferred, timestamp and content hash.
- **Lifecycle:** immutable revision, predecessor/supersession chain, status, valid-from/to, recorded-at, baseline membership and retention policy.
- **Semantics:** type-specific fields, constraints/invariants, permitted variation boundaries, business examples/counterexamples, glossary links.
- **Relationships:** typed outgoing/incoming references with evidence, temporal validity and access classification.
- **Quality:** completeness/consistency score, validated shapes, assurance state, known contradictions and last verified time.

Fields may be deliberately unknown in a draft; requiredness varies by stage and element type, not by optimistic auto-filling.

### 2.2 Distinguish identifiers

- **Element ID**: persistent conceptual identity; survives renames and reformatting.
- **Revision ID**: immutable, content-addressed or monotonically assigned revision snapshot.
- **Source artifact ID**: file/model/document/container, potentially many elements per file.
- **Native element ID**: modeler's BPMN/ArchiMate/UML/DMN/OpenAPI ID; never assume universal stability.
- **External taxonomy ID**: BIAN service domain, regulatory reference or other catalog URI; is a *mapping reference*, not the element ID.
- **Deployment ID**: immutable realized environment/version/time identity.
- **Graph edge ID**: distinguish alternative mappings/claims/provenance and time windows.

Keep an explicit source ID crosswalk with mapping version, lossiness marker and confidence.

## 3. Canonical entity families

### 3.1 Enterprise and motivation

| Type | Distinguishing semantics | Typical authority |
|---|---|---|
| Enterprise, OrganizationUnit, Domain | Jurisdiction, organizational boundary and business scope | EA / business architecture |
| Capability, CapabilityProfile | Durable ability and maturity/ownership charter; may be hierarchical | Capability Owner |
| Outcome, Goal, Objective, KPI | Desired outcome, measure and success criteria | Business sponsor |
| ValueStream, ValueStreamStage | Value-creation stages; not an executable BPMN process | Business architect / value-stream owner |
| Principle, Policy, Constraint | Enterprise normative guidance and constraint applicability | Enterprise policy owner / control owner |
| Stakeholder, Concern, Viewpoint, View | ISO 42010-style architecture concern/view mechanism | Lead architect |
| ArchitectureBaseline, TargetArchitecture, TransitionState | Explicit current/target/roadmap viewpoints and compositions | Architecture authority |
| ArchitectureDecision, Rationale, Assumption | Chosen alternative, rejected options, reversibility and review trigger | Relevant decision authority |

### 3.2 Business operation and behavior

| Type | Distinguishing semantics | Typical authority |
|---|---|---|
| ProcessFamily | Stable family purpose and common outcomes across versions/divisions | Enterprise Process Owner |
| BusinessProcess | Boundary, trigger, inputs, output, consumer, obligations, service relationships | Process Owner |
| ProcessVariant | Context-specific intentional differences (jurisdiction, segment, channel, business unit) | Scoped Process Owner |
| Activity, Subprocess, Event, Handoff | BPMN semantics + model reference, not a substitute BPMN engine | Process Owner |
| BusinessService | Externally visible service behavior offered to consumers | Service Owner |
| BusinessFunction, BusinessInteraction | Activity grouping and cross-role interaction | Business architect |
| Decision, DecisionTable, BusinessRule | Rule, inputs/outputs, FEEL/other expression, effective/priority/exception semantics | Decision Owner |
| ControlActivity | Control objective, location, performer, evidence and applicable risk | Control Owner |

Do not conflate **BPMN call activity/subprocess** with a hierarchical business capability. A ProcessFamily and ProcessVariant are *KDESF extension types*, not official BPMN elements.

### 3.3 Information, semantics and data

| Type | Distinguishing semantics | Typical authority |
|---|---|---|
| BusinessConcept | Definition, scope, synonyms, invariants, lifecycle and identity semantics | Information Owner |
| InformationObject | Composite information used/exchanged in a business context | Information Owner |
| Attribute, ValueDomain, Enumeration | Field meaning, cardinality, semantic constraint and policy | Information Owner / Data Steward |
| InformationRelationship | Entity association, role names, multiplicities and conditions | Information Owner |
| StateMachine, LifecycleState, Transition | Business object state, transition invariants and legal transitions | Domain/Information Owner |
| BusinessEvent, EventType | Something that happened, trigger, schema, time, meaning and publisher | Domain Owner |
| DataProduct, DataSet, DataContract | Producer/consumer semantics, lineage, quality promise, sensitivity | Data Product Owner |
| SystemOfRecordAssignment | *For which fact/attribute and lifecycle interval* an authority is declared | Data Owner |
| SemanticMapping, ContextMap | Translation between domain concepts, bounded contexts or standards | Both affected semantic owners |
| OntologyVocabulary, OntologyRule, ValidationShape | Definitions, class/property modeling, machine-checkable constraints | Ontology/Domain Steward |

Two representations may be *mapped* without being *equivalent*. Support exactMatch, broadMatch, narrowMatch, relatedMatch, transformation and deliberatelyUnmapped, each with reviewed scope. Preserve schema evolution and alias migration history.

### 3.4 Services, interfaces and realization

| Type | Distinguishing semantics | Typical authority |
|---|---|---|
| BoundedContext | DDD semantic boundary and ubiquitous language | Domain Architect |
| LogicalService, LogicalInterface | Technology-neutral service/interface with promised behavior | Service/API Architect + Business Owner |
| LogicalOperation | Consumer-facing intent, request/response semantics, effects, pre/postconditions | Service/API Architect |
| InterfaceContract | Versioned semantic compatibility and quality/security contract | API/Product Owner |
| TechnicalContract | OpenAPI, AsyncAPI, GraphQL, gRPC/protobuf or other executable contract | API/Engineering Owner |
| ApplicationComponent, ApplicationService | Application structure/service realization | Solution Architect |
| DataStore, DataPipeline, Integration | Technical resources, lineage and delivery semantics | Engineering/Data Owner |
| Repository, CodeComponent, BuildArtifact | Implementation assets and committed source/build evidence | Engineering Lead |
| Deployment, RuntimeComponent, Configuration | Concrete environment-specific installed state | Operations Owner |
| TestCase, TestRun, VerificationResult | Assertion, expected behavior, run and source revisions | Test/Quality Owner |

A **logical interface** is not an OpenAPI operation; it can have many protocols/realizations. Semantic compatibility and wire compatibility are separate checks.

### 3.5 Change, delivery, assurance and observation

| Type | Distinguishing semantics | Typical authority |
|---|---|---|
| Signal | Origin, urgency, source/evidence, context, duplicates | Signal submitter/triage owner |
| Intent, Opportunity | Problem/outcome worth pursuing | Sponsor |
| Hypothesis, Experiment | Falsifiable claim, assumptions, test, evidence, selection status | Product/Business Owner |
| ChangeCase | Umbrella intent, scope, lifecycle, risk, coordination | Change Owner |
| ScopeUnit, DeliverySlice | Ordered grouping such as initiative/epic/feature/story **as projection**, not governing ontology | Delivery Owner |
| Requirement, Scenario, AcceptanceCriterion | Normative desired behavior/quality and proof requirement | Requirement Owner |
| ChangeSet, ChangeOperation | Atomic *knowledge* delta, expected predecessor and impacted IDs | Proposer + affected authorities |
| ImpactAssessment | Downstream effects, uncertainty, materiality, approvers and alternative paths | Architect / agent-proposed |
| DecisionRecord, Approval, Delegation | Scoped, time-valid accountable disposition, diff hash and policy version | Authorized reviewer |
| Exception, Waiver, RiskAcceptance | Time-limited deviation with compensating control and review | Named risk/control authority |
| Claim, Argument, Evidence, AssuranceCase | Evidence-backed assertion about system/change/conformance | Assurer and evidence owner |
| ReleaseManifest, DeploymentManifest | Exact artifact versions, gates, sign-offs and environment | Release/Operations Owner |
| Observation, Inference, DriftCase | Time-bounded fact or inference, discrepancy and triage resolution | Operations + semantic owner |
| AgentRun, ToolInvocation, AgentProposal | Model/tool identities, authorized context, input/output and provenance | Agent Supervisor / platform |
| AuditEvent | Append-only policy and business mutation/decision history | Platform governance |

## 4. Relationship ontology

All edges specify subject type, predicate, object type, evidence/provenance, revision applicability, temporal validity and derived/reference status. Inverse labels are **views**, not automatically independent facts.

| Predicate (directed) | Allowed example | Invariant/semantics |
|---|---|---|
| decomposesInto | Capability → Capability | No cycles in declared primary decomposition |
| ownedBy | Element → RoleAssignment | Exactly one accountable authority per active scope; optional co-approvers |
| supportsOutcome | Capability → Outcome | No presumption of sole causality |
| enables | Capability → ValueStreamStage | Many-to-many |
| realizes | BusinessProcess → BusinessService; ApplicationService → LogicalService | Relation meaning depends on type mapping; native ArchiMate relations must be checked independently |
| performs | BusinessRole → ProcessActivity | Scope, effective time |
| hasVariant | ProcessFamily → ProcessVariant | Distinct applicability rule required |
| callsProcess | Activity → BusinessProcess | Reusable call, not another capability |
| governedBy | Process/Decision/Interface → Policy | Policy must be effective and applicable |
| evaluates | Decision → BusinessConcept | Traceable decision inputs |
| usesInformation | Process/Service/Operation → InformationObject | Denotes semantic use, not ownership |
| publishes / subscribesTo | LogicalService → BusinessEvent | Semantics of publisher and subscriber are different |
| exposes | LogicalInterface → LogicalOperation | Operation bound to interface version |
| fulfilledBy | LogicalOperation → TechnicalContractOperation | Mapping may be many-to-many and transformed |
| implementedBy | LogicalService → ApplicationComponent; Requirement → CodeComponent | Does not assert compliance |
| mapsToReference | Element → BIAN/other ReferenceEntry | Scope/quality/confidence mandatory |
| refines | Requirement → Requirement | Parent-child logical refinement; do not overload chronology |
| satisfies | Architecture/Implementation → Requirement | Design intent; proof belongs to verifies |
| verifies | VerificationResult → Requirement/Constraint/Claim | Version/evidence linked |
| supportsClaim | Evidence → Claim | Evidence applicability and expiry checked |
| affects | ChangeSet → ElementRevision | Explicit changed or transitively impacted |
| supersedes | Revision → Revision | No cyclic supersession |
| derivedFrom | InferredElement → SourceArtifact/Observation | Does not confer authority |
| deployedAs | BuildArtifact → Deployment | Exact hash and environment |
| observes | Observation → RuntimeComponent/Service | Time bounded |
| contradicts | Observation/Inference → ApprovedElementRevision | Creates candidate drift, not implicit replacement |

**Consistency checks:** type compatibility; directed predicate validity; referential existence; no orphan mandatory references; version compatibility; approved-source requirement for generated specifications; no illegal overlap in accountable assignment; no retroactive unsanctioned effective dates; explicit multi-owner resolution for shared artifacts.

## 5. The artifact metamodel — source, fragment, projection

**Artifact** = an identifiable authored or generated container (ArchiMate exchange model, BPMN XML file, UML XMI, JSON Schema, decision record, PR, code repo, test report, dashboard snapshot).

**Model element** = a native part of that artifact (ArchiMate capability object, BPMN task, UML class, DMN decision, OpenAPI operation).

**Canonical element** = KDESF semantic identity projected from zero/many native elements, authored natively as structured content, or inferred from observation.

**Representation** = source-language syntax and presentation of a canonical element. Multiple native representations can refer to the same canonical semantic subject, with explicitly reviewed equivalence.

**Projection** = materialized graph/search/read model, never silently authoritative.

Example: A UML class named Customer and an ArchiMate Business Object “Customer Data” may be related, but cannot be equated automatically. A domain BusinessConcept “Party” might map to both with different scope. Model round-tripping *must not* erase UML association multiplicities or BPMN boundary event semantics.

## 6. Artifact types and per-capability requirement profiles

A **CapabilityProfile** contains:
- capabilityId, parentId and owning organizational scope;
- accountable Capability Owner and delegated knowledge custodians;
- business goals/outcomes, regulatory/control applicability, criticality, data sensitivity;
- process families/variants, business service offerings, information scope, decisions and rules;
- logical interface/API and event inventory, dependent capabilities, accountable service owners;
- current/target architecture references, deployed realization evidence and known drift;
- required-artifact policy set (stage, risk, materiality, applicability), mandatory quality gates;
- exceptions, completeness/coverage, freshness SLOs, review cadence;
- links to external taxonomies (BIAN, internal taxonomy), with version and mapping rationale.

**Default baseline profile (risk-aware, not identical for every capability):**

| Artifact family | Mandatory for every capability? | Minimum condition | Authority |
|---|---|---|---|
| Capability charter, goal, boundary, owner | Yes | Before capability is marked governed | Capability Owner |
| Dependency and stakeholder map | Yes | Approved, maintained | Capability Owner + Architect |
| Business service catalog | If externally offered business service exists | Consumer/outcome/owner defined | Business Service Owner |
| Process family and variants | If repeatable business operation exists | Applicability and control points explicit | Process Owner |
| Business concepts and information inventory | If capability creates/consumes information | Meaning, stewardship and sensitivity present | Information Owner |
| Data lineage and SOR declarations | If authoritative data is stored/produced | Scope, field/fact, producer, policy clear | Data Owner |
| Rules/decisions and exceptions | If capability exercises discretionary or policy logic | Decision owner, inputs/outputs, validity | Decision Owner |
| Logical interfaces and interactions | If interface is offered/consumed | Owner, semantics, version and compatibility | API/Service Owner |
| Security, privacy, risk/control mapping | Per policy applicability | Control owner, scope and assurance method | Control Owner |
| Technology architecture/realization | For deployed digital realization | Target/current, dependencies, owners | Solution Architect |
| Requirements, tests, releases, observations | For active change and runtime | Traceable relevant versions and evidence | Change/Engineering/Operations Owners |
| BIAN reference mapping | For participating bank where useful | Catalog/version, mapping type and confidence | Domain Architect |
| Detailed BPMN executable model | **No, not always** | Only where execution-level fidelity is needed | Process Owner |
| UML diagrams for every concept | **No** | Structured semantics required; notation can vary | Information Owner |

Policy shall derive the actual minimal artifact set by criticality, risk, activity, jurisdiction and maturity. “Not applicable” requires explicit reason; “missing” must remain visible.

## 7. Capability and process scope rules

**Capability hierarchy** is a configurable taxonomy, not a universal four/five-level standard. At high level, it answers *what ability is needed*, not *what sequential steps run*.

**Process structure** has two independent axes:
- **Decomposition**: process → embedded sub-processes / reusable called processes → activities.
- **Variation**: ProcessFamily → variants distinguished by jurisdiction, customer type, product, channel or division and effective time.

Optional L0–L4 *modeling convention*:
- L0 enterprise value chain/value stream/capability landscape;
- L1 end-to-end process scope and outcomes;
- L2 process areas/major subprocesses;
- L3 BPMN analytical executable-descriptive detail, as appropriate;
- L4 technical realization, integrations, events and runtime controls (not a BPMN-defined level).

A single ArchiMate Business Process can link to a BPMN process at whichever decomposition level faithfully represents its boundary. Neither standard demands a fixed 1:1 linking level.

## 8. Change semantics

A ChangeSet is a transaction-like collection of immutable proposed operations on canonical **revisions**:
- **add** new identity;
- **modify** meaning/constraint/relationship;
- **clarify** prose without intentionally altering semantics (subject to semantic check);
- **link / unlink** relationship with explicit edge revision;
- **deprecate / retire** by effective time;
- **supersede** with lineage;
- **split / merge** with successor identity graph and migration mapping;
- **rename** with identity preserved and alias history;
- **deviate** (implemented/observed state differs from normative; not direct normative mutation).

Include expected previous revision, before/after canonical semantic hashes, source diff, rationale, risk, classification, policy version, owner resolution, affected claims/evidence, and effective time. Applying a ChangeSet uses optimistic concurrency; changed bases trigger rebase and *reapproval if semantics or evaluated impact changed*. Cross-element promotion is atomic at baseline pointer level or remains pending with controlled partial state.

## 9. Requirements metamodel

Requirement is a **first-class typed normative knowledge element**, not a text string attached to a backlog card.

Recommended fields:
- identifier, owner, parent intent, source/hypothesis;
- statement and modality (must/should/may), quality dimension, priority;
- applies-to scope, relevant capabilities, process steps, rules, concepts and interfaces;
- measurable outcome, preconditions, postconditions, exceptions and acceptance criteria;
- rationale, evidence/constraints, assumptions, unresolved questions and gate due dates;
- refinement status, required commitment detail level, effective/retirement dates;
- change operation and version, validation/proof strategy and linked evidence;
- priority/risk, security classification, policy and approval disposition.

Separate **business requirement**, **user/functional requirement**, **quality attribute**, **data/semantic constraint**, **control obligation**, **migration constraint**, **operational SLO**; project management epic/feature/story is a delivery grouping or refinement relationship, not an alternative authority for meaning.

## 10. Ontology layering and extensibility

Implement four namespaces:
1. **Core KDESF** (identity, revision, role, relation, change, assurance, provenance) — very stable.
2. **Method and standard mappings** (ArchiMate/TOGAF, BPMN/DMN, UML, BIAN, API4KP, OSLC, SACM, Essence/SPEM) — versioned adapter/profile.
3. **Enterprise policy/profile** (company taxonomies, artifact criticality, classification, workflows) — centrally governed.
4. **Domain/capability vocabularies** (Party, Customer, Lending, Payments) — owned locally; portable.

Use RDF/OWL vocabulary for declarative meaning and align with PROV-O for provenance where useful. Use **SHACL for closed-world structural validation** and app-level/business logic for temporal constraints, workflows and authorization. OWL's open-world inference alone is *not* a compliance or completeness validator. JSON/relational projections can be the operational storage interface; RDF is not a mandatory universal database implementation.

A starter ontology and shapes are in [spec/ontology](../spec/ontology), and JSON schemas in [spec/schemas](../spec/schemas). They intentionally cover a small subset and are **not** complete standard-conformant adapters.

## 11. Representative validation invariants

- All baseline elements resolve to an effective authority and classification.
- Active authoritative information attributes have a declared domain and meaning.
- Each declared technical API operation maps to a logical operation or carries a reviewed exception.
- Each material requirement has at least one acceptance criterion and test/assurance strategy by its assigned gate.
- Process variant applicability predicates cannot unintentionally collide; intentional precedence must be explicit.
- Two separate facts cannot both claim exclusive system-of-record authority for the same attribute/scope/effective interval.
- Critical decision tables have designated owners, defined input/output types and coverage tests.
- A release's evidence hashes refer to the exact versions deployed, not merely the latest branch.
- All active exception expiries have a designated responsible reviewer.
- A derived inference is never promoted without a signed/recorded authorized disposition.

## 12. Query semantics

Every question operates on a query context:
- identity/roles/delegations and policy purpose;
- capability/domain scope;
- classification clearance plus relationship/field policy;
- effective-at and recorded-as-of time;
- state selector: approved, proposed, implemented, observed, inferred or comparison;
- desired evidence quality/freshness.

Results should expose: entity IDs, baseline/revision, relation direction, claim/provenance, confidence, ACL-induced incompleteness indicator (without leaking restricted counts), freshness and “cannot determine” where appropriate. Never convert “not found” into “does not exist” when evidence or access is incomplete.

## 13. Decisions still to make

- Global identity URI strategy and namespace ownership.
- Cross-enterprise federation: publish selected baseline products rather than grant broad raw-graph access.
- Whether to build RDF-native canonical persistence or relational event-sourced persistence with RDF projections.
- How to resolve conflicting authority in cross-capability concepts and who may create equivalence assertions.
- How much of SACM/Essence/SPEM to support natively versus via interoperability mappings.
- Constraint policy runtime: declarative policy engine, compiled checks or hybrid.
