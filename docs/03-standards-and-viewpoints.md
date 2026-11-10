# 03 — Standards integration, modeling policy and required viewpoints

**Status:** Proposed interoperability blueprint, version-pinned as of 2026-11-10. This design uses standards as complementary *languages, methods and reference taxonomies*; it does not claim that standards together provide one ready-made software-factory metamodel.

## 1. Standards and their jobs

| Standard/framework | Use in KDESF | What it does **not** provide by itself |
|---|---|---|
| ISO/IEC/IEEE 42010:2022 | Define architecture descriptions, stakeholders, concerns, viewpoints and model kinds | Concrete enterprise-wide data model or development method |
| TOGAF Standard, 10th Edition | ADM, architecture governance, architecture repository, baseline/target, migration and change management | Precise BPMN semantics, executable API contracts or domain ontology |
| ArchiMate 3.2 | Motivation, strategy/capabilities, business, application, technology, implementation/migration viewpoints | Detailed executable behavior or complete conceptual data semantics |
| BPMN 2.0.2 | Business process sequencing, events, gateways, participants, subprocesses, exceptions | Enterprise capability taxonomy, business decision logic |
| DMN 1.6 (formal, Sep 2026) | Decision requirements and precise decision logic/tables | End-to-end business process execution design |
| UML 2.5.1 | Conceptual/logical class models, state machines, use cases, sequence/activity models | Automatically correct information ownership, enterprise architecture governance |
| BIAN Service Landscape 14.0 (2026) | Banking reference service domains, business objects, scenarios, semantic APIs, ISO 20022 mappings where available | An organization's *actual* domain/service/API/application decomposition |
| OMG API4KP 1.0 | Knowledge asset identity, knowledge repository/API concepts, discoverability and lifecycle interoperability | Complete governance/architecture/change implementation |
| OMG Essence 1.2; SPEM 2.0 | Work definitions, roles, practices, work products and progress health | Enterprise normative business semantics |
| OMG SACM 2.3 | Claim–argument–evidence structures for assurance | Actual verification or assurance decision by itself |
| OMG BACM 1.1 | Optional mapping to formal business architecture concepts | One universal enterprise capability classification |
| DDD / SOA / SoaML-informed design | Bounded contexts, ubiquitous language, contracts, service boundaries | A mandated 1:1 mapping among BIAN, teams, capabilities and microservices |
| OpenAPI 3.x / AsyncAPI 3.x / JSON Schema / protobuf | Implementable transport and data contracts | Full business meaning and authorization policy |
| W3C RDF/OWL/SHACL/PROV-O/SKOS | Graph semantics, shape checking, provenance, semantic mapping | Whole regulated authorization and temporal workflow engine |
| OSLC Core 3.0 | Linked resource identities and cross-tool traceability | Enterprise ownership and gate rules |
| AWS AI-DLC / agent harnesses | A useful delivery phase/stage/agent/artifact reference implementation | Enterprise knowledge authority or comprehensive banking ontology |
| SAFe-like agile delivery | Portfolio/initiative/epic/feature/story/timebox planning as *delivery projections* | Semantic ownership, evidence integrity or requirements ontology |

**Version policy:** Pin importer/exporter to a declared standard version. Do not “auto-upgrade” model semantics. Verify that the licensed source model content can be redistributed before copying into this repository. Reference external standards by citation/URI rather than mirroring protected content.

## 2. Source of truth per semantic concern

| Concern | Authoritative model/artifact class | Derived/projection lenses |
|---|---|---|
| Strategy, capability composition, cross-layer enterprise architecture | Capability charter + governed ArchiMate model / EA repository | Capability maps, roadmaps and impact network |
| Architecture governance and approvals | KDESF change/decision/policy graph, TOGAF-aligned records | ADM phase/readiness dashboard |
| Operational behavior and handoffs | BPMN source model, ProcessFamily/Variant governance metadata | ArchiMate process/service relationships |
| Business decision execution and rule precedence | DMN or approved decision rule source | BPMN business-rule tasks and rationale |
| Information identity, constraints, semantics | InformationOwner-approved UML/ontology/structured concept source | ArchiMate Business Objects, schemas, glossary, BIAN mappings |
| Logical service offering and operation meaning | LogicalInterface + SemanticContract | ArchiMate business/application service, SOA contracts |
| Technical APIs/events | OpenAPI/AsyncAPI etc at versioned source | API portal, downstream generated SDK/docs |
| Actual system construction | Git + immutable build/configuration manifests | Implementation graph, architecture realization |
| Operational truth | Observability/CMDB/configuration records with provenance | Drift and conformance lenses |
| Claims of conformance | SACM-inspired assurance graph linked to immutable evidence | Gate/readiness dashboard |

Conflict handling is explicit: neither a BPMN event named “Customer” nor an OpenAPI schema called “Customer” can unilaterally redefine the canonical “Customer” business concept.

## 3. Cross-standard mapping rules

### 3.1 ArchiMate and business process

