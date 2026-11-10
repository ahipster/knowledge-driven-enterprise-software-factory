# 09 — Delivery roadmap, implementation slices, ADRs and unresolved decisions

**Status:** Proposed phasing, not committed dates/costs. Prioritize a vertically coherent capability slice over broad but shallow enterprise modeling.

## 1. Product strategy

The highest-risk uncertainty is not whether an LLM can generate code. It is whether the organization can maintain **reliable knowledge identity, ownership, authorization, scoped changes, and evidence links** across heterogeneous model stores.

Build the **knowledge governance substrate** first, then add AI and realization automation that relies on it. Avoid attempting to model the whole enterprise before demonstrating a real cross-capability change.

## 2. Releases and exit criteria

| Release | Core scope | Primary demonstration | Exit condition |
|---|---|---|---|
| R0 — Blueprint and governance | Ontology core, role/approval policy, classification, approved modeling conventions, reference taxonomies | Dry-run sample cross-capability ChangeCase | Real owners agree type vocabulary, trust boundaries and gate policy |
| R1 — Knowledge registry | Canonical IDs/revisions/links, sources, two capabilities, source imports, baseline snapshots | Find “what is the current definition and owner?” | Stable IDs; immutable revisions; classified queries; import fidelity |
| R2 — Governed semantic change | ChangeCase/requirements, typed ChangeSet, impact, required reviewer resolution, per-element approvals and promotions | One data concept + process change with two distinct owners | No stale/review-bypassing promotion; audit reconstruction |
| R3 — Delivery traceability | Logical interface, API contracts, Git/CI tests, release/assurance manifests, agent proposal | From business requirement to deployed artifact and proof | Release pinned to exact approved semantic target and evidence |
| R4 — Operational conformance | Runtime observations, drift cases, owners, correction/exception pathways | Detect mismatch and reconcile knowledge/code | At least one demonstrated false positive and one real drift |
| R5 — Federation and scale | More capabilities, external sources, catalog/search, richer semantic rules, policy partitioning, agent workflow | Multi-capability E2E change at organization scale | Security/load/DR metrics validated and operability proven |

## 3. Suggested pilot boundaries

**Fictional banking example:** Customer Onboarding with Party & Customer Information and Identity Verification integrations. Use a small but representative set:
- 2–3 capabilities and their owners;
- 1 cross-capability customer-onboarding process with ≥2 documented variants;
- 10–20 business concepts / important attributes;
- 2–4 decisions, including one DMN table;
- 2 logical interfaces with OpenAPI/AsyncAPI technical realizations;
- 5–10 requirements and related acceptance tests;
- 1 policy-driven security/control approval;
- 1 deliberately seeded semantic or runtime drift;
- 1 end-to-end simulated release.

All names and artifacts must be invented or approved for external sharing; the public reference specification should not contain production bank schemas, customer data or security topology.

## 4. Workstreams and deliverables

### WS-A — Semantics and methodology
- Canonical ontology v1 and invariants (element vs revision vs source vs assertion).
- Mapping policy ArchiMate/TOGAF/BPMN/DMN/UML/BIAN.
- CapabilityProfile minimum artifact requirements.
- Stakeholder/concern/viewpoint catalog aligned to ISO 42010.
- Approved temporal/baseline and inference/epistemic policy.

### WS-B — Governance and identity
- Enterprise role bindings and capability ownership.
- Classification and attribute access design.
- Approval resolution rules, delegation, SoD, emergency policy.
- Audit append-only journal, exception/expiry.
- Secure agent execution/trust boundaries.

### WS-C — Platform
- Transactional canonical store and immutable revision API.
- Native import + provenance/crosswalk.
- Graph/read model/query with authorization and time selectors.
- ChangeSet validation, impact and promotion.
- Authenticated workbench pages and search.

### WS-D — Assurance and integration
- Git/CI/CD/API contract/test adapters.
- SACM-inspired claim/evidence registry.
- Reproducible policy gate evaluator and signed release manifest.
- Runtime observation ingestion and drift triage.
- Source/version conformance reports.

### WS-E — Agents and user experience
- Knowledge discovery with citations/source revisions.
- Capability and change workspace with semantic diff visualization.
- Agent ContextManifest, proposal tools, deterministic guardrails.
- Agent evaluation corpus and injection/unauthorized-action tests.
- Feedback flow that converts validated corrections to governed knowledge.

## 5. Prioritized implementation backlog

| Order | Slice | Dependencies | Demo |
|---|---|---|---|
| 1 | Element/revision/baseline CRUD and hashes | Identity URI strategy | Historical semantics survive edit |
| 2 | Capability ownership + role scopes + classification | Policy definition | Two users see correct different slices |
| 3 | Typed relation and graph traversal | Registry, ABAC | “What depends on X?” with access protections |
| 4 | Source import of structured JSON + BPMN | Crosswalk | Native process maps to canonical source |
| 5 | ChangeSet typed diff + optimistic concurrency | Revision store | Reject stale modification |
| 6 | Owner resolution + scoped sign-offs | Role/SoD policy | Two owners approve different diffs |
| 7 | Commitment + hypothesis + requirement | ChangeCase model | Commit without all details |
| 8 | Logical interfaces + OpenAPI + basic tests | Semantic concepts | Validate an API against logical contract |
| 9 | Test/evidence and assurance release gate | Build metadata | Block wrong-version evidence |
| 10 | Agent read/propose tool gateway | Authorization + context | Agent proposes without power to approve |
| 11 | Observed state + DriftCase | Deployment/evidence | Reconcile an intentional mismatch |
| 12 | UML/ArchiMate/DMN richer adapters | Adapter spec | Cross-standard round-trip fidelity review |

