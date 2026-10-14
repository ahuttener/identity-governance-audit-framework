# Compliance Mapping — SOC 2® (CC6.1, CC6.2) ↔ ISO/IEC 27001:2022

**Client:** Kestrel Ledger Ltd. *(fictitious)* · **Domain:** Identity and Access Management (IAM)

| Field | Detail |
|---|---|
| Document ID | GRC-MAP-IAM-2026 |
| Version | 1.1 — implementation status updated for audit results |
| Dates | 3 July 2026 (v1.0) · 15 September 2026 (v1.1) |
| Document owner | Kestrel management — GRC Manager *(fictitious)* |
| Portfolio author | A. Huttener — written as a client document for this simulated case study |
| Frameworks | AICPA 2017 Trust Services Criteria (with Revised Points of Focus — 2022) · ISO/IEC 27001:2022 (incl. Amd 1:2024) · HIPAA Security Rule (45 CFR Part 164, Subpart C) · NIST CSF 2.0 · CIS Controls v8.1 |
| Related documents | [Risk assessment](authentication-risk-assessment.md) · [Control testing program](../02-audit-testing-workpapers/control-testing-program.md) · [IAM Policy](../03-policy-governance/identity-access-management-policy.md) |

> [!NOTE]
> AICPA criteria text and ISO/IEC requirement text are copyrighted. This document **paraphrases** requirements and cites them by identifier and title only. Always refer to the authoritative publications when designing or testing controls.

---

## 1. Purpose and approach

Kestrel is assessed against several frameworks every year: a SOC 2® Type 2 examination for US customers, ISO/IEC 27001 certification for EU customers, HIPAA Security Rule obligations under Business Associate Agreements with US healthcare customers, and customer due-diligence questionnaires aligned to NIST CSF and CIS. Testing each framework separately would duplicate effort and produce inconsistent evidence.

This document defines a **common control framework (CCF)** for the identity domain: each Kestrel control is **designed once, mapped to every framework it satisfies, and tested once**. The same workpaper evidence then supports the SOC 2® report, the ISO/IEC 27001 internal audit (Clause 9.2), certification-body surveillance audits and customer requests.

**Mapping principles**

1. Map at the **requirement level**, not by keyword. A control is mapped only if it would produce evidence that addresses the intent of the requirement.
2. Record coverage honestly: **Full**, **Partial** or **Other domain** (addressed by a control outside IAM).
3. Every control has **one accountable owner** and one primary workpaper reference.
4. The mapping is reviewed at least annually, and whenever a framework is revised or a control changes.

## 2. How the two frameworks differ

| Dimension | SOC 2® Type 2 | ISO/IEC 27001:2022 |
|---|---|---|
| Nature | Attestation engagement under AICPA attestation standards (AT-C 105 and AT-C 205) and the AICPA SOC 2® Guide | Certification of an information security management system (ISMS) |
| Performed by | Licensed CPA firm (the service auditor) | Accredited certification body (ISO/IEC 17021-1 and the ISO/IEC 27006 series); internal audits by competent, objective auditors (Clause 9.2) |
| Criteria | Trust Services Criteria for the categories in scope, plus DC 200 description criteria | Clauses 4–10 (all mandatory) plus Annex A controls selected through risk treatment and justified in the Statement of Applicability (SoA) |
| Output | Restricted-use report: auditor's opinion, management's assertion, system description, tests of controls and results | Certificate (public) and audit report; major/minor nonconformities and opportunities for improvement |
| Time coverage | Operating effectiveness **over a period** (typically 6–12 months) | **Point-in-time** audits: Stage 1 / Stage 2, annual surveillance, recertification every three years |
| Testing depth | Each control tested with defined procedures and samples; every exception disclosed in Section 4 | Sample-based audit of the management system and controls; findings graded |
| Central question | Did controls operate effectively to provide reasonable assurance that service commitments and system requirements were achieved? | Does the ISMS conform to the standard, and is it effectively implemented and maintained? |

