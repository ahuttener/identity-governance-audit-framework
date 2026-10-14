# Identity and Access Management Policy

**Kestrel Ledger Ltd.** *(fictitious)*

| Field | Detail |
|---|---|
| Document ID | IAM-POL-001 |
| Version | 3.2 |
| Status | Approved |
| Classification | Internal |
| Policy owner | Chief Information Security Officer (CISO) |
| Approved by | Information Security Steering Committee (ISSC) |
| Approval date | 30 September 2026 |
| Effective date | 1 October 2026 |
| Next scheduled review | September 2027, or earlier upon significant change |
| Related documents | Information Security Policy (ISP-001) · Authentication Standard (IAM-STD-02) · Privileged Access Standard (IAM-STD-03) · Joiner-Mover-Leaver Procedure (IAM-PRO-01) · User Access Review Procedure (IAM-PRO-04) · Exception Management Procedure (GRC-PRO-02) · Logging and Monitoring Standard (SEC-STD-05) |

> [!NOTE]
> **Repository context — auditor's note.** This is the auditee's policy: the **criteria** against which IAM controls were tested. Testing during the review period (1 Jul 2025 – 30 Jun 2026) used v3.0 and v3.1. Version 3.2 incorporates remediation of findings [IAM-2026-01](../02-audit-testing-workpapers/audit-finding-and-remediation.md), IAM-2026-02 and OBS-2026-01 — showing how audit results feed back into governance.

## Revision history

| Version | Date | Author | Summary of changes |
|---|---|---|---|
| 2.0 | 08 Feb 2024 | CISO | Restructured for ISO/IEC 27001:2022 Annex A (5.15–5.18, 8.2–8.5); introduced Tier-1 system classification |
| 3.0 | 22 Jan 2025 | CISO | HR-driven provisioning (HiBob → Okta); leaver SLA "end of next business day"; just-in-time privileged access |
| 3.1 | 20 Jan 2026 | IAM Lead | Annual review; access reviews performed in Okta Identity Governance; system-generated listings mandatory (IPE) |
| 3.2 | 30 Sep 2026 | IAM Lead · GRC Manager | **Post-audit update:** contractors mastered in HiBob with mandatory end dates (§6.5); scheduled deactivation for planned exits (§6.4); prohibition of named non-federated accounts (§9.5); phishing-resistant MFA mandatory for privileged roles by 31 Mar 2027 (§7.2); weekly identity reconciliation (§14.2) |

---

## 1. Purpose

This policy defines how Kestrel establishes, authenticates, authorises, reviews and removes identities and access. It protects the confidentiality, integrity and availability of customer and corporate information — including payment data, personal data and electronic protected health information (ePHI) — and supports Kestrel's commitments under its SOC 2® report, ISO/IEC 27001 certification, Business Associate Agreements and customer contracts.

## 2. Scope

This policy applies to:

- all **workforce members** — employees, contractors, temporary staff and third-party personnel;
- all **non-human identities** — service accounts, API tokens, workload identities and application database roles;
- all Kestrel information systems, with additional requirements for **Tier-1 systems**: Okta, AWS production accounts, Amazon RDS production databases, GitHub Enterprise and the Kestrel AP Admin Console.

**Out of scope:** customer user accounts in Kestrel AP that are administered by customers. These are governed by the Product Security Standard and customer agreements; customer responsibilities are communicated as complementary user entity controls (CUECs).

## 3. Normative references

- ISO/IEC 27001:2022 — Annex A 5.3, 5.15–5.18, 6.5, 8.2–8.5, 8.15, 8.16
- AICPA Trust Services Criteria — CC6.1, CC6.2, CC6.3, CC7.2
- HIPAA Security Rule — 45 CFR §164.308 and §164.312
- NIST SP 800-63B-4 (authentication and authenticator management) · NIST CSF 2.0 (PR.AA)
- GDPR — Article 32 (security of processing)

## 4. Roles and responsibilities