## 6. Pilot experiment questions

- Can capability owners understand and approve a **semantic diff** without opening a modeling tool?
- Can the system distinguish a meaningful policy change from cosmetic model edits?
- Can it resolve the true approvers when a requirement changes a shared concept?
- Does graph-based impact analysis identify more affected assets than human-only review, and with what false-positive rate?
- Can an agent create useful drafts without being granted business authority or sensitive context?
- Can assurance prove exact deployed behavior, or are all artifacts merely linked?
- Does runtime observation find real knowledge drift rather than tool synchronization noise?
- Can teams still use their established modeling/engineering tools without loss of meaning?

## 7. Architecture Decision Record queue

| ADR | Decision | Options / criteria |
|---|---|---|
| ADR-001 | Canonical persistence | PostgreSQL+event/graph projections vs RDF-native; transactions, policy, cost |
| ADR-002 | Stable identity namespace | URN vs HTTPS URI; portability, federation, rename policy |
| ADR-003 | Native-model source authority | Source tool master vs Git master vs domain-per-artifact |
| ADR-004 | Temporal model | Valid time + recorded time + baseline; audit and query complexity |
| ADR-005 | Cross-standard mapping representation | RDF/OWL/SKOS vs typed relational edge profiles |
| ADR-006 | Authorization engine | Cedar/OPA/custom; field/traversal guards, reasoning, audit |
| ADR-007 | Capability ownership conflicts | Arbitration and coordinating authority |
| ADR-008 | Knowledge promotion model | Atomic baseline snapshot vs per-domain monotonic baselines with dependency fences |
| ADR-009 | Agent execution harness | AWS AI-DLC-inspired staging vs internal orchestrator; portability |
| ADR-010 | Evidence integrity | Signature, external attestations, chain of custody |
| ADR-011 | Graph/search strategy | Postgres edges, graph DB or RDF/SPARQL; temporal/query ACL |
| ADR-012 | Source standards versions | Supported import/export versions and licensing |
| ADR-013 | Requirement progression | Level of detail and gates per risk/criticality |
| ADR-014 | Logical service design | Contract syntax, version/compatibility and BIAN mapping |
| ADR-015 | Federation | Cross-capability/enterprise publishing, classification downgrade policy |
| ADR-016 | Runtime facts | OpenTelemetry and deployment record models, conformance proof thresholds |
| ADR-017 | Security tier mapping | Enterprise policy 1–4 and domain/jurisdiction overrides |
| ADR-018 | Audit retention and deletion | Records regulation, privacy and evidence preservation |
| ADR-019 | Governance automation | Deterministic rules, delegated approvals and emergency workflow |
| ADR-020 | Deployment topology | Region, networks, data sovereignty, isolated agent execution |

Every ADR should contain context, decision, alternatives, consequences, affected metamodel elements and later review trigger.

## 8. Unknowns requiring design discovery

1. Which modeling products/formats can the enterprise legally and technically export? What fidelity is possible?
2. What is the existing formal authority matrix for business concepts, data, processes, services and controls?
3. Can the organization assign one accountable authority per information fact and effective scope?
4. Which information-classification policy is actually in force? Is purpose-based L3 acceptable?
5. Which model/provider runtimes may receive restricted architecture content?
6. Which approval types may be automated, delegated or grouped under existing regulation/policy?
7. What does “knowledge in sync with production” mean operationally for non-executable semantics?
8. What graph completeness level is needed for an impact report to be trustworthy?
9. Which artifacts should be modeled at enterprise, domain, capability, process and solution scopes?
10. How should multi-enterprise collaboration or outside contractor access work?
11. Which control framework needs detailed risk/evidence mapping, and for what jurisdictions?
12. Who becomes the product owner of shared ontology/meta-schema and cross-capability arbitration?

Unresolved items must be visible as design questions, **not silently treated as established standards or actual enterprise policies**.

## 9. Exit gates for moving beyond prototype

**Governance:** genuine capability/information/decision owners exercise approval in a pilot; not simulated by developers.  
**Security:** L3/L4 negative tests and agent prompt-injection tests pass under a real policy engine.  
**Semantic integrity:** source mappings, revisions and diff hashes reproducible; no hidden data loss.  
**Delivery:** one release demonstrably linked to business outcome, normative semantics, actual code, tests and operation.  
**Assurance:** independent evidence assessment distinguishes actual coverage from merely attached documents.  
**Value:** measured faster impact assessment or fewer escaped change defects; do not claim ROI from document counts.

## 10. Suggested next concrete build

Create a thin vertical slice: **“Change an onboarding eligibility rule that reuses a Party concept and is exposed through a customer-status API.”** Start with two fictitious capability profiles, one process variant, a DMN decision, a UML concept, a logical interface and a synthetic API contract. Demonstrate the full semantic delta, the two distinct owners, deterministic tests, approval invalidation after rebase, an agent proposal and drift detection. Expand standards adapters after that path works.
