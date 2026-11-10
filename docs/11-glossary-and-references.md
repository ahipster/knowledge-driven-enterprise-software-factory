# 11 — Glossary, normative distinctions and external references

## 1. Glossary

| Term | Meaning in this specification |
|---|---|
| Architecture Description (AD) | A structured expression of architecture for an entity of interest, with stakeholders/concerns/viewpoints/model kinds; inspired by ISO 42010 |
| Artifact | Native or authored container (diagram, file, model, API description, test report, decision record), not necessarily one semantic element |
| Baseline | Named immutable set of element revisions and related policy/time context |
| Business Capability | A durable ability to achieve a business outcome; not a stepwise process |
| Business Process | A defined sequence/interrelation of business behavior and outcome with explicit boundary |
| Business Service | Externally significant behavior/value provided to consumers; may have multiple underlying processes |
| BIAN Service Domain | Banking reference taxonomy/service domain in the BIAN landscape; not automatically local microservice/team |
| Bounded Context | Boundary within which a domain model and ubiquitous language have consistent meaning |
| ChangeCase | Governed intent and delivery coordination aggregate, containing hypotheses, requirements, changes and releases |
| ChangeSet | Versioned proposal of typed changes to knowledge elements/relations with exact diff and expected revisions |
| Classification | Sensitivity and purpose/role policy governing information access; defined by actual enterprise policy |
| Concept | Normative semantic definition with identity, owner, constraints, lifecycle and context |
| ContextManifest | Agent context authorization/provenance and baseline bundle |
| Decision | Rule/policy-governed choice with inputs, outputs, conditions and owner |
| Drift | Explained/triaged discrepancy between approved expectations and implemented or observed facts |
| Effective time / valid time | When a fact/knowledge revision is intended to apply in the business world |
| Recorded time / transaction time | When a claim/revision/event entered the registry; independent of valid time |
| Epistemic status | Whether an assertion is approved, proposed, inferred, implemented or observed |
| Evidence | Time- and version-scoped material supporting a claim (not merely a prose assertion) |
| Impact | Affected elements/roles/controls/realizations reachable by policy-defined dependency semantics |
| Information Object | A structured unit of business information, distinct from one transport schema |
| Logical Interface | Technology-neutral offered business/application operation semantics with owner and contract |
| Model kind | Kind of model used in a viewpoint, e.g. UML class model or BPMN process |
| Native element | An element represented according to a standard or source tool's own metamodel |
| Ontology | Controlled vocabulary and relations expressing classes, predicates and constraints (not merely a graph UI) |
| ProcessFamily | KDESF grouping for end-to-end process purpose and alternative process variants |
| ProcessVariant | Applicability-scoped variation of a process family, often jurisdiction/segment/channel based |
| Projection | Rebuildable derived graph/index/view over authoritative records; not automatically the source of truth |
| Requirement | Owned, versioned normative statement of desired outcome, behavior, constraint or quality with verification strategy |
| Revision | Immutable content and metadata state for a stable element identity |
| Scope | Explicit capability/domain/jurisdiction/process/solution/attribute/environment boundary |
| Semantic delta | Meaningful change to business/architecture/information/decision/interface knowledge, separate from file text diff |
| Source crosswalk | Mapping of native fragment IDs and source versions to canonical identities |
| System of Record (SOR) | Declared authority for *specific facts/attributes within a scope and effective period* |
| View | A specific audience-specific representation of an architecture model under a viewpoint |
| Waiver / Exception | Authorized, scoped, time-limited deviation from a policy/requirement with compensating obligations |

## 2. Reference list

Version declarations are scoped to **10 November 2026** and do not assert that every vendor/tool supports them.

