# 08 — Functional requirements, non-functional requirements and acceptance tests

**Status:** Proposed backlog-level requirements specification. IDs are stable and can map into an issue tracker; priority **P0** = must-have for secure pilot, **P1** = pilot expansion, **P2** = later.

## 1. Acceptance conventions

- Each requirement has a unique ID, priority and **objectively testable** condition.
- Gate checks may be synchronous or asynchronous; asynchronous jobs have explicit terminal result and deadline.
- Authorization behavior must be tested both for visible content and inferred/existence side channels.
- Pilot requirements reference the [01 PRD](01-product-requirements.md), [02 Metamodel](02-canonical-metamodel.md), [06 Lifecycle](06-change-delivery-assurance.md), and [07 Security](07-security-and-controls.md).
- “User” means authenticated authorized human/integration/agent, with scoped permissions and policy applied.

## 2. Knowledge registry and temporal semantics

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| KNO-001 | P0 | Register typed KnowledgeElement with stable ID | New element yields immutable ID, type, scope and recorded provenance |
| KNO-002 | P0 | Store immutable revisions independently of element identity | Old revision readable as-of time; hash unchanged after new revision |
| KNO-003 | P0 | Bind elements to active baseline snapshots | Query named baseline returns precisely its assigned revisions |
| KNO-004 | P0 | Distinguish approved/proposed/implemented/observed/inferred states | Same element query can return each state without overwriting |
| KNO-005 | P0 | Store typed directed relationships with source and validity | Invalid type/edge rejected; valid edge is time/version queryable |
| KNO-006 | P0 | Require authority, classification and home scope at governable baseline | Promotion fails if missing; drafts may record known gaps |
| KNO-007 | P0 | Support optimistic concurrency and rebase detection | Stale expected revision rejects promotion with conflict details |
| KNO-008 | P1 | Support effective-at and recorded-as-of queries | Two independent temporal filters return correct past state |
| KNO-009 | P1 | Support split/merge/retire/supersede lineage | Old ID retains lineage and historical references |
| KNO-010 | P1 | Prevent accidental dual exclusive owners and circular decomposition | Validator flags conflicting ownership/cycles |

## 3. Capability and model registry

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| CAP-001 | P0 | Every governed capability has a CapabilityProfile | Profile declares charter, owner, scope, policy profile and dependencies |
| CAP-002 | P0 | Compute required knowledge by capability profile and risk | Missing, not-applicable and complete statuses distinguishable |
| CAP-003 | P0 | Support federated semantic owner assignment | Two capabilities can have distinct concept owners and cross-capability review |
| CAP-004 | P1 | Render capability maturity and traceable readiness | Readiness score linked to underlying required artifact IDs |
| CAP-005 | P1 | Display capability and value-stream views | Navigable model with owner/scope/baseline filters and no privilege leaks |
| MOD-001 | P0 | Import at least one native source model with provenance | Every projected element has source URI, hash and mapping rule version |
| MOD-002 | P0 | Preserve unsupported constructs and report losses | Import report lists omitted/unmappable structures, never silent drop |
| MOD-003 | P1 | Support BPMN 2.0 XML and DMN XML model adapters | Process variant and decision rule fragments cross-linked with source |
| MOD-004 | P1 | Support ArchiMate model exchange adapter | Preserves native types, legal links, views, external IDs |
| MOD-005 | P1 | Support UML/XMI adapter with dialect limitations disclosed | Mapped class/attribute invariants and import fidelity report |
| MOD-006 | P1 | Store BIAN catalog version and reviewed reference mappings | BIAN entry cannot automatically become a local service or capability |
| MOD-007 | P2 | Export compatible native models where reversible | Round-trip comparison measures all relevant preserved semantics |

