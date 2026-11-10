# 10 — Worked example: fictional customer onboarding change

**All content is synthetic.** No real bank process, customer data, production interface, security configuration or regulatory obligation is represented.

## 1. Business situation

A fictional European retail bank wants to allow existing verified customers to begin opening an additional account through a streamlined onboarding journey, without repeating an identity verification step when an *illustrative* verification-validity decision permits reuse.

The change affects:
1. **Customer Onboarding** capability (journey, eligibility and process variant).
2. **Party Information Management** capability (meaning of verification status, timestamp, provenance).
3. **Identity Verification** capability (verification assertion and interface).
4. **Customer Status logical service** (consumer-facing status semantics).
5. Cross-cutting **Security/Privacy Control** and **Assurance**.

**Not a proposed real KYC policy:** A production bank must have its own legally reviewed rules. No numeric validity period or actual AML/KYC bypass recommendation is implied.

## 2. Capability profiles

| Capability | Purpose | Accountable role | Key owned artifacts |
|---|---|---|---|
| CAP-ONB | Establish permitted customer relationships and products | Onboarding Capability Owner | ProcessFamily, variants, service promise, outcomes |
| CAP-PARTY | Govern Party/Customer information meaning and authority | Party Information Owner (semantic), Party Capability Owner (scope) | VerificationStatus concept, source authority, state machine |
| CAP-IDV | Provide verified identity evidence and status | Identity Verification Service Owner | Verification event and logical interface |
| CAP-API | Cross-capability customer status presentation | Customer Status API Owner | LogicalOperation, technical OpenAPI contract |
| CTRL-PRIV | Protect permitted use of identity-derived information | Privacy/Security Control Owners | Control obligation and assurance criteria |

Different capabilities can consume the same concept without becoming joint owners of its meaning.

## 3. Initial knowledge graph (approved baseline B0)

~~~mermaid
flowchart TD
  GOAL[GOAL-1 Reduce repeat friction without weakening controls] --> CAP[CAP-ONB Customer Onboarding]
  CAP --> PROC[PROC-FAMILY-1 Onboarding]
  PROC --> VAR[VAR-RETAIL-1 Existing Customer Digital]
  VAR --> DEC[DEC-1 Reverification Needed?]
  DEC --> CON[CON-1 Identity Verification Status]
  CON --> SOR[SOR-1 Authoritative status source]
  VAR --> LOG[IF-1 Customer Status Logical Interface]
  LOG --> OAS[API-1 OpenAPI status endpoint]
  VAR --> CTRL[CTRL-1 Eligibility and data access control]
  API[APP-1 Onboarding Application] --> OAS
  API --> DEC
~~~

**Representative modeled semantics:**
- ProcessFamily: outcome “allowed additional account relationship established”; not a claim of full BPMN content.
- Variant: existing digital retail customer; explicit jurisdiction/segment/channel applicability.
- Decision: “Must a new verification be requested?”; actual predicate not defined until approved.
- Information concept: status is a qualified business assertion, not merely a boolean.
- Logical operation: “Retrieve current verification eligibility summary” with privacy and freshness constraints.
- Technical endpoint: GET /v1/customers/{customerId}/verification-summary (synthetic, for model illustration only).

## 4. Signal and competing hypotheses

**Signal SIG-17:** Contact center reports re-verification friction; evidence is a synthetic analytics report.

**Intent INT-4:** Reduce unnecessary repeated work while preserving approved control quality.

**Hypotheses**
- **HYP-A:** Reuse verification evidence when an approved business decision says evidence remains applicable.
- **HYP-B:** Always re-verify but streamline UX.
- **HYP-C:** Defer automation and optimize manual review.

**Selection record:** HYP-A selected for a bounded prototype; decision notes incomplete risk analysis and reversibility. HYP-B and C stay searchable with reasons for non-selection.

### Commitment gate G1