- KDESF **Capability** may map to an ArchiMate Capability element. Capability is a durable ability, not a behavior sequence.
- KDESF **BusinessProcess** may map to ArchiMate Business Process and, for detailed behavior, to a BPMN Process/Collaboration. These are **typed cross-reference links**, not automatic equivalence.
- KDESF **BusinessService** maps to an externally meaningful offering, which can be realized by business behavior in an architecture model according to allowed ArchiMate relationships.
- BPMN subprocess boundaries can be finer or coarser than ArchiMate process elements. Link at the boundary that best captures the same scope; mapping may be many-to-many, may be “decomposes”, and must declare fidelity.

**Direction caution:** ArchiMate provides specific *native* relationship semantics and allowed compositions; KDESF's generic “realizes/uses/implements” predicates are not free-form substitutes. An adapter must validate whether a proposed native ArchiMate relationship is legal before exporting it.

### 3.2 BIAN and local banking architecture

Use BIAN 14.0 reference entries via versioned **ReferenceMapping**:
- BIAN Service Domain ↔ local domain/capability/service/bounded context: label exact, related, part-of, broader, narrower, unmapped.
- BIAN Business Object ↔ local BusinessConcept / InformationObject.
- BIAN Service Operation ↔ LogicalOperation / TechnicalContractOperation, with transformation notes.
- BIAN Business Scenario/wireframe ↔ local ProcessFamily/Variant as *inspiration or mapping*, not normative execution requirement.

**Invariant:** A BIAN Service Domain is not intrinsically a single bounded context, business capability, team, database or microservice. Never generate deployment boundaries solely from BIAN taxonomy.

### 3.3 DDD + UML + logical service interfaces

A BoundedContext declares language, owners, invariants, aggregate boundaries and relationship to other contexts (conformist, partnership, anti-corruption layer, published language, etc.).

Information modeling shall support:
- concept identity and definition;
- attributes (types, optionality, allowed values, precision, classification);
- associations/cardinality and state transitions;
- invariants and business rules;
- canonical identifier semantics (including global vs local identifier);
- data authority at *fact/attribute + scope + effective time* level;
- data quality constraints and stewardship;
- input/output semantics for business services;
- mappings to wire schemas, not unreviewed class-to-table assumptions.

**LogicalInterface** must specify provider/consumer, purpose, operation, request/response/event meaning, pre/postconditions, side effects, error taxonomy, quality expectations, authorization, idempotency and version/compatibility rules. One logical operation can have REST, asynchronous and batch realizations. 

### 3.4 DMN and BPMN

Business-process activities may invoke decisions, but DMN **decision ownership/logic/version** remains independent. A decision may be reused by multiple processes and services. Verify:
- input information binding;
- output vocabulary;
- decision-table coverage and hit policy;
- rule validity windows;
- control/exception mappings;
- test vectors and recorded evidence.

Do not encode rules only inside gateway labels or source code where a business owner cannot maintain them.

### 3.5 API4KP, Essence/SPEM and SACM

- **API4KP:** align asset identities, knowledge artifact descriptors, publication, discovery and retrieval operations; reuse the formal API/ontology assets selectively.
- **Essence/SPEM:** model AgentRole/HumanRole, WorkDefinition, Activity, WorkProduct, Practice and progress checkpoints. A canonical method instance maps to these rather than copying every workflow as a new ontology class.
- **SACM:** model Claim, Argument, Evidence and context, especially control compliance, compatibility, performance, security and release readiness. Evidence must identify which revision and environment it proves.
- **OSLC:** expose linkable resources for external issue/test/architecture/release tools where feasible; typed URI links carry stronger guarantees than pasted URLs.

## 4. Canonical architecture description and viewpoints

Following ISO 42010, every Architecture Description (AD) references the **entity of interest**, stakeholder concerns, viewpoint definitions, instantiated views, model kinds, correspondences and known inconsistencies. A view is an *audience- and concern-specific projection*, not a separate copy of truth.

| Viewpoint / model kind | Stakeholders | Primary concern | Source families |
|---|---|---|---|
| Enterprise motivation | Executives, capability owners | Why change? goals, outcomes, constraints, principles | ArchiMate Motivation, Goal/Policy |
| Capability landscape | EA, business owners | What abilities exist, who owns, what maturity? | CapabilityProfile / ArchiMate strategy |
| Value stream / E2E journey | Business, product | How is value delivered across capability boundaries? | ValueStream, BPMN, ArchiMate |
| Process variant map | Operations, risk | Which division/customer/jurisdiction follows which behavior? | BPMN, ProcessFamily/Variant |
| Decision landscape | Process, rules, compliance | Which decisions govern which steps and who owns them? | DMN, Policy, Decision |
| Information semantics | Data Owner, API Architect | What exactly do key business entities mean? | UML, ontology, constraints |
| Data lineage/authority | Data governance, regulators | Which system is authoritative for which information? | UML, SORAssignment, Lineage |
| Service and interface catalog | API architect, consumers | What can be called/published, under which contracts? | LogicalInterface, OpenAPI, AsyncAPI |
| Bounded-context map | Domain/Solution Architects | Where are semantic boundaries and translations? | DDD ContextMap, UML |
| Application/integration | Solution architects | What realizes business services and how? | ArchiMate App, technical contracts |
| Security/privacy/control | Security, DPO, risk | What is sensitive, who accesses, what protects it? | Policy/ABAC, controls, data classifications |
| Change impact | Sponsors, owners, architects | Which semantics, systems, teams and controls change? | ChangeSet + graph closure |
| Transition roadmap | Portfolio, EA | How move from baseline to target, by increments? | TOGAF/ArchiMate Implementation-Migration |
| Delivery and traceability | Engineering, delivery | Why does this code exist and what verifies it? | Requirement, Git, CI, tests |
| Assurance case | Assurers, audit | Why should we trust the change? | SACM-shaped Claims/Arguments/Evidence |
| Runtime conformance | SRE, business owner | Is what runs consistent with approved business promises? | Telemetry, release, drift |
| AI context and authority | Agent supervisor, platform security | What exact knowledge and actions can this agent use? | ContextManifest, scopes, AgentRun |

