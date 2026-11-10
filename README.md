# Knowledge-Driven Enterprise Software Factory

**Enterprise knowledge is the governed product. Software is one of its realizations.**

This repository contains a **detailed PRD and reference architecture** for an AI-native, knowledge-driven enterprise software factory serving complex federated enterprises (with a fictional European banking example). It combines business architecture, process and decision models, conceptual information semantics, logical interfaces, code, runtime observations and assurance into one **versioned, policy-governed knowledge graph**.

**Status:** Design blueprint **v0.1.0 (10 November 2026)**. This repository currently contains *specifications and synthetic fixtures, not a running implementation*. Nothing here claims adoption by a specific institution or regulatory certification.

## The proposition

The platform helps an enterprise answer, continuously:

- **What do we mean and why?** Strategy, outcomes, capabilities, processes, variants, decisions, information, service contracts, policies and constraints.
- **Who is accountable?** Scoped capability, process, information, decision, service, engineering, security and risk/control owners.
- **What is changing?** Signals → ideas → competing hypotheses → commitment → governed semantic diffs → code/configuration → release.
- **What proves it?** Acceptance criteria, tests, architecture conformance, assurance claims/evidence and immutable release manifests.
- **Does reality still match?** Approved vs target vs implemented vs observed state, with operational drift detection and governed reconciliation.
- **Can AI participate safely?** Yes—as authorized researcher, designer, implementer and proposer, **not an implicit semantic approver**.

~~~mermaid
flowchart LR
  S[Signal] --> H[Intent and hypotheses]
  H --> C[Commitment gate]
  C --> K[Typed knowledge change sets]
  K --> A[Scoped owner approvals]
  A --> D[Requirements, models and delivery]
  D --> T[Verification and assurance]
  T --> R[Release and observation]
  R --> X{Drift?}
  X -->|Yes| K
  X -->|No| O[Evidence-backed enterprise state]
  B[Capabilities, processes, decisions, concepts, interfaces, controls] --> K
  B --> D
~~~

## Read the blueprint

| Document | Contents |
|---|---|
| [01 — Product requirements](docs/01-product-requirements.md) | Vision, pain points, personas, features, boundaries, success metrics, invariants |
| [02 — Canonical ontology & metamodel](docs/02-canonical-metamodel.md) | Knowledge/Revision/Relationship, artifact taxonomy, capability knowledge profiles, typed operations |
| [03 — Standards & viewpoints](docs/03-standards-and-viewpoints.md) | TOGAF, ArchiMate, BPMN, DMN, UML, BIAN, API4KP, Essence/SPEM, SACM, architecture viewpoints |
| [04 — Reference platform architecture](docs/04-reference-architecture.md) | Components, storage, APIs, events, authorization boundary, adapters, AI, deployment |
| [05 — Governance, roles & ownership](docs/05-governance-roles-and-ownership.md) | Artifact-level accountability, per-capability roles, RACI, delegation, conflict resolution |
| [06 — Change, delivery & assurance](docs/06-change-delivery-assurance.md) | Signal to hypothesis to commitment to code to assurance to drift reconciliation |
| [07 — Security & controls](docs/07-security-and-controls.md) | Proposed L1–L4 access model, ABAC, graph leakage, AI isolation, SoD, regulatory control mapping |
| [08 — Testable functional requirements](docs/08-functional-requirements.md) | Prioritized requirements, NFRs and Given/When/Then end-to-end tests |
| [09 — Roadmap & open decisions](docs/09-roadmap-and-open-decisions.md) | Rollout phases, pilot definition, ADR queue and uncertainty |
| [10 — Fictional onboarding example](docs/10-worked-example.md) | End-to-end illustrative multi-capability semantic change |
| [11 — Glossary & source references](docs/11-glossary-and-references.md) | Terminology, source standards, limits and IP/conformance cautions |

### Machine-readable starter models

- [Knowledge Element JSON Schema](spec/schemas/knowledge-element.schema.json)
- [Capability Profile JSON Schema](spec/schemas/capability-profile.schema.json)
- [Semantic ChangeSet JSON Schema](spec/schemas/change-set.schema.json)
- [Scoped Approval JSON Schema](spec/schemas/approval.schema.json)
- [KDESF core OWL/Turtle vocabulary](spec/ontology/kdesf-core.ttl)
- [KDESF starter SHACL constraints](spec/ontology/kdesf-shapes.ttl)
- [Synthetic customer onboarding example](spec/examples/customer-onboarding.json)