Minimum approved:
- selected objective, synthetic baseline metric, business sponsorship;
- affected capabilities and named abstract role assignments;
- privacy/security and information owner identified;
- expected outcome and high-level journey;
- key unknowns: decision logic/thresholds, provenance quality, data classification, consumer compatibility;
- these unknowns due before G2/G3, not falsely treated as approved;
- commitment decision, date and intended pilot scope.

This shows the requirement distinction: **commit to investigating a safe change without pretending every executable business rule already exists**.

## 5. Semantic ChangeSets

**CS-17A — Business process**
- modify ProcessVariant “Existing Customer Digital” to include decision interaction;
- link to DEC-1 with expected process revision;
- process owner approves;
- no information semantic changes.

**CS-17B — Information meaning**
- clarify/modify “Identity Verification Status” from ambiguous yes/no to qualified status + evidence timestamp + provenance + permitted-use scope;
- change data authority mapping at attribute level if needed;
- impact touches CAP-ONB, CAP-IDV and status logical interface;
- Party Information Owner and affected interface owners review; privacy owner reviews relevant processing impacts.

**CS-17C — Decision and logical interface**
- define DEC-1 decision table with approved inputs/outputs and control assumptions;
- specify status operation meaning, error/unknown behavior, authorization and freshness;
- Decision Owner, Logical Service/API Owner and control reviewer approve per impact.

**CS-17D — Technical realization**
- update OpenAPI operation/schema, implementation, tests, instrumentation;
- technical API owner and engineering approvals; semantic owners only re-review when behavior/meaning materially differs from previously approved target.

### Example semantic diff (not actual policy)

~~~diff
- IdentityVerificationStatus: Boolean
+ IdentityVerificationStatus: QualifiedVerificationAssertion
+ fields:
+   outcome: Verified | NotVerified | Unknown
+   evaluatedAt: Timestamp
+   evidenceReference: OpaqueReference
+   permittedUseScope: VerificationUseScope
+ invariant:
+   Unknown MUST NOT be interpreted as Verified
~~~

This is an *illustrative information design proposal*, not a UML or regulatory standard requirement. The native information representation could be UML class + state machine; the canonical graph stores the reviewed meaning and crosswalk.

## 6. Required authority by artifact

| Changed knowledge | Required actor/role | Why |
|---|---|---|
| Digital onboarding process variant | Onboarding Process Owner | Owns sequence/control placement |
| Identity status concept, attributes and invariants | Party Information Owner | Owns meaning and lifecycle |
| Verification reuse decision | Decision Owner; policy control reviewer as required | Owns business decision and applicability |
| Identity service interaction | Identity Verification Service Owner | Owns source service promise |
| Customer status logical operation | Customer Status API Owner plus semantic consumer/owner review | Owns interface behavior and compatibility |
| Customer identity-derived data use | Privacy/Security Control Owner | Owns relevant processing/access control |
| Code and versioned technical contract | Engineering/API Owner | Owns realization |
| Assurance claim and release | Independent Assurer/Release Authority | Confirms proof/appropriate risk acceptance |

A single ChangeCase can include all changes. The platform resolves approvals **per affected element**. A Product Owner's commitment decision does **not** grant permission to reinterpret IdentityVerificationStatus.

## 7. Cross-standard representations

| Knowledge assertion | Native representation | Cross-link |
|---|---|---|
| Why reduce onboarding friction | ArchiMate motivation / outcome | GOAL-1 supports CAP-ONB |
| Capability dependencies | ArchiMate capability/business/application viewpoints | CAP-ONB ↔ CAP-PARTY ↔ CAP-IDV |
| Behavior variants and verification interaction | BPMN 2.0 process with variants and called subprocess | PROC-FAMILY-1 / VAR-RETAIL-1 |
| Verification required? | DMN 1.6 decision model | DEC-1 governedBy relevant control |
| Qualified verification assertion | UML information model (class/enum/constraint) | CON-1, attribute authority |
| Consumer business operation | LogicalInterface/Operation catalog | IF-1 uses CON-1 |
| Wire-level operation | OpenAPI 3.x with JSON schema | API-1 fulfilledBy IF-1 |
| Banking reference | Reviewed mapping to BIAN 14.0 entries *after actual catalog lookup* | No fabricated BIAN service-domain ID |
| Work stages and work products | Essence/SPEM-inspired plan | ChangeCase links stages and outputs |
| Claim and proof | SACM-inspired assurance case | Test evidence proves exact DEC/API revisions |

