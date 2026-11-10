# 04 — Reference logical, information and deployment architecture

**Status:** Implementation-neutral reference design. No tool/product below is mandatory unless later selected in an ADR.

## 1. Architectural style

A **modular knowledge platform** with federated ownership, Git-compatible source management, versioned domain APIs, asynchronous projection, and deterministic policy and assurance gates. Prefer an initial **modular monolith for the write path** with separated adapters, graph/search read models and independently scaled agent workers. Split services only when scale, security boundaries or different operational lifecycles justify them.

**Primary design:** Authoritative **immutable mutation/audit ledger + transactional canonical element/revision store**; user-authored native files remain in source systems/Git; materialized **graph, full-text/vector search, catalogs and dashboards** are *rebuildable projections*. Repository content is important input, but “latest Git branch contents” alone cannot capture temporal authority, decision scopes, observed state, field-level classification and signed policy evaluations.

## 2. C4-style landscape

~~~mermaid
flowchart TB
  U[Human users and approvers] --> UI[Enterprise / Capability / Change Workbench]
  A[Scoped AI agents] --> AG[Agent Tool Gateway]
  UI --> BFF[Experience API / authorized query]
  AG --> BFF
  AG --> CMD[Proposal / semantic mutation API]
  BFF --> QRY[Knowledge Query & Impact Service]
  BFF --> CMD
  BFF --> WF[Change and Assurance Orchestrator]
  CMD --> POL[Identity / ABAC / policy engine]
  WF --> POL
  QRY --> POL
  CMD --> VAL[Schema / Ontology / Semantic validation]
  WF --> APP[Role and Approval Resolver]
  APP --> POL
  CMD --> TX[(Transactional canonical revisions + event ledger)]
  WF --> TX
  TX --> BUS[Outbox + event bus]
  BUS --> PROJ[Graph/search/catalog projectors]
  PROJ --> G[(Versioned graph index)]
  PROJ --> S[(Filtered search indexes)]
  QRY --> G
  QRY --> S
  CI[CI / CD / Git / Tests] --> INT[External adapters and evidence ingestion]
  EA[EA/BPMN/DMN/UML tools] --> INT
  OBS[Telemetry / runtime / ITSM] --> INT
  INT --> CMD
  INT --> EVI[Evidence and Observation Store]
  EVI --> WF
  EVI --> QRY
  CMD --> OBJ[(Content-addressed source and evidence object store)]
  WF --> OBJ
~~~

**Critical trust boundary:** all callers (human, integration, agent) enter via authenticated API policy checks; graph/vector endpoints must not be exposed directly to untrusted principals. Retrieval authorization must apply before answer synthesis and across multi-hop links.

## 3. Platform modules and contracts

| Module | Responsibilities | Writes / reads |
|---|---|---|
| Identity & authorization | SSO/OIDC, service/mTLS identity, short-lived scopes, ABAC, capabilities, purpose and field guards | Policy decisions, audit |
| Registry & canonical store | IDs, types, revisions, baselines, authority, relationships, temporal semantics | Authoritative ledger |
| Native artifact service | Git/source connectors, content hashes, import/export, native URI bridge, lossiness reports | Immutable source references |
| Ontology/validation engine | Type checks, SHACL shapes, referential rules, semantic conflicts, owner/classification checks | Validation outcomes |
| Graph/query service | Viewpoints, baseline and time travel, access-aware traversal, impact closure, explainability | Rebuildable graph/index |
| Change studio | Intent/hypothesis, ChangeCases, ChangeSets, concurrency, decomposition, commitments | Change journal |
| Ownership/approval | Role assignments, delegations, materiality policy, required approver set, SoD | Decision records |
| Agent gateway | Version-pinned context, tool capabilities, execution sandbox, evaluation, proposal API | AgentRun and tool trace |
| Assurance | SACM-shaped claims, reproducible gates, freshness, waivers, release decisions | AssuranceCase + evidence refs |
| Delivery adapters | Issue/epic/story synchronization, Git, CI, OpenAPI/AsyncAPI, tests, SBOM and deploy signatures | Realizations/manifests |
| Observability/drift | Runtime source collection, conformance evaluation, discrepancies and operational reconciliation | Observations and DriftCases |
| Capability catalog | Profile/policy-by-scope, glossary, viewpoints, required artifacts, health and dependency ownership | Profile aggregate |
| Audit/analytics | Append-only traces, inventory KPIs, stale elements, control and approval latency | Reports |