**Practical consequence:** an ISO/IEC 27001 certificate does not demonstrate period-of-time operating effectiveness, and a SOC 2® report does not demonstrate a functioning management system (risk assessment, management review, corrective action). The CCF lets one body of evidence answer both questions.

## 3. CC6.1 — Logical access security

**Criterion (paraphrased):** The entity implements logical access security software, infrastructure and architectures that protect its information assets from security events, in support of its objectives.

Points of focus are **not requirements** — the AICPA provides them to help design and evaluate controls, and not every point must be addressed. The table records how Kestrel's control set covers each one.

| Point of focus (AICPA title) | What the auditor looks for | Kestrel control(s) | Coverage |
|---|---|---|---|
| Identifies and Manages the Inventory of Information Assets | An asset inventory that supports access decisions | ASSET-01 (asset management domain) | Other domain |
| Assesses New Architectures | Security of new system architectures is evaluated before they go live | CM-05 — architecture review in change management | Other domain |
| Restricts Logical Access | Access to information assets is restricted through access control software and rule sets | IAM-02, IAM-05, IAM-08 | Full |
| Identifies and Authenticates Users | People, infrastructure and software are identified and authenticated before access, locally or remotely | IAM-02, IAM-03, IAM-04 | Full |
| Considers Network Segmentation | Segmentation isolates unrelated parts of the system | NET-02 (network domain) | Other domain |
| Manages Points of Access | External access points are identified, inventoried and managed | IAM-03 (Okta as the single authenticated workforce entry point); NET-01 | Partial — shared with network domain |
| Restricts Access to Information Assets | Role-based access, data classification and separation restrict access to what is needed | IAM-05, IAM-08 | Full |
| Manages Identification and Authentication | Identification and authentication requirements are established, documented and managed | IAM-01, IAM-02, IAM-04 | Full |
| Manages Credentials for Infrastructure and Software | Infrastructure and software are registered and authorised before credentials are issued; credentials are removed when no longer needed | IAM-10 | Full |
| Uses Encryption to Protect Data | Encryption protects data at rest and in transit where appropriate | CRY-01 (cryptography domain) | Other domain |
| Protects Encryption Keys | Keys are protected across their lifecycle | CRY-02 (cryptography domain) | Other domain |

**Coverage:** 11 points of focus — 6 addressed by IAM controls (5 Full, 1 Partial) and 5 by other control domains outside this repository.

## 4. CC6.2 — Registration, authorisation and removal of users

**Criterion (paraphrased):** Before issuing credentials or granting system access, the entity registers and authorises new internal and external users whose access it administers; credentials are removed when a user's access is no longer authorised.

| Key point of focus (AICPA title) | What the auditor looks for | Kestrel control(s) | Coverage / audit result |
|---|---|---|---|
| Controls Access Credentials to Protected Assets | Credentials are created only on authorisation from the asset owner or an authorised custodian | IAM-05, IAM-11 | Full — no exceptions |
| Removes Access to Protected Assets When Appropriate | Credentials are removed promptly when access is no longer required | IAM-06, IAM-07 | Full (design) — **operating exceptions in IAM-07** (finding IAM-2026-01) |
| Reviews Appropriateness of Access Credentials | Credentials are periodically reviewed for unnecessary or inappropriate access | IAM-09 | Full — **exception in the Q3 2025 review cycle** (finding IAM-2026-02, remediated) |

**Scope note — external users:** CC6.2 covers users *whose access is administered by the entity*. Kestrel AP customer users are administered by each customer, so their lifecycle is addressed through **complementary user entity controls (CUECs)** disclosed in the system description of the SOC 2® report (listed in §9.2 below), not through Kestrel's controls.

## 5. Related criteria