The machine-readable models are a **partial starter contract**, not a complete implementation of the written metamodel and not fully conformant adapters to the referenced standards. The example includes explicitly non-verifiable synthetic digest placeholders. Real systems must calculate content hashes and run contextual policy/temporal/SoD checks beyond schema validation.

## Architecture principles

1. **Govern knowledge, not documents alone.** Model a stable element, its immutable revisions, source artifacts, typed relationships, scope and provenance separately.
2. **Preserve native semantics.** A BPMN process, UML class, DMN decision and ArchiMate capability are linked deliberately; they are not flattened into one generic diagram.
3. **One accountable owner per scope.** Shared meaning uses explicit coordination and specialized co-approvals, never artificial universal ownership.
4. **Separate truths.** Approved/current, approved target, proposed/inferred, implemented and observed views cannot silently overwrite each other.
5. **Approve exact semantic deltas.** Material changes are reviewed by affected information/process/rule/API/control owners at a frozen diff hash.
6. **Commit progressively.** Business value and risk can justify commitment before detailed design is complete; deferred decisions become explicit obligations at later gates.
7. **Assure implementation and operation.** Build/test/release evidence must prove exact declared versions, while runtime drift creates reviewable work.
8. **AI never creates authority.** Model output is a proposal or observation until deterministic validation and required human governance.
9. **Security follows every edge and field.** Classifications propagate into graph traversal, search, agent contexts and exported views.
10. **Start with an end-to-end pilot.** One meaningful multi-capability change is more valuable than an enormous unassured diagram inventory.

## Modeling standards

This reference design interoperates with **TOGAF 10**, **ArchiMate 3.2**, **BPMN 2.0.2**, **DMN 1.6**, **UML 2.5.1**, **BIAN 14.0**, **ISO/IEC/IEEE 42010:2022**, **OMG API4KP 1.0**, **Essence 1.2**, **SPEM 2.0**, and **SACM 2.3**, with RDF/OWL/SHACL/PROV-O, OpenAPI, AsyncAPI and OSLC where useful. These have **different purposes**, and their mappings are proposed, not a claim of an existing official integrated standard. See [standards analysis](docs/03-standards-and-viewpoints.md).

## Suggested pilot

Model a fictional cross-capability customer onboarding change: a process variant calls a new decision; the decision uses an Information Owner-approved concept; the logical service has a technical API contract; the code and test evidence prove exactly those versions; operations expose a seeded drift. Demonstrate at least two distinct semantic approval authorities and an AI agent that can propose but not self-approve.

Read the [worked example](docs/10-worked-example.md) and [delivery roadmap](docs/09-roadmap-and-open-decisions.md).

## How to use this repository

- **Architects and business owners:** start with 01, 02, 03 and 05; review boundary definitions and ownership before implementation.
- **Product and transformation leads:** use 01, 06, 08 and 09 as the initial PRD/backlog and delivery gate definitions.
- **Engineers/platform teams:** use 02, 04, 07, 08 and the schemas as source material for explicit ADRs and proof-of-concept APIs.
- **Risk/security/compliance:** validate 05–07 against *your organization's actual* policies before adapting any design.
- **Agent developers:** begin with the ContextManifest and server-side policy requirements in 04/06/07, not an unscoped RAG demo.

## Status and next work

- [x] PRD, canonical artifact taxonomy, ontology and relationships
- [x] Version-aware standards mapping and architecture viewpoints
- [x] Reference platform, governance roles, approval policy and security model
- [x] Knowledge change/assurance/drift lifecycle
- [x] Prioritized acceptance requirements and fictional banking example
- [x] Starter JSON Schemas and RDF/SHACL files
- [ ] Tested, deployable backend/UI and database migrations
- [ ] Executable model import/export adapters and full round-trip fixtures
- [ ] Working server-side authorization/SoD and policy tests
- [ ] Real signed evidence, approval and release gates
- [ ] Production telemetry and runtime conformance connectors
- [ ] Enterprise owner validation and concrete ADR decisions

**License/standards note:** No repository-wide license has been selected. Referenced frameworks and standards retain their respective intellectual-property terms. This repository does not reproduce their full specifications and must not be interpreted as a compliance attestation.