**Consistency model:** authoritative mutation and outbox event commit in one database transaction; projections eventually consistent with watermark. Gate checks read a **consistent named snapshot** from the transaction store or a confirmed projection watermark, never silently from stale graph indexes.

## 4. Storage choice and source authority

Recommended initial deployable baseline (subject to enterprise constraints):
- **Relational DB** (e.g. PostgreSQL): stable elements, immutable revisions, baseline pointers, role bindings, change/approval/assurance aggregates, append-only events, outbox.
- **Object storage/Git**: source model files and evidence artifacts by SHA-256; cryptographic digest verified at ingest.
- **Graph read store** (RDF store, graph DB, or relational edge tables): typed, temporal, authorized traversals. Keep model abstraction independent from vendor choice.
- **Text search index**: language-aware search, identities and version filters; access-control-aware indexing.
- **Vector search** (optional): embeddings for authorized, low-risk documents/knowledge; vectors are derivative sensitive assets. Not an authority or substitute for typed links.
- **Message bus**: projection and reconciliation events; outbox/CDC with at-least-once delivery, idempotent consumers, poison-event quarantine.
- **Policy engine**: deterministic versioned rules for access, approval matrix, exception and gates; do not delegate enforcement to LLM.
- **Agent sandbox**: agent orchestration with scoped tools and explicit human checkpoints, compatible with AWS AI-DLC-style workflow stages but not coupled to one LLM provider.

### Persistence model (representative)

~~~text
knowledge_element(id PK, kind, namespace, home_capability_id, created_at, retired_at)
knowledge_revision(revision_id PK, element_id FK, parent_revision_id?,
  content_hash, content_ref, epistemic_status, lifecycle, valid_from, valid_to,
  recorded_at, provenance_ref, classification, metadata_json)
relationship_revision(edge_revision_id PK, relation_id, predicate, subject_revision_id,
  object_revision_id, source_ref, valid_from, valid_to, classification)
baseline(baseline_id PK, scope_id, name, recorded_at, policy_revision_id)
baseline_member(baseline_id FK, element_id FK, revision_id FK)
capability_profile(capability_id FK, profile_revision_id, policy_profile_id)
authority_assignment(element_scope_id, role_id, principal_or_group_id,
  delegated_by?, valid_from, valid_to)
change_case(id PK, state, intent_id, coordinator_id, scope_json, risk)
change_set(id PK, expected_baseline_id, diff_hash, state, proposer_id, ...)
change_operation(change_set_id, seq, type, element_id, before_revision_id?,
  after_revision_id?, edge_revision_id?)
approval(id PK, change_set_id, reviewed_diff_hash, reviewer_principal_id,
  reviewer_role_id, scope_id, policy_revision_id, verdict, signed_at)
assurance_case(id PK, release_scope_id, baseline_id, verdict, ...)
evidence(id PK, claim_id, subject_revision_id, source_hash, captured_at, expiry?)
release_manifest(id PK, environment, build_digests_json, knowledge_baseline_id, ...)
observation(id PK, subject_id, observed_at, method, confidence, source_hash, ...)
drift_case(id PK, expected_revision_id, observation_id, owner_id, status, ...)
event_ledger(sequence PK, aggregate_id, event_type, actor_id, event_hash, timestamp)
outbox(event_id PK, sequence, topic, payload, dispatched_at?)
~~~

The table is illustrative. Enforce transactional temporal overlap/integrity where feasible and journal every material authoritative mutation.

## 5. Primary APIs (proposed)

All APIs must support conditional writes (revision ETags/expectedRevision), idempotency keys and policy-derived redaction. Versioned REST is a viable starting interface; OSLC/RDF link representations are an interoperability option.

| Operation | Purpose |
|---|---|
| GET /v1/capabilities/{id}/profile?baseline=... | Capability responsibilities, required artifacts, health |
| GET /v1/elements/{id}?revision=... | Authorized canonical element and provenance |
| GET /v1/relationships?from=...&type=...&depth=... | Policy-filtered bounded graph traversal |
| POST /v1/knowledge/query | Typed query with scope, valid-at, recorded-as-of and state |
| POST /v1/impact-assessments | Compute impact, owners, controls and uncertainty |
| POST /v1/change-cases | Open an intent and alternative hypotheses |
| POST /v1/change-sets/validate | Pure validation: semantic diff, policy and concurrency |
| POST /v1/change-sets | Propose typed, expected-revision mutations |
| POST /v1/change-sets/{id}/submit | Freeze review scope and resolve approvers |
| POST /v1/approvals | Signed/recorded verdict referencing exact diff+policy hashes |
| POST /v1/baselines/promote | Controlled atomic authoritative promotion |
| POST /v1/assurance-cases/{id}/evaluate | Reproducible gate check for exact release versions |
| POST /v1/releases | Record immutable signed build, baseline and deploy linkage |
| POST /v1/observations | Ingest attested time-bounded operational finding |
| POST /v1/drift-cases/{id}/disposition | Triage/resolve discrepancy by authority |
| GET /v1/audit/events?subject=... | Authorized audit reconstruction |

