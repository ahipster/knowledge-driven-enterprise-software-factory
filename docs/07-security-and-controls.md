# 07 — Security, classification, authorization and enterprise controls

**Status:** Design proposal, not a bank's existing classification scheme or legal compliance determination. Local information-security policy and legal/regulatory advice override these illustrative defaults.

## 1. Protect *knowledge and relationships*, not only source files

Enterprise models can reveal critical information even without real customer records: vulnerabilities, business controls, internal topology, data ownership, payment paths, access mechanisms and incident response. Labels must apply to **elements, attributes, relationships, derived content, search embeddings, observations, agent prompts and review exports**. A file or diagram's classification is an upper-bound classification of its content; its child elements may need stricter handling.

Core principles: least privilege, deny by default, purpose limitation, data minimization, separation of duties, explicit delegation, time validity, zero-trust workload identity and end-to-end evidence/audit.

## 2. Illustrative four-level classification profile

The following implements a **proposed model**, NOT an assertion that any given bank uses these labels or access rules.

| Tier | Illustrative meaning | Default audience and access |
|---|---|---|
| **L1 — Broad / non-sensitive** | Public/reference architecture, generic standards mappings, approved general capability map | Broad discoverability subject to publication rules |
| **L2 — Internal** | Standard internal processes, team roles, routine high-level architecture without critical sensitive detail | Authenticated workforce with authorized internal purpose |
| **L3 — Restricted by purpose and capability** | Detailed process rules, internal information models, sensitive dependencies or control descriptions | Members of owning capability/domain **or** approved participants in common delivery affecting it, subject to purpose, role, scope and attribute restrictions |
| **L4 — Strict need-to-know** | Security-critical designs, operational secrets/topology, privileged threat/incident data, highly sensitive customer/process/control details | Explicit roles directly working with affected assets in that capability **plus** narrowly delegated independent reviewers; no blanket inherited access |

For L3, “common delivery” must be a real, time-bounded, authorized ChangeCase with capability-owner-approved participation. **For L4, even participation in an enterprise-wide initiative does not imply access.** A narrowly assigned capability-specific role, purpose and explicit authorization are needed. Sensitive fields/relationships can require **additional attribute policy** even at the same nominal level.

Tier rules are only one dimension; jurisdiction, user status, data subject rights, legal basis, risk domain, operational environment, retention, and conflicting roles can restrict further.

## 3. Object-level and field-level ABAC

A policy decision request includes:

~~~json
{
  "subject": {
    "principalId": "urn:kdesf:actor:employee-123",
    "identityType": "human",
    "roles": ["solution-architect"],
    "capabilities": ["urn:kdesf:capability:onboarding"],
    "delegationIds": [],
    "tenantId": "example-bank",
    "clearanceLevel": 3
  },
  "action": "read:attribute",
  "resource": {
    "elementId": "urn:kdesf:information-object:customer",
    "revisionId": "urn:kdesf:revision:customer:v8",
    "attributePath": "/attributes/identityDocumentToken",
    "classificationLevel": 4,
    "ownerCapabilityId": "urn:kdesf:capability:onboarding",
    "policyTags": ["personal-data", "restricted-identifier"]
  },
  "context": {
    "purpose": "approved-change-CC-17",
    "changeCaseId": "urn:kdesf:change:CC-17",
    "environment": "design",
    "time": "2026-11-10T10:00:00Z"
  }
}
~~~

**Decision outcome:** Permit / Deny / Permit with exact field mask and graph traversal limits / Step-up authorization required. Every decision records policy version and auditable reason codes. Authentication (OIDC) establishes identity; authorization uses server-side scopes/relationships/attributes; mTLS provides workload channel identity, not semantic content permission.

### Proposed authorization predicate

~~~text
allow = authenticated
    AND active_principal
    AND valid_scope_assignment
    AND clearance_adequate
    AND purpose_authorized
    AND classification_policy_permits
    AND jurisdiction_policy_permits
    AND element_role_policy_permits
    AND attribute_policy_permits
    AND no_explicit_deny
    AND (cross_capability_access_is_approved OR within_home_scope)
    AND (level4? explicit_need_to_know_grant : true)
~~~

This is explanatory pseudocode, not an implementable policy language. Deny precedence and interpretation of delegation are policy-engine responsibilities.

## 4. Graph and model security

**Existence leakage** is a first-class threat: even a link or result count may reveal restricted asset presence.

- Validate access at **every hop**, not only starting nodes.
- Filter edge classifications and any informative predicate/label.
- Do not expose forbidden IDs, names, counts or redaction placeholders where mere existence is classified.
- Bounded impact analysis may return “partial results due to access restrictions” without revealing how many hidden resources exist.
- Owner resolution and gate enforcement use **privileged policy service** internally but only return appropriately scoped reviewer requirements to the caller.
- Never put high-classification data into lower-classification search indexes or shared embeddings; establish derivative classification and retention.
- Graph query language and SQL injection, prompt injection and graph-exfiltration path testing are mandatory.
- Differential analysis must prevent exposing sensitive changes through before/after count differences and cache timing where material.

## 5. Agents, retrieval and prompt-injection controls

All agent activity must be attributable to:
- **workload identity** (service principal) and, where applicable, **human delegator**;
- declared purpose, scope and expiration;
- approved tool allowlist with per-operation authorization;
- pinned knowledge baselines and filtered ContextManifest;
- declared LLM/provider, hosting location and permitted data handling;
- run-level budget and kill/stop mechanism;
- complete tool invocation log with protected storage;
- output validation and data-loss-prevention checks before publication.