| Criterion | Topic (paraphrased) | Kestrel control(s) |
|---|---|---|
| CC2.2 | Internal communication of information needed for internal control | IAM-01 |
| CC5.3 | Control activities deployed through policies and procedures | IAM-01 |
| CC6.3 | Access authorised, modified and removed based on roles, least privilege and segregation of duties | IAM-05, IAM-06, IAM-07, IAM-08, IAM-09 |
| CC6.6 | Logical access measures against threats from outside system boundaries | IAM-03 |
| CC7.2 | Monitoring of system components for anomalies indicative of malicious acts or errors | IAM-12 |
| CC7.3 | Evaluation of security events to determine whether they are incidents | IAM-12 |
| CC9.2 | Assessment and management of risks from vendors and business partners | IAM-11; VEN-03 (subservice organisation monitoring) |

## 6. Unified control matrix

| Control | Control summary | SOC 2® TSC | ISO/IEC 27001:2022 Annex A | NIST CSF 2.0 | CIS v8.1 | Policy (IAM-POL-001) | Workpaper |
|---|---|---|---|---|---|---|---|
| **IAM-01** | IAM policy and standards approved by the ISSC, reviewed at least annually and communicated | CC2.2, CC5.3, CC6.1 | 5.1, 5.15 | GV.PO-01, GV.PO-02 | — | §15 | WP-IAM-01 |
| **IAM-02** | Unique, named identities; all workforce access to Tier-1 systems federated through Okta SSO; shared or generic accounts prohibited | CC6.1 | 5.16, 8.5 | PR.AA-01, PR.AA-03 | 5.6, 6.7 | §6.1, §7.1 | WP-IAM-02 |
| **IAM-03** | MFA enforced by Okta authentication policies for all users and applications; SMS, voice and email factors disabled | CC6.1, CC6.6 | 5.17, 8.5 | PR.AA-03 | 6.3, 6.4, 6.5 | §7.2 | WP-IAM-03 |
| **IAM-04** | Password and authenticator configuration per standard; authenticator resets require verified identity | CC6.1 | 5.17, 8.5 | PR.AA-01, PR.AA-03 | 5.2 | §7.3, §7.4 | WP-IAM-04 |
| **IAM-05** | Joiners: birthright access by role; additional Tier-1 access approved by manager and system owner before grant | CC6.2, CC6.3 | 5.15, 5.18, 8.3 | PR.AA-05 | 6.1, 6.8 | §6.2 | WP-IAM-05 |
| **IAM-06** | Movers: role change triggers review; access no longer required removed within 5 business days | CC6.2, CC6.3 | 5.18, 6.5 | PR.AA-05 | 6.1, 6.2 | §6.3 | WP-IAM-06 |
| **IAM-07** | Leavers: access revoked within SLA, including non-federated accounts | CC6.2, CC6.3 | 5.16, 5.18, 6.5 | PR.AA-01, PR.AA-05 | 5.3, 6.2 | §6.4 | [WP-IAM-07](../02-audit-testing-workpapers/sample-testing-workpaper.md) |
| **IAM-08** | Privileged access: approved eligibility, just-in-time elevation with independent approval, separate admin identities, controlled break-glass accounts | CC6.1, CC6.3 | 5.3, 8.2, 8.18 | PR.AA-05 | 5.4, 6.5 | §8 | WP-IAM-08 |
| **IAM-09** | Quarterly user access reviews of Tier-1 systems on system-generated listings; changes actioned within SLA | CC6.2, CC6.3 | 5.18 | PR.AA-05 | 5.1 | §10 | WP-IAM-09 |
| **IAM-10** | Non-human identities inventoried with owners; no interactive login; secrets vaulted and rotated | CC6.1, CC6.2 | 5.16, 5.17 | PR.AA-01 | 5.5 | §9 | WP-IAM-10 |
| **IAM-11** | Contractor and third-party access sponsored, approved and re-certified every 90 days | CC6.2, CC9.2 | 5.18, 5.19, 5.20 | PR.AA-05 | 6.1 | §6.5 | WP-IAM-11 |
| **IAM-12** | Authentication events centrally logged (≥ 400 days); anomalous-authentication alerts triaged within SLA | CC7.2, CC7.3 | 8.15, 8.16 | DE.CM-03, DE.AE-02 | 8.2, 8.11 | §12 | WP-IAM-12 |