## 4. Intent, hypothesis and requirements

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| CHG-001 | P0 | Capture Signal with source, scope and provenance | Source cannot be absent in triaged state |
| CHG-002 | P0 | ChangeCase supports multiple alternatives | Two hypotheses independently evaluated/selected/rejected with rationale |
| CHG-003 | P0 | Commitment gate accepts intentionally incomplete details | Committed case records required scope/owners/risks and explicit deferred decisions |
| CHG-004 | P0 | Requirement is structured, typed and linked to intent | Can trace requirements to goals, affected elements, tests |
| CHG-005 | P0 | Require acceptance evidence strategy before verification gate | G3 cannot pass without relevant linked verification method/result |
| CHG-006 | P1 | Support non-tree delivery grouping and requirement refinement | One requirement can map to multiple stories and releases |
| CHG-007 | P1 | Decomposition and process variants are distinct dimensions | Variants declare applicability without duplicating ProcessFamily identity |
| CHG-008 | P1 | Impact analysis traces likely affected elements and roles | Paths are explainable, include confidence and unresolved dependencies |
| CHG-009 | P1 | Change from selected hypothesis to detailed model is incremental | New semantic requirements can be added with scoped approval after commitment |

## 5. Semantic ChangeSet and governance

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| GOV-001 | P0 | Submit atomic typed semantic operations | Each operation has type, target, expected revision, rationale and diff |
| GOV-002 | P0 | Compute and freeze canonical diff hash on submission | Review references same hash; changed semantics invalidate approval |
| GOV-003 | P0 | Resolve required approvers by type, scope, risk and policy | Resolver returns roles and reasons deterministically |
| GOV-004 | P0 | Verify live reviewer role and delegation | Revoked/expired reviewer role cannot authorize promotion |
| GOV-005 | P0 | Enforce SoD for designated high-risk changes | Same actor cannot propose+solely approve a disallowed change |
| GOV-006 | P0 | Promotion is conditional on approved exact revisions and policy | Missing mandatory decision blocks baseline update |
| GOV-007 | P0 | Every governance decision is auditable and immutable | Actor, authority, scope, diff and policy version reconstructible |
| GOV-008 | P1 | Handle cross-capability mixed ownership | Distinct semantic diffs require respective owner decisions |
| GOV-009 | P1 | Support expiry-bound waiver/exception | Expired waiver cannot satisfy a release gate |
| GOV-010 | P1 | Support asynchronous batched review without losing granularity | Group approval records element sets and diff hash exactly |
| GOV-011 | P1 | Validate baseline dependencies across ChangeSets | Incompatible partially promoted states are rejected |
| GOV-012 | P1 | Approval policy is versioned and governed as knowledge | Policy update cannot alter historical verdict inputs retroactively |

## 6. Interfaces, implementation and knowledge conformance

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| INT-001 | P0 | Model logical interface separately from technical contract | One logical operation can reference multiple protocol realizations |
| INT-002 | P0 | Link code/test/release evidence to knowledge revision | Release manifest resolves exact hash and normative baseline |
| INT-003 | P1 | Validate OpenAPI/AsyncAPI contract versions and links | Missing semantics or breaking change produces policy warning/block |
| INT-004 | P1 | Track consumer and provider compatibility separately | Unreviewed breaking producer change identified with consumers |
| INT-005 | P1 | Validate data concept mappings and authority conflicts | Overlapping SOR authority in same scope/time causes finding |
| INT-006 | P1 | Track decision-table execution coverage | Test evidence refers to exact decision revision and expected hit policy |
| INT-007 | P2 | Check process/decision/information consistency in graph | Counterexample scenario produces reproducible validator finding |

## 7. Assurance, runtime and drift

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| ASS-001 | P0 | Represent assurance claims with evidence and assumptions | Claim references requirement, revision and evidence applicability |
| ASS-002 | P0 | Release gate evaluates exact pinned target/build/test versions | Stale test/build blocks gate unless explicit valid waiver |
| ASS-003 | P0 | Create immutable ReleaseManifest for authorized release | Manifest records target baseline, build, verdict, decision and environment |
| ASS-004 | P1 | Ingest observed state as separate epistemic class | Observation never replaces approved state on ingest |
| ASS-005 | P1 | Detect material discrepancy and open DriftCase | Contradictory observation links expected revision and proof |
| ASS-006 | P1 | Route drift to responsible semantic/operations owners | Resolution policy creates appropriate owner queue |
| ASS-007 | P1 | Close drift only with supported disposition | Closed drift references correction/approved change/waiver/false-positive proof |
| ASS-008 | P2 | Refresh proof continuously on code/deploy/policy change | Dependent claims mark stale on underlying version change |