### 4.1 Minimum viewpoints by lifecycle

At **idea:** motivation, capability impact, stakeholders, assumptions and alternatives.  
At **commitment:** high-level target/impact, information/security implications, named owners, acceptance outline and deferred decisions.  
At **design approval:** process/information/decision/interface and solution views proportionate to change.  
At **release:** target-to-realization traceability, control/evidence, exception disposition, deployment manifest.  
At **operate:** actual-state/reliability view and drift log.

Each viewpoint has a “fit for purpose” checklist and minimum trace links. A model's detail level must be driven by risk and decision needs, not a desire for uniform diagram depth.

## 5. Process modeling examples and variation policy

A single bank-wide “Customer Onboarding” **ProcessFamily** may have:
- Retail Sweden branch variant;
- Retail Sweden digital variant;
- Corporate Sweden variant;
- Cross-border corporate variant.

Variants can share a reusable “Verify Identity” **called subprocess** while differing in an eligibility DMN decision, required KYC evidence, escalation control or API integration.

**Decision procedure for variants:**
1. Is the outcome/boundary the same? If not, separate ProcessFamily or process.
2. Are differences controlled by routing rules, minor tasks or participant assignments? Model variants with applicability/parameterization, avoid forks.
3. Do differences affect controls, evidence, accountability, timing, or regulatory obligation materially? Explicit named variants and own approvals.
4. Is a reusable subprocess truly shared and versioned? Use a called process rather than copy/pasting.
5. Are runtime process execution paths already divergent? Treat as observed variations until approved.

Modeling “L0–L4” is an **organizational convention** only, not a BPMN-defined multi-level standard. “BPMN descriptive/analytical/executable” is another informal modeling maturity convention, not equivalent to L0–L4.

## 6. Integration fidelity and provenance

For every adapter store:
- standard name/version, exporter/importer vendor+version;
- source artifact URI, hash, source fragment native ID;
- mapped canonical type and identity;
- transformation rule version and mapping author;
- preserved properties and omitted/unsupported constructs;
- lossiness status, confidence, known ambiguities;
- import timestamp, baseline and effective date;
- round-trip diff outcome (if supported).

**Example:** BPMN timer boundary events must not disappear when summarized as an ArchiMate process. Their existence and impact survive via the original BPMN source and a projection fidelity report.

## 7. Reference resources (version-pinned)

- [ISO/IEC/IEEE 42010:2022](https://www.iso.org/standard/74393.html)
- [TOGAF Standard, 10th Edition — introduction](https://help.opengroup.org/hc/en-us/articles/32726995273362-Understanding-the-TOGAF-Standard)
- [The Open Group: TOGAF and ArchiMate complementarity](https://help.opengroup.org/hc/en-us/articles/32115987894930-How-the-ArchiMate-Language-and-the-TOGAF-Standard-Complement-Each-Other)
- [ArchiMate 3.2 (Open Group information)](https://www.opengroup.co.za/archimate-standard-32)
- [OMG BPMN 2.0.2](https://www.omg.org/spec/BPMN/2.0.2)
- [OMG DMN 1.6](https://www.omg.org/spec/DMN/1.6) (1.7 beta is not assumed production baseline)
- [OMG UML 2.5.1](https://www.omg.org/spec/UML/2.5.1)
- [BIAN Service Landscape 14.0](https://bian.org/deliverables/service-landscape/)
- [OMG API4KP 1.0](https://www.omg.org/spec/API4KP/1.0)
- [OMG SACM 2.3](https://www.omg.org/spec/SACM/2.3)
- [OMG Essence 1.2](https://www.omg.org/spec/Essence/1.2)
- [OMG SPEM 2.0](https://www.omg.org/spec/SPEM/2.0)
- [OMG BACM 1.1](https://www.omg.org/spec/BACM/1.1)
- [W3C SHACL Recommendation](https://www.w3.org/TR/shacl/) (SHACL 1.2 was a working draft as of Nov 2026)
- [W3C PROV-O](https://www.w3.org/TR/prov-o/)
- [AWS Labs AI-DLC Workflows](https://github.com/awslabs/aidlc-workflows)

This document contains original adaptation guidance and pointers; it does not reproduce proprietary specification text.