Test procedures for every control are in the [control testing program](../02-audit-testing-workpapers/control-testing-program.md).

## 7. ISO/IEC 27001:2022 — Statement of Applicability (IAM extract)

| Annex A control | Title | Applicable | Implementation status (after 2026 surveillance audit) | Justification for inclusion | Kestrel controls |
|---|---|---|---|---|---|
| 5.3 | Segregation of duties | Yes | Implemented | Conflicting duties exist in access requests, approvals, provisioning and privileged elevation | IAM-05, IAM-08, IAM-09 |
| 5.15 | Access control | Yes | Implemented | Topic-specific access control policy required by risk assessment | IAM-01 |
| 5.16 | Identity management | Yes | Implemented | Full identity lifecycle, including non-human identities | IAM-02, IAM-07, IAM-10 |
| 5.17 | Authentication information | Yes | Implemented | Allocation and management of authenticators and secrets | IAM-04, IAM-10 |
| 5.18 | Access rights | Yes | Implemented — **minor NC-01 open** | Provisioning, review, modification and removal of access | IAM-05, IAM-06, IAM-07, IAM-09 |
| 5.19 | Information security in supplier relationships | Yes | Implemented | Contractors and third parties access Tier-1 systems | IAM-11 |
| 5.20 | Addressing information security within supplier agreements | Yes | Implemented | Access and confidentiality obligations in contractor agreements | IAM-11 |
| 6.5 | Responsibilities after termination or change of employment | Yes | Implemented — **minor NC-01 open** | Revocation of access and post-exit obligations | IAM-06, IAM-07 |
| 8.2 | Privileged access rights | Yes | Implemented | JIT elevation and separate administrative identities | IAM-08 |
| 8.3 | Information access restriction | Yes | Implemented | Role-based access within Kestrel AP and the Admin Console | IAM-05, IAM-08 |
| 8.4 | Access to source code | Yes | Implemented | GitHub access via SSO and team-based permissions (secure development controls tested separately) | IAM-05, IAM-09 |
| 8.5 | Secure authentication | Yes | Implemented — **OFI-01 raised** (OBS-2026-01) | MFA everywhere; phishing-resistant MFA for privileged users in progress | IAM-02, IAM-03, IAM-04 |
| 8.15 | Logging | Yes | Implemented | Authentication and administrative event logging | IAM-12 |
| 8.16 | Monitoring activities | Yes | Implemented | Detection of anomalous authentication behaviour | IAM-12 |
| 8.18 | Use of privileged utility programs | Yes | Implemented (partially covered here) | Administrative tooling restricted to privileged roles | IAM-08 |

Implementation status reflects the 2026 ISO/IEC 27001 surveillance audit, performed alongside SOC 2® fieldwork using the same evidence — see the [findings report](../02-audit-testing-workpapers/audit-finding-and-remediation.md).

## 8. ISMS clause linkage

Annex A controls only work inside a functioning management system. This table shows where the mandatory clauses are evidenced in this repository.

| Clause | Requirement (summary) | Evidence in this repository |
|---|---|---|
| 4.3 | ISMS scope | System boundaries in the [testing program](../02-audit-testing-workpapers/control-testing-program.md) §2 |
| 5.2 · A.5.1 | Information security policy and topic-specific policies | [IAM-POL-001](../03-policy-governance/identity-access-management-policy.md) |
| 6.1.2 · 8.2 | Information security risk assessment | [Risk assessment](authentication-risk-assessment.md) §3–§5 |
| 6.1.3 · 8.3 | Risk treatment and Statement of Applicability | Risk assessment §8; §7 of this document |
| 9.1 | Monitoring, measurement, analysis and evaluation | KRIs — risk assessment §7 |
| 9.2 | Internal audit | Kestrel's own internal audit programme, run by the Internal Audit Lead independently of IAM operations (not reproduced here). It can reuse the procedures in the [testing program](../02-audit-testing-workpapers/control-testing-program.md); the external surveillance audit evaluates it |
| 9.3 | Management review | Findings presented to the ISSC — [findings report](../02-audit-testing-workpapers/audit-finding-and-remediation.md) §7 |
| 10.2 | Nonconformity and corrective action | [Findings report](../02-audit-testing-workpapers/audit-finding-and-remediation.md) — root cause, corrective action, retest |