### Example: semantic mutation contract

~~~json
{
  "changeSetId": "urn:kdesf:change-set:CS-42",
  "baseBaselineId": "urn:kdesf:baseline:retail:2026-11-01",
  "requestedEffectiveAt": "2026-12-01T00:00:00Z",
  "operations": [
    {
      "type": "modify",
      "elementId": "urn:kdesf:concept:party:customer-status",
      "expectedRevisionId": "urn:kdesf:revision:customer-status:v4",
      "proposedContentRef": "sha256:PLACEHOLDER_DIGEST",
      "rationale": "Align customer eligibility definition with approved decision"
    }
  ],
  "proposer": "urn:kdesf:actor:example-user",
  "idempotencyKey": "CS-42-v1"
}
~~~

The server computes canonical diff hash and impact before reviewers approve; clients must not supply authoritative “approved=true” flags.

## 6. Event and workflow architecture

Recommended domain events:
- KnowledgeImported; MappingCreated; ValidationFailed; ElementRevisionProposed;
- ChangeCaseOpened; HypothesisEvaluated; CommitmentGranted;
- ImpactCalculated; OwnerResolutionCompleted; ChangeSetSubmitted;
- ApprovalRecorded; ApprovalInvalidated; ExceptionGranted/Expired;
- BaselinePromoted; EvidenceReceived; AssuranceGateEvaluated;
- BuildVerified; ReleaseRecorded; DeploymentObserved;
- DriftDetected; DriftDispositionRecorded; KnowledgeReconciled.

Publish **redacted event views** to consumers based on scope; raw payloads may contain restricted names, existence or hashes. Events have immutable IDs, aggregate version, correlation/causation IDs, schema version, source actor/workload identity, timestamp and idempotency semantics.

### Semantic change sequence

~~~mermaid
sequenceDiagram
  participant P as Proposer/Agent
  participant C as Change API
  participant V as Validator/Impact
  participant O as Owner Resolver
  participant R as Human Reviewers
  participant B as Baseline Store
  participant I as Graph/Index
  P->>C: Propose ops + expected revisions
  C->>V: Validate semantics, policy, dependencies
  V-->>C: Diff hash, risk, impact graph
  C->>O: Resolve approvers for changed scopes
  O-->>C: Required role/scope decisions
  C-->>P: Proposal/required reviewers
  R->>C: Approve exact diff hash and policy version
  C->>B: Check revisions, all gates, commit promotion
  B-->>C: Immutable promotion event
  C-->>I: Outbox event
  I-->>C: Projection watermark
  C-->>P: Baseline revision + effective time
~~~

Change proposals can be created by agents, but the server enforces the review policy and freshness of revision, role assignment and evidence on promotion.

## 7. Knowledge context for AI agents

A **ContextManifest** is mandatory for any agent run that proposes or changes enterprise assets:
- run ID, model/provider and policy version;
- acting principal + delegated capability scope + expiry;
- query purpose, allowed classification, allowed element types;
- approved baseline IDs and effective time;
- exact source artifact IDs + revision hashes + retrieval decisions;
- allowed operations/tools and execution budgets;
- forbidden mutation/resource categories;
- untrusted input source labels and prompt-injection sanitization;
- unresolved ambiguities, coverage/freshness warning flags;
- resulting proposal IDs, calls, outputs and error handling.

**Agent principle:** reasoning can be probabilistic; **authorization, mutation semantics and gate evaluation must be deterministic**. Human approvals must not be spoofed by model-produced text. Models must receive only authorized inputs, not redacted output from an overprivileged graph or retrieval worker.

## 8. Import adapters and reverse engineering

**Adapter pipeline:** discover → authenticate and classify source → fetch immutable snapshot → parse natively → generate model fragments → map to canonical IDs with crosswalk → assess ambiguity/lossiness → validate → propose additions/updates → source-owner approval → publish projections.