| Role | Responsibilities |
|---|---|
| Information Security Steering Committee (ISSC) | Approves this policy; receives KRIs monthly; oversees High-rated exceptions |
| CISO | Policy owner; approves privileged eligibility and exceptions; co-signs privileged access reviews |
| IAM Lead | Operates Okta and Okta Identity Governance (OIG); maintains lifecycle automation, authentication policies and the weekly reconciliation |
| Head of People Operations | Maintains HiBob as the authoritative source for all workers, including accurate effective dates and contractor end dates |
| Hiring managers and sponsors | Request and justify access; sponsor contractors; re-certify contractor access every 90 days |
| System owners | Maintain role-based access matrices; approve access; perform access reviews |
| IT Service Desk | Fulfils requests; verifies identity before authenticator resets; completes offboarding checklists |
| Security Operations | Monitors authentication events and triages alerts |
| GRC Manager | Coordinates access reviews; maintains the exception register; monitors KRIs |
| Internal Audit Lead | Performs the ISMS internal audit (ISO/IEC 27001 Clause 9.2) independently of IAM operations |
| All workforce | Protect authenticators; never share accounts; report suspected compromise immediately |

## 5. Principles

1. **Least privilege and need-to-know** — access is limited to what the role requires.
2. **Default deny** — access is not granted unless explicitly authorised.
3. **One person, one identity** — every identity is unique and attributable to an individual.
4. **Segregation of duties** — no individual controls a process end to end (§11).
5. **Authoritative source drives the lifecycle** — identities exist only for workers recorded in HiBob.
6. **Centralised authentication** — workforce access is federated through Okta.
7. **Accountability** — access and administrative actions are logged and traceable.

## 6. Identity lifecycle

### 6.1 Authoritative sources and unique identities

6.1.1 HiBob is the authoritative source for every worker — employees and, from 1 October 2026, contractors and temporary staff. No Okta account may be created for a person who does not exist in HiBob.
6.1.2 The non-human identity (NHI) inventory is the authoritative source for non-human identities (§9).
6.1.3 Each person receives one unique, named identity. Shared or generic accounts are prohibited unless approved as an exception (§13), with an accountable owner and logging.

### 6.2 Joiners

6.2.1 Birthright access is assigned automatically from HiBob attributes according to the role-based access matrix maintained by system owners.
6.2.2 Any other access to Tier-1 systems must be requested in OIG with a business justification and approved by the line manager **and** the system owner before it is granted.
6.2.3 Accounts must not be active before the start date. Approved access is provisioned within 2 business days.
6.2.4 The approver must not be the requester or the person provisioning the access.

### 6.3 Movers

6.3.1 A change of job, department or manager in HiBob triggers a review of all access outside the new birthright set.
6.3.2 Access no longer required must be removed within 5 business days of the change.

### 6.4 Leavers

6.4.1 Access must be revoked as follows:

| Exit type | Requirement |
|---|---|
| Planned (resignation, contract end, retirement) | Okta deactivation scheduled for **18:00 local time on the effective date** |
| Unplanned (immediate resignation, death, abandonment of post) | Deactivation within **4 business hours** of HR notification |
| Involuntary | Deactivation **before or at the time of notification** |
| Non-federated accounts (registered exceptions only) | Removed within the same timeframe and evidenced in the offboarding ticket |

6.4.2 Deactivation must end all active sessions and trigger deprovisioning in downstream systems.
6.4.3 Offboarding must include the return of assets and a reminder of post-employment confidentiality obligations.
6.4.4 The weekly identity reconciliation (§14.2) detects accounts that remain active for workers who are no longer active in HiBob.

### 6.5 Contractors and third parties

6.5.1 Every contractor must have a named Kestrel sponsor, a signed agreement with confidentiality and security terms, and a HiBob record with an end date that does not exceed the contract end date.
6.5.2 Access expires automatically at the end date. Extensions must be approved by the sponsor **before** the end date.
6.5.3 Sponsors must re-certify contractor access every 90 days.
6.5.4 Third-party remote support access must be session-based, time-limited and monitored.