## 9. Subservice organisations and complementary controls

Kestrel's SOC 2® report uses the **carve-out method**: controls operated by AWS and Okta are excluded from the scope of the examination, and the controls Kestrel expects them to operate are disclosed as complementary subservice organisation controls (CSOCs).

### 9.1 Complementary subservice organisation controls (CSOCs)

| Subservice organisation | Service | CSOCs relevant to IAM (summary) | Kestrel monitoring control |
|---|---|---|---|
| Amazon Web Services | Infrastructure hosting (eu-west-1); AWS IAM Identity Center | Logical and physical access to underlying infrastructure is restricted to authorised AWS personnel; the identity service enforces configured assignments | VEN-03 — annual review of the AWS SOC 2® Type 2 report, its exceptions and bridge letter |
| Okta | Workforce identity platform | Okta personnel access to customer tenants is restricted and monitored; authentication policies are enforced as configured; platform changes are controlled | VEN-03 — annual review of the Okta SOC 2® Type 2 report, its exceptions and bridge letter |

The reviews also map the user-entity responsibilities (CUECs) listed in each provider's report to Kestrel controls. Under the shared responsibility model these typically include configuring authentication policies and administering administrator accounts in the identity platform (Kestrel: IAM-03, IAM-08) and managing identities and permissions inside the customer's own cloud accounts (Kestrel: IAM-02, IAM-08). The review procedure is set out in the [control testing program](../02-audit-testing-workpapers/control-testing-program.md) §6.

### 9.2 Complementary user entity controls (CUECs) — Kestrel's customers

| CUEC | Related criterion |
|---|---|
| Customers provision, modify and remove their own users' access to Kestrel AP in a timely manner | CC6.2, CC6.3 |
| Customers periodically review their users' access to Kestrel AP | CC6.2 |
| Customers that use SSO with Kestrel AP are responsible for authentication controls (including MFA) in their own identity provider | CC6.1 |
| Customers notify Kestrel promptly of changes to contacts authorised to request support access to their tenant | CC6.2 |

## 10. Regulatory overlays

### 10.1 HIPAA Security Rule (45 CFR Part 164, Subpart C)

Some US healthcare customers submit invoices containing protected health information (for example, patient-specific device or home-care invoices). Kestrel has signed Business Associate Agreements (BAAs) with these customers and is therefore a **business associate** subject to the Security Rule for electronic PHI (ePHI). The same IAM controls evidence the identity-related standards and implementation specifications:

| Citation | Standard / implementation specification | Required (R) / Addressable (A) | Kestrel controls |
|---|---|---|---|
| §164.308(a)(1)(ii)(D) | Information system activity review | R | IAM-09, IAM-12 |
| §164.308(a)(3)(ii)(B) | Workforce clearance procedure | A | IAM-05 (with HR pre-employment screening) |
| §164.308(a)(3)(ii)(C) | Termination procedures | A | IAM-07 — **exceptions noted** (IAM-2026-01) |
| §164.308(a)(4)(ii)(B) | Access authorization | A | IAM-05, IAM-08 |
| §164.308(a)(4)(ii)(C) | Access establishment and modification | A | IAM-05, IAM-06, IAM-09 |
| §164.308(a)(5)(ii)(C) | Log-in monitoring | A | IAM-12 |
| §164.308(a)(5)(ii)(D) | Password management | A | IAM-04 |
| §164.312(a)(2)(i) | Unique user identification | R | IAM-02 |
| §164.312(a)(2)(ii) | Emergency access procedure | R | IAM-08 (break-glass accounts) |
| §164.312(a)(2)(iii) | Automatic logoff | A | Session limits — IAM-POL-001 §7.5 |
| §164.312(b) | Audit controls (standard) | R | IAM-12 |
| §164.312(d) | Person or entity authentication (standard) | R | IAM-02, IAM-03, IAM-04 |