### Architecture and enterprise modeling
- [ISO/IEC/IEEE 42010:2022 — Architecture description](https://www.iso.org/standard/74393.html).
- [The Open Group — TOGAF Standard, 10th Edition overview](https://help.opengroup.org/hc/en-us/articles/32726995273362-Understanding-the-TOGAF-Standard).
- [The Open Group — TOGAF and ArchiMate complementarity](https://help.opengroup.org/hc/en-us/articles/32115987894930-How-the-ArchiMate-Language-and-the-TOGAF-Standard-Complement-Each-Other).
- [ArchiMate 3.2 Standard information](https://www.opengroup.co.za/archimate-standard-32).
- [OMG Business Architecture Core Metamodel (BACM)](https://www.omg.org/spec/BACM/).

### Business behavior and information models
- [OMG BPMN 2.0.2](https://www.omg.org/spec/BPMN/2.0.2).
- [OMG DMN 1.6](https://www.omg.org/spec/DMN/1.6) — formal 2026.
- [OMG UML 2.5.1](https://www.omg.org/spec/UML/2.5.1).
- [BIAN Service Landscape](https://bian.org/deliverables/service-landscape/) — 14.0 released 2026.
- [BIAN announcement of 14.0](https://bian.org/news-room/bian-unveils-new-service-landscape-14-0-to-accelerate-ai-ready-banking-architecture/).

### Knowledge, methods, assurance, links
- [OMG APIs for Knowledge Platforms (API4KP) 1.0](https://www.omg.org/spec/API4KP/1.0).
- [OMG Software & Systems Process Engineering Metamodel (SPEM) 2.0](https://www.omg.org/spec/SPEM/2.0).
- [OMG Essence 1.2](https://www.omg.org/spec/Essence/1.2).
- [OMG Structured Assurance Case Metamodel (SACM) 2.3](https://www.omg.org/spec/SACM/2.3) — formal; SACM 2.4 was beta in 2026.
- [W3C Shapes Constraint Language (SHACL)](https://www.w3.org/TR/shacl/) — W3C Recommendation; SHACL 1.2 was under development as of Nov 2026.
- [W3C Provenance Ontology (PROV-O)](https://www.w3.org/TR/prov-o/).
- [W3C SKOS Reference](https://www.w3.org/TR/skos-reference/).
- [OSLC Core](https://open-services.net/spec/core/latest/).
- [AWS Labs AI-DLC Workflows](https://github.com/awslabs/aidlc-workflows) — an implementation/method reference, not a normative standard.

### Technical interface and engineering references
- [OpenAPI Specification](https://spec.openapis.org/oas/latest.html).
- [AsyncAPI Specification](https://www.asyncapi.com/docs/reference/specification/v3.0.0).
- [JSON Schema Draft 2020-12](https://json-schema.org/draft/2020-12).
- [OpenTelemetry](https://opentelemetry.io/docs/).
- [SLSA](https://slsa.dev/).

## 3. Conformance and intellectual property

These documents describe **original KDESF modeling rules and integration proposals**. References to named specifications do not confer compliance/conformance or product certifications. An implementation must validate conformance to the particular standard version, exchange format, schema, relationship rules and licensing requirements. External reference models and proprietary standard texts should not be copied into this repository without license review.

## 4. Potential regulatory mappings in a European bank

These are **possible requirements sources**, not assumptions about the applicability or compliance status of a particular institution:
- EU DORA: ICT operational resilience and relevant controls;
- GDPR: personal-data processing and protection;
- EBA guidelines and local supervisory obligations;
- BCBS 239: risk-data aggregation/reporting principles when applicable;
- sector-specific AML/KYC and payment requirements;
- internal information classification, risk appetite and retention policy.

Each obligation should become a versioned **ControlObligation** with jurisdiction, applicability, source version, interpretation, accountable authority, affected elements and assurance evidence.

## 5. Document consistency policy

All repository documents use the following shared assumptions:
1. KDESF types/ProcessFamily/ChangeSet/CapabilityProfile are *design extensions*, not native OMG/TOGAF classes.
2. “Approved knowledge,” “approved target,” “implemented” and “observed” remain distinct, even when all describe the same element.
3. Reference-mapped BIAN domains and standard model fragments are **not** assumed equivalent to local semantic elements.
4. Ownership and authorization attach to scoped semantic elements and their changes.
5. Agent proposals have no implicit approval authority.
6. Git and modeling tools remain sources of native artifacts; canonical, governed baselines provide authoritative knowledge state.
7. The specification is a blueprint and does not claim working code or formal validation unless supplied elsewhere.