### 6.6 Dormant accounts

Workforce accounts with no successful authentication for 60 days are suspended automatically, unless the worker is on approved leave recorded in HiBob. Reactivation requires manager approval.

## 7. Authentication

### 7.1 Single sign-on

All Tier-1 and business applications must authenticate workforce users through Okta (SAML 2.0 or OIDC). Local authentication must be disabled where the application supports it; where it does not, the application must be registered as an exception (§13).

### 7.2 Multi-factor authentication

7.2.1 MFA is required for all workforce access to all applications.
7.2.2 Permitted authenticators: FIDO2/WebAuthn security keys or platform authenticators; Okta FastPass configured for phishing resistance; Okta Verify push with number challenge; time-based one-time passwords (fallback only).
7.2.3 SMS, voice and email one-time codes are not permitted.
7.2.4 **Privileged identities must use phishing-resistant authenticators by 31 March 2027.**

### 7.3 Passwords and passphrases

Aligned with NIST SP 800-63B-4:

- Minimum **14 characters** for workforce passwords, which are always combined with MFA. At least 64 characters must be accepted, including spaces and all printable characters.
- Where a registered exception requires a password to be the only authenticator, the minimum is **15 characters**.
- **No composition rules** (e.g., mandatory symbols) and **no periodic expiry**. A change is required on evidence or suspicion of compromise.
- New passwords are screened against lists of common, expected and compromised passwords.
- Accounts lock after 10 consecutive failed attempts.
- Credentials not federated through Okta must be stored in the company password manager.

### 7.4 Authenticator reset and identity verification

7.4.1 Password and authenticator resets require identity verification by the IT Service Desk through a live video call with a photo-ID match against the HiBob profile, plus confirmation by the worker's manager through a separate channel. Verification evidence is recorded in the ticket.
7.4.2 From 1 January 2027, resets for privileged identities additionally require in-person verification or verification by a second authorised approver.
7.4.3 Self-service resets are permitted only with an already-enrolled phishing-resistant authenticator.

### 7.5 Session management

- Okta global session: maximum 12 hours; idle timeout 2 hours.
- Administrative consoles (Okta, AWS, Admin Console): re-authentication at least every hour; idle timeout 15 minutes.

## 8. Privileged access management

**8.1 Eligibility.** Privileged roles are defined in IAM-STD-03 (Okta administrators, AWS production administrator permission sets, RDS administrative roles, GitHub organisation owners, Admin Console super-users). Eligibility requires CISO and system-owner approval with a documented justification and is reviewed quarterly (§10).

**8.2 Just-in-time elevation.** There is no standing privileged access to production, except break-glass accounts and no more than two named platform roles approved by the CISO. Elevation is requested in OIG with a justification and ticket reference, approved by someone other than the requester, limited to 4 hours, and expires automatically.

**8.3 Separate administrative identities.** Okta administrator roles are held on separate admin identities that are not used for email, browsing or day-to-day work.

**8.4 Emergency (break-glass) access.** Two Okta break-glass accounts are protected by FIDO2 keys held in a dual-control safe and are excluded only from standard authentication policies. Any use raises a high-severity alert and is limited to declared incidents or planned tests. Credentials are rotated and use is reviewed after every use. The procedure is tested semi-annually.

**8.5 Monitoring.** Elevations, administrative actions and break-glass use are logged and monitored (§12).

## 9. Non-human identities and non-federated accounts

9.1 Every non-human identity must be registered in the NHI inventory with an owner, purpose, system and credential type.
9.2 Non-human identities must not be capable of interactive login.
9.3 Secrets must be stored in AWS Secrets Manager and rotated at least every 90 days, or replaced with short-lived credentials or workload identities.
9.4 Owners review their non-human identities quarterly (§10).
9.5 **Named non-federated human accounts are prohibited.** Existing accounts must be migrated to federated authentication by 31 October 2026. Any exception must be registered (§13), expire within 90 days and be included in the weekly reconciliation.

## 10. User access reviews