> **"Addressable" is not "optional".** The entity must implement an addressable specification where reasonable and appropriate, or document why it is not and implement an equivalent alternative. HHS published a proposed update to the Security Rule in January 2025 (including mandatory MFA and removal of most "addressable" designations); it had **not been finalised at the time of writing**, so the current rule applies — monitor for a final rule.

> The same controls could support a **SOC 2+ HIPAA** examination, in which HIPAA Security Rule requirements are added to the SOC 2® report as additional criteria — avoiding a separate assessment for healthcare customers.

### 10.2 EU requirements (GDPR, NIS2, DORA)

Kestrel operates from Ireland, processes personal data, and serves regulated customers. The same IAM controls provide evidence for these requirements:

| Requirement | Summary (paraphrased) | Kestrel controls |
|---|---|---|
| GDPR Art. 32(1)(b) | Ongoing confidentiality, integrity, availability and resilience of processing systems | IAM-02 – IAM-09 |
| GDPR Art. 32(1)(d) | A process for regularly testing, assessing and evaluating the effectiveness of security measures | This audit; IAM-09 |
| NIS2 Directive Art. 21(2)(i) | Human resources security, access control policies and asset management | IAM-01, IAM-05 – IAM-07, IAM-09 |
| NIS2 Directive Art. 21(2)(j) | Multi-factor or continuous authentication where appropriate | IAM-03 |
| DORA Art. 9(4)(c) | Access limited to what legitimate, approved functions require, with sound administration of access rights | IAM-05 – IAM-09 |
| DORA Art. 9(4)(d) | Strong authentication mechanisms | IAM-03, IAM-04 |

> DORA applies to Kestrel's financial-entity customers, not directly to Kestrel. As an ICT third-party service provider, Kestrel receives these obligations through contractual flow-down (DORA Art. 30) and evidences them with its SOC 2® report and ISO/IEC 27001 certificate.

## 11. Coverage summary and gaps

| Area | Result |
|---|---|
| CC6.1 points of focus | 6 of 11 addressed by IAM controls (5 Full, 1 Partial); 5 addressed by other domains |
| CC6.2 key points of focus | 3 of 3 addressed by design; operating exceptions in IAM-07 and IAM-09 |
| ISO/IEC 27001 Annex A (IAM-relevant) | 15 controls applicable and implemented; 1 minor NC open (5.18 / 6.5); 1 OFI (8.5) |
| HIPAA Security Rule (IAM-relevant) | 12 standards and implementation specifications mapped; operating exceptions affect §164.308(a)(3)(ii)(C), §164.308(a)(4)(ii)(C) and §164.308(a)(1)(ii)(D) |
| NIST CSF 2.0 | PR.AA-01, -03 and -05 fully supported; detection supported via DE.CM-03 and DE.AE-02 |

**Gaps carried into remediation:**

1. **Design weakness** — contractor access had no automated expiry at contract end (IAM-11) → finding IAM-2026-01.
2. **Design weakness** — legacy non-federated database roles sat outside the identity lifecycle (IAM-07) → finding IAM-2026-01.
3. **Maturity gap** — phishing-resistant MFA not yet enforced for privileged identities (IAM-03) → OBS-2026-01.

## 12. Maintenance

| Trigger | Action | Owner |
|---|---|---|
| Annual review | Re-validate every mapping row and coverage rating | GRC Manager |
| Framework revision (e.g., new TSC or ISO amendment) | Gap-assess new or changed requirements; update matrix | GRC Manager |
| Control change or new Tier-1 system | Update control description, mapping and testing program | Control owner + GRC Manager |
| Audit finding | Reflect design changes and status in §6, §7 and §11 | GRC Manager |