**Untrusted source files, comments, email, external web content and repository instructions are data, not authority.** Never allow retrieved text to override tool permissions, force new reviewers, approve a ChangeSet, deploy to production, or exfiltrate secrets. Retrieval-augmented generation must not bypass class restrictions through summarization. An agent may request escalation, but the policy service makes the decision.

For highly restricted assets, plan either enterprise-approved private model deployments or deny model processing outright. Human authorization to view a document does **not** necessarily authorize sending it to an external LLM.

## 6. Delegation, break-glass and SoD

- Human approval grants are purpose-, role-, scope-, duration- and risk-bounded.
- Enforce SoD on **actual identities**, not just role labels; a proposer may not independently approve their own high-impact change if policy requires independence.
- Emergency access requires incident reference, step-up authentication, short expiry, full audit and later independent review.
- Delegations and approvals become invalid on revocation, employment status change or scope closure.
- Two-person rule for high-risk ontology/policy changes and sensitive privilege modifications as policy mandates.
- Enterprise policy modification follows a separate, more restrictive governance path than ordinary knowledge edit.

## 7. Data and privacy architecture

- Do not require production personal data to describe models; synthetic examples and classification metadata usually suffice.
- Personal-data processing has purpose/retention/legal basis and access rules at **specific attribute, lineage and usage** scopes.
- Model residency, replication, third-party processor, export and deletion policy by organizational legal requirements.
- Maintain owner/steward, processing purpose and relevant risk/control mapping.
- Handle “right to erasure” and records retention without corrupting required immutable business audit: use data minimization, encrypted payload separation and policy-compliant cryptographic erasure/tombstones where appropriate.
- Audit/event logs must not unnecessarily duplicate PII, tokens or secrets.

## 8. Security control domains and assurance

| Control domain | Required platform behavior | Sample evidence |
|---|---|---|
| Identity | Workforce SSO, workload authentication, service-to-service security | Authentication configuration, access tests |
| Authorization | ABAC, attribute masking, dynamic capability/purpose checks | Policy version, deny/permit test suite |
| SoD | Independent reviewer enforcement and delegation revocation | Role conflict test and approval logs |
| Data protection | Encryption, keys, retention, classification propagation | Key configuration, DLP validation |
| Supply-chain integrity | Signed versioned artifacts/manifest, SBOM where required | CI attestations, digest records |
| Knowledge integrity | Immutable revisions, provenance, approval diff hash | Reproducibility/audit tests |
| API security | OAuth2/OIDC as applicable, mTLS where required, consumer authorization | Contract/security test results |
| Availability | Fail-closed writes, recovery, backups, replay | Restore simulation, failure injection |
| Agent safety | Context filtering, tool sandbox, prompt injection resistance | Red-team tests, run logs |
| Auditability | Who/what/when/why, effective policy and evidence | Audit reconstruction tests |
| Regulatory mapping | Link policies/controls to applicable obligations | Control evidence and scope mapping |
| Operations | Drift detection, incident workflows, exception expiry | Drift/incident cases and closure proof |

**Important:** DORA, GDPR, BCBS 239, EBA guidelines and internal policy may be relevant in a bank; they should be entered as *versioned, jurisdiction-applicable control requirements*. The design does not claim compliance merely by linking them.

## 9. Security requirements (testable)

- SEC-01: All APIs deny unknown or unauthenticated callers except explicitly public endpoints.
- SEC-02: Every element and nested attribute retrieval evaluates active ABAC policy; no bypass through exported views.
- SEC-03: Multi-hop graph and vector search are subject to the same policy as element retrieval.
- SEC-04: Cross-capability L3 access requires a verifiable role/purpose or approved common delivery; revoked participation removes access.
- SEC-05: L4 access requires explicit need-to-know assignment; organization-wide seniority does not override it.
- SEC-06: Agent tool calls cannot perform unauthorized semantic promotions or approval impersonation.
- SEC-07: The reviewer cannot approve a changed diff under a previously approved hash.
- SEC-08: Role revocation is enforced on the next authorization check, subject to documented bounded cache TTL.
- SEC-09: Audit events capture policy version, actor, action, scope and decision without leaking content into less secure stores.
- SEC-10: Sensitive embeddings, caches and logs have derived classification and cannot be queried at lower privileges.
- SEC-11: Encryption-at-rest/in-transit and secret rotation use approved enterprise key controls.
- SEC-12: Emergency override is individually audited, expires and forces post-event review.
- SEC-13: Disaster recovery restores authoritative ledger, policy versions and baseline identities consistently.
- SEC-14: Cross-tenant data access is blocked even when canonical IDs collide or prefixes overlap.
- SEC-15: Security tests deliberately probe counts, link traversal, timing and AI summaries for hidden-node leakage.

## 10. Negative test scenarios

1. L2 architect requests L4 topology diagram by querying its L1 parent: must not learn existence or content.
2. L3 engineer in Onboarding tries to traverse into Payment Fraud L3 without shared purpose/assignment: denied.
3. Agent accesses broad repository containing embedded malicious instructions to update policy: tool gateway refuses.
4. Approver loses delegation after signing a future-effective revision: policy reevaluation follows declared revocation rules; no silent promotion.
5. Summary endpoint attempts to merge L4 and L2 model sources: result remains L4 or redacted by approved transformation.
6. Two entities share the same label; auto-mapping would bridge isolated classifications: proposed mapping held for review.
7. Critical vulnerability observation from telemetry attempts to reclassify active security policy: results in drift and change proposal, not auto-approval.

## 11. Governance before production

Before enterprise rollout, record reviewed ADRs for classification taxonomy, allowed access purposes, model-provider/data residency, RACI and SoD, emergency access, data retention, cross-capability trust/federation and evidence trust. Security design must be threat-modeled and tested against the bank's *actual* access policy, not just this proposal.