10.1 Access is reviewed at the frequencies in Appendix A.
10.2 Listings must be system-generated, with source, parameters and extraction date retained. Manually compiled spreadsheets are not acceptable evidence.
10.3 Reviewers must not certify their own access.
10.4 Reviews must be completed within 30 days of the end of the review period. Reminders are sent from day 15; overdue reviews are escalated to the CISO at day 25.
10.5 Access marked for removal must be removed within 10 business days (2 business days for privileged access), and the removal verified.
10.6 Review evidence is retained for at least six years, in line with HIPAA documentation requirements (45 CFR §164.316(b)(2)).

## 11. Segregation of duties

| Activity | Must not be combined with | Control |
|---|---|---|
| Requesting access | Approving the same access | OIG workflow enforces different approver |
| Approving access | Provisioning the same access | Automated provisioning or a separate administrator |
| Holding an Okta super-administrator role | Approving one's own privileged elevation | OIG approval rules |
| Developing a code change | Deploying the same change to production without peer review | Change Management Policy; GitHub branch protection |
| Reviewing access (certification) | Certifying one's own access | Campaign rules exclude self-review |

## 12. Logging and monitoring

12.1 Authentication events, lifecycle changes, administrative actions, privileged elevations and break-glass use must be logged centrally in the SIEM.
12.2 Logs are retained for at least 400 days and protected from alteration.
12.3 Detection rules alert on anomalous authentication, including password spraying, repeated MFA denials, privileged sign-in from a new country, privileged role assignment outside JIT, and any break-glass use.
12.4 Alerts are triaged within 1 hour (High) and 24 hours (Medium). Confirmed incidents follow the Incident Response Plan.

## 13. Exceptions and risk acceptance

13.1 Exceptions are requested in the GRC tool with a justification, risk assessment, compensating controls and expiry date.
13.2 Maximum duration: 6 months (90 days for non-federated accounts).
13.3 Approval: CISO for Low and Medium residual risk; Risk Committee for High.
13.4 The GRC Manager reviews the exception register monthly and reports it to the ISSC.

## 14. Compliance monitoring and enforcement

14.1 KRIs are reported monthly to the ISSC: MFA coverage, phishing-resistant MFA for privileged identities, exits deprovisioned within SLA, access-review timeliness, dormant accounts, standing privileged assignments and non-human identity ownership.
14.2 A **weekly automated reconciliation** compares Okta, HiBob and the register of non-federated accounts. Discrepancies are ticketed and resolved within 2 business days.
14.3 Compliance is assessed through the annual internal audit (ISO/IEC 27001 Clause 9.2), ISO/IEC 27001 certification audits and the annual SOC 2® Type 2 examination.
14.4 Violations may result in disciplinary action under the HR Disciplinary Policy. For third parties, contractual remedies apply.

## 15. Policy governance

15.1 This policy is reviewed at least annually and upon significant change — a new Tier-1 system, a change of identity provider, a regulatory change, an audit finding or a material incident.
15.2 It is approved by the ISSC, published on the intranet, and acknowledged by the workforce at onboarding and annually thereafter.

---

## Appendix A — Access review matrix

| Tier | Systems | Frequency | Reviewer |
|---|---|---|---|
| Tier 1 | Okta administrator roles · AWS production · RDS production · GitHub Enterprise · Kestrel AP Admin Console | Quarterly | System owner; CISO co-signs privileged access |
| Tier 2 | HiBob · Jira Service Management · finance systems | Semi-annual | System owner |
| Tier 3 | Other applications federated through Okta | Annual | Application owner |
| Non-human identities | All | Quarterly | NHI owner |
| Contractors | All systems | Every 90 days | Sponsor |

## Appendix B — Service levels