## 8. Search, AI and traceability

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| QRY-001 | P0 | Search by typed concept, scope and approved baseline | Results contain IDs, revisions, provenance and access context |
| QRY-002 | P0 | Deny unauthorized entity/field/edge retrieval | Negative tests across graph depth, export and summaries pass |
| QRY-003 | P1 | Return “unknown/partial” when model graph incomplete | No fabricated missing link or unauthorized hidden-node count |
| QRY-004 | P1 | Explain impact paths and source fidelity | User can inspect provenance for each asserted dependency |
| AI-001 | P0 | Every agent tool invocation is server-side authorized | Disallowed mutation/tool call rejected regardless of prompt |
| AI-002 | P0 | Agent context is version- and scope-pinned | ContextManifest records exact permitted element revision hashes |
| AI-003 | P0 | Agent cannot self-approve normative changes | Proposal can be created; baseline promotion requires authority |
| AI-004 | P1 | Agent distinguishes verified/inferred/proposed statements | Candidate findings flagged until validated |
| AI-005 | P1 | Prompt-injection and retrieval exfiltration evaluated | Malicious source cannot escalate tools or access classification |
| AI-006 | P2 | Agent can propose repair from drift with auditable rationale | Proposal links drift, observations, target revisions and tests |

## 9. Enterprise security and quality

| ID | Priority | Requirement | Acceptance |
|---|---|---|---|
| SEC-001 | P0 | Authorize every read and write via identity + policy context | Anonymous and cross-tenant access denied |
| SEC-002 | P0 | Enforce field-/row-/edge-level security and classifications | Restricted field never appears in low-privilege graph, cache or response |
| SEC-003 | P0 | Scope L3 to purpose/capability and L4 to explicit need-to-know | Cross-capability and time-expired grants fail |
| SEC-004 | P0 | Audit review, mutation, release, agent acts and privileged access | Audit reconstruction matches versioned policy input |
| SEC-005 | P0 | Fail closed when policy/audit/evidence integrity unavailable | No silent promotion under dependency outage |
| SEC-006 | P1 | Respect residency/retention policies in model/evidence and LLM integrations | Disallowed external destination blocked by integration tests |
| OPS-001 | P0 | Track projection watermark vs authoritative commit version | Reads expose freshness; gate is not stale-index dependent |
| OPS-002 | P1 | Outbox consumers are replayable/idempotent | Duplicate delivery yields no duplicate authoritative mutations |
| OPS-003 | P1 | Rebuild graph/search indexes from authoritative records | Rebuild output matches checkpoint and classification |
| OPS-004 | P1 | Track model adapter fidelity and staleness | Missing or old source versions surface in dashboard |
| OPS-005 | P2 | Time-travel audit of enterprise knowledge | Authorized reconstruction of before/after release and approval context |

## 10. Non-functional acceptance benchmarks (pilot hypotheses)