## 8. Verification cases

**AC-1 — Unknown meaning:** When verification result is Unknown, no consumer can interpret it as Verified.

**AC-2 — Decision traceability:** Each decision outcome references the exact approved decision table revision and its applicable input concepts.

**AC-3 — Data authority:** Verified outcome and source timestamp derive from the authoritative source declared for the attribute and scope.

**AC-4 — Control enforcement:** Identity-derived information is disclosed only to authorized callers under purpose and field policy.

**AC-5 — Interface contract:** The new API operation states semantic errors and unknown behavior; incompatible consumers receive impact notice/appropriate versioning.

**AC-6 — Evidence:** Release build, tests, decision version, process variant and information model revision are linked through a version-pinned assurance case.

**AC-7 — Governance:** If an AI agent proposes that Unknown=Verified, semantic policy validation or mandatory owner review blocks it.

**AC-8 — Variants:** A different corporate onboarding variant is unaffected unless explicitly linked to the approved change.

## 9. Assurance case sketch

~~~text
Claim C1: Synthetic release R17 implements approved target baseline BT17.
  Argument A1: All applicable critical semantic constraints are realized.
    Evidence E1: Exact version DMN table coverage test T-DEC-17.
    Evidence E2: API contract and authorization tests T-API-17.
    Evidence E3: UML invariant test for Unknown not equivalent to Verified.
  Argument A2: Mandatory owners approved the exact ChangeSets.
    Evidence E4: Signed review decisions and frozen canonical diff hashes.
  Argument A3: Deployment carries approved artifacts and controls.
    Evidence E5: Build digest + DeploymentManifest + security controls.
  Context: Retail digital existing customers, approved pilot environment.
  Exception: None, unless individually recorded with expiry and authority.
~~~

A green UI indicator is a projection of C1 + its evidence; it does not substitute for this record.

## 10. Demonstrating operational drift

After release, an instrumented integration test in a sandbox sees an unknown verification status returned with a misleading “eligible” flag.

**Observed:** status=Unknown, consumer display=Eligible (hypothetical).  
**Expected:** Unknown must not imply Verified.  
**DriftCase DR-1:** expected Concept revision CON-1 v2 and LogicalOperation IF-1 v3; observed trace E-OBS-1, deployed build R17.

**Disposition:** fix implementation and consumer mapping; add regression test; preserve normative concept unchanged. A different disposition—propose a revised business rule—would require a fresh scoped ChangeSet and reviews. This demonstrates the distinction between **correcting code** and **changing knowledge truth**.

## 11. Data and boundary examples

- **No real customer identifiers** appear in this repository; use synthetic IDs.
- “Verification status” is modeled as a business concept; a real design must separately assess personal-data categories, retention, legal basis, identity verification and AML/KYC.
- The technology-specific OpenAPI schema must preserve this meaning without falsely declaring that JSON types alone contain all business semantics.
- The model may be visualized through ArchiMate, BPMN, DMN and UML separately; one diagram does not replace their source metamodels.

## 12. Expected demonstration outcome

The user can ask “Why did this code/API change, who owned the meaning, who approved it, which business processes depend on it, what tests prove it, and does production still conform?” The platform returns a permission-filtered, source-cited, baseline-specific chain without mixing hypotheses, approved semantics and observations.

An illustrative machine-readable example lives at [spec/examples/customer-onboarding.json](../spec/examples/customer-onboarding.json).