| Event | Requirement | Section |
|---|---|---|
| Joiner provisioning | Within 2 business days of approval; never before the start date | 6.2 |
| Mover — remove access no longer required | Within 5 business days | 6.3 |
| Leaver — planned exit | Scheduled deactivation at 18:00 on the effective date | 6.4 |
| Leaver — unplanned exit | Within 4 business hours of HR notification | 6.4 |
| Leaver — involuntary exit | Before or at notification | 6.4 |
| Contractor access end | Automatic at end date; extension approved before end date | 6.5 |
| Dormant account | Suspended after 60 days without sign-in | 6.6 |
| Just-in-time elevation | Maximum 4 hours | 8.2 |
| Access review completion | Within 30 days of period end | 10 |
| Removal after review | 10 business days (privileged: 2) | 10 |
| Non-human identity secret rotation | ≤ 90 days, or short-lived credentials | 9 |
| Log retention | ≥ 400 days | 12 |
| Reconciliation discrepancies | Resolved within 2 business days | 14.2 |
| Exception maximum duration | 6 months (non-federated accounts: 90 days) | 13 |

## Appendix C — Definitions

| Term | Definition |
|---|---|
| Authoritative source | The system of record that determines whether a person is an active worker (HiBob) |
| Break-glass account | An emergency account used only when normal administrative access is unavailable |
| Business day | Monday to Friday, excluding Irish public holidays |
| Effective date | The date a worker's employment or engagement ends, as recorded in HiBob |
| Federated account | An account whose authentication is performed by Okta |
| Non-federated account | An account that authenticates locally in a system rather than through Okta |
| Non-human identity (NHI) | A service account, API token, workload identity or application role not tied to a person |
| Phishing-resistant authenticator | An authenticator bound to the legitimate site's origin, so credentials cannot be replayed on a fake site (e.g., FIDO2/WebAuthn) |
| Privileged access | Access that can change security settings, other users' access or production data and infrastructure |
| Tier-1 system | A system that stores or controls access to customer data or production infrastructure |
| Just-in-time (JIT) elevation | Temporary, approved assignment of a privileged role that expires automatically |

## Appendix D — Control crosswalk

| Policy section | Kestrel control | SOC 2® | ISO/IEC 27001:2022 | HIPAA |
|---|---|---|---|---|
| §15 | IAM-01 | CC2.2, CC5.3, CC6.1 | A.5.1, A.5.15 | §164.316 |
| §6.1, §7.1 | IAM-02 | CC6.1 | A.5.16, A.8.5 | §164.312(a)(2)(i) |
| §7.2 | IAM-03 | CC6.1, CC6.6 | A.5.17, A.8.5 | §164.312(d) |
| §7.3, §7.4 | IAM-04 | CC6.1 | A.5.17, A.8.5 | §164.308(a)(5)(ii)(D) |
| §6.2 | IAM-05 | CC6.2, CC6.3 | A.5.18, A.8.3 | §164.308(a)(4)(ii)(B) |
| §6.3 | IAM-06 | CC6.2, CC6.3 | A.5.18, A.6.5 | §164.308(a)(4)(ii)(C) |
| §6.4 | IAM-07 | CC6.2, CC6.3 | A.5.18, A.6.5 | §164.308(a)(3)(ii)(C) |
| §8 | IAM-08 | CC6.1, CC6.3 | A.5.3, A.8.2 | §164.312(a)(2)(ii) |
| §10 | IAM-09 | CC6.2, CC6.3 | A.5.18 | §164.308(a)(1)(ii)(D) |
| §9 | IAM-10 | CC6.1, CC6.2 | A.5.16, A.5.17 | — |
| §6.5 | IAM-11 | CC6.2, CC9.2 | A.5.19, A.5.20 | — |
| §12 | IAM-12 | CC7.2, CC7.3 | A.8.15, A.8.16 | §164.312(b) |

The full mapping, including NIST CSF 2.0 and CIS Controls v8.1, is in the [compliance mapping](../01-governance-and-risk/compliance-mapping-soc2-iso.md).

## Approval

| Role | Decision | Date |
|---|---|---|
| Policy owner — CISO *(fictitious)* | Approved | 30 Sep 2026 |
| Chair, Information Security Steering Committee *(fictitious)* | Approved | 30 Sep 2026 |