| Dimension | Provisional target | Verification |
|---|---|---|
| Read latency | p95 single authorized element lookup ≤500 ms at 100k elements | Load test across classification classes |
| Graph impact | p95 configured ≤2-hop bounded traverse ≤3 s at 100k elements | Seeded test dataset with high fanout and privilege filtering |
| Approval integrity | 0 improperly promoted semantic revisions in security and concurrency suites | Policy mutation, SoD and rebase property tests |
| Data integrity | All promoted revisions hash-verifiable and reconstructible | Snapshot restore and audit replay |
| Index freshness | Normal event-to-query projection lag p95 ≤30 s | Outbox instrumentation and failure injection |
| Scale | Pilot supports ≥100k elements, ≥500k typed edges and ≥100 concurrent readers | Benchmark; tune before broader roll-out |
| Availability | No unauthorized write under DB, index, policy or evidence outages | Chaos tests |
| LLM safety | 100% of forced escalation tests denied without independent human authorization | Red-team prompt-injection corpus |
| Model fidelity | 100% of unrepresentable native structures explicitly reported | Adapter conformance fixtures |
| Usability | ≥80% of pilot users complete scoped reviewer task without specialist intervention | Usability tests |
| Accessibility | WCAG 2.2 AA objective, subject to enterprise standard | Automated and human accessibility review |

All numerical targets are **proposed acceptance criteria**, not measured product performance.

## 11. End-to-end test scenarios (Given / When / Then)

**E2E-01 — Normal cross-capability semantic change**
- Given active Customer Onboarding and Party Information owners plus pinned baseline v3,
- When a ChangeCase proposes a process change and a customer concept change,
- Then independent accountable reviewers see exact diffs, approve per scope, and promotion emits linked revisions and audit events.

**E2E-02 — Revocation**
- Given an approved diff with a reviewer whose authority is revoked before effective promotion,
- When the promotion gate evaluates current policy,
- Then it blocks or follows a documented revocation-effective rule and requests a valid reviewer.

**E2E-03 — Agent-proposed unauthorized change**
- Given an agent allowed read/propose but not approve,
- When untrusted repository text instructs it to promote a business rule,
- Then the server refuses promotion and logs a policy denial, regardless of generated text.

**E2E-04 — Changed diff after approval**
- Given approved change hash A,
- When the proposer rebases and produces hash B,
- Then relevant decisions for A no longer satisfy the gate.

**E2E-05 — Runtime drift**
- Given a production release bound to rule revision D5,
- When measured behavior contradicts D5 within policy applicability,
- Then a DriftCase references exact observation, current deployment and D5, and cannot close without recorded disposition.

**E2E-06 — Authorized partial graph**
- Given an L3 user exploring a process whose related security asset is L4,
- When graph query/search/AI assistant traverses those edges,
- Then the restricted asset/content/count is not revealed, and results do not assert false completeness.

**E2E-07 — Deferred requirements**
- Given committed business outcome with explicitly deferred API details,
- When the project designs that API later,
- Then the commitment remains valid if its declared invariants hold and separate semantic approval gates cover the new details.

**E2E-08 — Model lossiness**
- Given a BPMN source with a timer boundary event,
- When the adapter generates an ArchiMate-level process projection,
- Then the timer is retained in source provenance and the projection's reduced fidelity is disclosed.

**E2E-09 — SOR collision**
- Given System A is authoritative for Customer Status in scope S/time T,
- When another declaration makes System B exclusively authoritative for the same fact/scope/time,
- Then validation blocks overlapping normative approval or obtains conflict resolution.

**E2E-10 — Incomplete evidence**
- Given green tests for build B1 and candidate release B2,
- When assurance evaluates B2 without applicable evidence,
- Then previous green evidence cannot make the new release pass.

## 12. Definition of done by tranche

**MVP secure pilot:** KNO-001…007, CAP-001…003, MOD-001…002, CHG-001…005, GOV-001…007, INT-001…002, ASS-001…003, QRY-001…002, AI-001…003, SEC-001…005, OPS-001; integration tests E2E-01,02,03,04,06,07,10.

**MVP+ enterprise pilot:** native model adapters, domain mapping, multi-capability shared contracts, drift cases, assurance coverage, visibility/readiness, stronger temporal queries; E2E-05,08,09.

**Scale stage:** inference/observation reconciliation, continuous validation, richer export/round-trip fidelity, enterprise federation and broader scale metrics.

A requirement is complete only when a real implementation/test demonstrates it; this document alone does not prove fulfillment.