Supported initial adapters:
1. Git: Markdown/front matter, JSON/YAML, schema and tests, PR links.
2. BPMN 2.0 XML and DMN XML, preserving subprocess, events, gateways, decision requirements and table semantics.
3. ArchiMate model exchange, preserving native relationships and viewpoints.
4. UML/XMI **subject to tool dialect compatibility** and explicit mapping fidelity.
5. OpenAPI / AsyncAPI with schema refs, version and operation IDs.
6. BIAN official reference catalog references using versioned catalog keys.
7. CI/CD, security and quality test artifacts; deployment and runtime telemetry.

**Reverse engineering rules:**
- Imported code classes → **inferred** structure, not business object authority.
- Observed endpoint responses → as-built evidence, not necessarily API contract intention.
- Database schema → *candidate* data concept, not guaranteed business meaning.
- Agent-produced mappings → confidence score and review queue.
- Legacy failures/conflicts → DriftCase; do not correct “reality” automatically.

## 9. Coherence checking and impact computation

Required checks (expanding in stages):
- schema/shape integrity, dangling links, hierarchy cycles, duplicate/conflicting names;
- capability ownership, scope overlap and policy applicability;
- cross-standard mapping fidelity and version validity;
- required artifacts by capability profile and gate;
- requirements ↔ process/decision/information ↔ interface ↔ code ↔ test coverage;
- API semantic, schema, consumer/producer compatibility;
- decision table/rule conflict and effective-date overlap;
- SOR authority collision, invalid state transitions, contract security mismatch;
- proof applicability, stale/expired evidence;
- runtime contradiction and unexplained deviations.

**Impact algorithm:** start with actual changed semantic IDs, follow policy-defined typed directed dependencies at a known baseline, include inbound consumers and controls, record paths/explanations and uncertainty. Return *definite*, *possible* and *unknown due to incomplete mapping/access*, not a single falsely precise total. Avoid giving inaccessible counts.

## 10. Security, privacy and tenancy boundaries

Segregate by jurisdiction/organization/domain/capability/classification and purpose. A “private repo” is not a sufficient security model for regulated enterprise knowledge. Configure:
- per-element and field access, contextual ABAC;
- graph traversal filtering and referential side-channel protection;
- object/embedding encryption, key segregation and tenant-specific credentials;
- agent scopes/short-lived delegated credentials and approval escalation;
- tenant- and classification-aware storage/index partitions;
- evidence integrity and consent/legal basis annotations where needed;
- sensitive payload minimization, deletion/retention procedures and disaster recovery.

See [07 — Security](07-security-and-controls.md).

## 11. Availability and disaster recovery

Operational commitments are configurable (initial aspirational objectives, not guarantees):
- RPO ≤15 minutes for platform metadata; signed source Git and immutable object storage have independent recovery controls.
- RTO ≤4 hours for pilot critical querying; mutations fail closed if policy/audit storage unavailable.
- 100% retriable outbox projection with idempotent replay; index rebuild from named ledger checkpoint.
- Daily point-in-time restore rehearsal in non-production, plus periodic cryptographic manifest verification.
- Bulk import and background graph builds must not starve interactive authorization and approval APIs.

## 12. Deployment sequence

**Phase A:** one region/environment, modular API, relational DB, source object store, simple adjacency graph/FTS, workers, CI.  
**Phase B:** enterprise SSO, policy/SoD, graph store, multi-adapter imports, model-quality dashboard, signed manifests.  
**Phase C:** federated capability tenancy, separate evidence/telemetry domain, full assurance, multi-region read resilience.  
**Phase D:** large-scale agent orchestration, differential conformance and self-healing *proposal* loops.

Choose cloud (AWS/Azure/GCP/on-prem) according to enterprise-approved sovereignty, security and hosting architecture. No assumption that a given managed service is approved for restricted bank data.

## 13. Architecture decisions required before implementation

ADR-01 canonical persistence: PostgreSQL+ledger with RDF projections versus RDF-first store.  
ADR-02 source model ownership: native tool authoritative with sync vs Git-authoritative files.  
ADR-03 identity URI and baseline time model.  
ADR-04 graph index and access filtering strategy.  
ADR-05 deterministic rule/OPA/Cedar-like policy engine choice and policy testing.  
ADR-06 evidence trust/signer model.  
ADR-07 agent runtime boundary, tool approval and retention.  
ADR-08 enterprise isolation/tenant strategy.  
ADR-09 standards import/export fidelity targets.  
ADR-10 technology-specific CI/CD and runtime adapters.
