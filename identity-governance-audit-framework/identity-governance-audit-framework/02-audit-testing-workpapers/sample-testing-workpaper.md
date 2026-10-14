# WP-IAM-07 — Test of Operating Effectiveness: Leaver Access Revocation

| Field | Detail |
|---|---|
| Client | Kestrel Ledger Ltd. *(fictitious)* |
| Engagement | KL-SOC2-FY26 — SOC 2® Type 2 (Security), integrated with the ISO/IEC 27001:2022 surveillance audit |
| Review period | 1 July 2025 – 30 June 2026 |
| Workpaper | **WP-IAM-07** (program: [AP-IAM-2026](control-testing-program.md)) |
| Control | IAM-07 — Leavers: timely revocation of logical access |
| Criteria | SOC 2® CC6.2 (primary), CC6.3 · ISO/IEC 27001:2022 A.5.18, A.6.5 · HIPAA §164.308(a)(3)(ii)(C) |
| Control owners | Head of People Operations (employees) · IT Service Desk Lead (contractors, non-federated accounts) |
| Prepared by | A. Huttener — 28 Jul 2026 · extended procedures 6 Aug 2026 *(simulated engagement)* |
| Reviewed by | M. Kelly, Engagement Manager *(fictitious)* — 4 Aug 2026 · review notes cleared 10 Aug 2026 |
| Status | **Final — exceptions noted** → finding [IAM-2026-01](audit-finding-and-remediation.md) |

---

## 1. Objective

Determine whether IAM-07 operated effectively **throughout the review period** — that is, whether logical access to all Tier-1 systems was revoked within the required timeframe for every worker who left Kestrel.

## 2. Control and criteria

**Control as described by management (system description):**
> When a worker leaves Kestrel, their Okta account is deactivated by the end of the next business day after the effective termination date, or on the same day for involuntary terminations. Deactivation removes access to all federated systems. Access to any non-federated account is removed within the same timeframe through the offboarding checklist.

**Criteria in effect during the period:** IAM-POL-001 v3.0 (to 19 Jan 2026) and v3.1 (from 20 Jan 2026), §6.4.

| Exit type | Requirement tested |
|---|---|
| Planned (resignation, contract end) | Okta deactivated no later than the end of the next business day after the effective date |
| Involuntary | Okta deactivated on the effective date, before or at the time of notification |
| Non-federated accounts | Removed within the same timeframe; evidenced in the offboarding ticket |

**Definitions used in testing**
- **Business day:** Monday–Friday excluding Irish public holidays. Holidays in the period: 4 Aug 2025 · 27 Oct 2025 · 25–26 Dec 2025 · 1 Jan 2026 · 2 Feb 2026 · 17 Mar 2026 · 6 Apr 2026 · 4 May 2026 · 1 Jun 2026.
- **BD:** business days from the effective date to the deactivation date (0 = same day).
- **Effective exit time:** 18:00 on the effective date for planned exits; the notification time for involuntary exits.
- **Timestamps:** converted from UTC to Europe/Dublin local time.

## 3. Walkthrough and design evaluation (WT-IAM-07, 15 Jul 2026)

| Path | How it works | Design assessment |
|---|---|---|
| Employees | HiBob termination → Okta deactivation scheduled for 18:00 on the effective date → SSO sessions ended, SCIM deprovisioning downstream → Jira offboarding ticket with checklist | Automated; suitably designed |
| Involuntary exits | People Operations triggers the urgent-termination workflow minutes before the HR meeting | Suitably designed |
| Contractors | Hiring manager raises a Jira offboarding ticket → IT Service Desk deactivates Okta manually | **Design weakness** — relies on a manager remembering to raise a ticket; no automated end date |
| Non-federated accounts | Five legacy local PostgreSQL roles (L-01 – L-05); removal depends on the technician completing the checklist item | **Design weakness** — roles not linked to identities in any authoritative inventory |

**Design conclusion:** the control is suitably designed for the automated employee path, which covers ~82% of exits. The contractor and non-federated paths each have an owner and a ticketed procedure, but no reliable trigger or reference data — **design weaknesses** that make operating failures likely, rather than a missing control. Instead of concluding on design alone, the team tested whether the weaknesses actually produced deviations: stratified random sampling plus **planned 100% analytics** over the contractor stratum and the non-federated accounts. The combined design and operating conclusion is in §11. The full process narrative and risk–control matrix are in the [testing program, Section 4](control-testing-program.md).

## 4. Population and IPE validation

| Source | Content | Count | Procedures performed | Result |
|---|---|---|---|---|
| HiBob report *Terminations by effective date* | Employee exits 01-Jul-2025 – 30-Jun-2026, all legal entities and employment types | 69 | Ⓞ Generation observed 14-Jul-2026 (People Operations Manager, screen share); parameters inspected; headcount roll-forward **362 + 87 hires − 69 exits = 380** agreed to HiBob headcount at 30-Jun-2025 and 30-Jun-2026; 69 agreed to payroll leavers report | ✓ § |
| Procurement contractor register | Contractor engagements ending in the period | 15 | Reconciled both ways to Okta deactivation events for the *Contractors* group in the period (15) and to final supplier invoices in accounts payable — no unmatched items | ✓ § |
| Okta lifecycle events (via Splunk) | Deactivation timestamps for all 84 exits | 84 matched | Ⓞ Query observed; logic inspected (event type, date range, UTC → local conversion); June 2026 event count agreed to the Okta admin console within Okta's 90-day native retention (5 = 5) | ✓ § |
| **Population** | | **84** | | |

## 5. Sample selection

| Item | Detail |
|---|---|
| Method | Stratified random selection · seed 20260720 · output retained in WP-IAM-07.2 |
| Strata | Employees 69 → **20** · Contractors 15 → **5** (proportional). The contractor design weakness is covered by planned 100% analytics of the contractor stratum (§9), not by a larger sample |
| Size rationale | Population of 84 (band 53–250), key control, elevated risk → **25** ([program §3.5](control-testing-program.md)). Statistical reference: 22 items at 90% confidence, 10% tolerable deviation rate, zero expected deviations |
| Coverage | All 12 months; 3 involuntary exits; 1 exit adjacent to a public holiday (S-23) |

## 6. Test attributes

| Attr. | Test | Evidence |
|---|---|---|
| **A** | Exit recorded in the authoritative source with an effective date (HiBob for employees; register for contractors) | HiBob record · contractor register § |
| **B** | Okta account deactivated within SLA — same day for involuntary exits, ≤ 1 BD otherwise | Okta `user.lifecycle.deactivate` event via Splunk § |
| **C** | Non-federated accounts removed within the same SLA (N/A where none exist) | RDS login-role listings, DDL log, ticket |
| **D** | No successful authentication to any Tier-1 system after the effective exit time | Ⓡ Splunk queries over Okta System Log, CloudTrail, GitHub audit log and pgaudit |
| **E** | Offboarding ticket opened by the effective date and checklist completed with IT and People Operations sign-off | Jira ticket and checklist |

## 7. Results

| Sample | Pop. ID | Worker | Type | Exit type | Effective date | Okta deactivated | BD | Non-federated access | A | B | C | D | E | Ticket |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| S-01 | P-004 | EMP-1021 | Employee | Resignation | Fri 11-Jul-2025 | 11-Jul-2025 18:02 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4398 |
| S-02 | P-009 | CTR-0207 | Contractor | Contract end | Thu 31-Jul-2025 | 01-Aug-2025 09:14 | 1 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4466 |
| S-03 | P-013 | EMP-0988 | Employee | Resignation | Fri 15-Aug-2025 | 15-Aug-2025 18:01 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4521 |
| S-04 | P-016 | EMP-1102 | Employee | Involuntary | Wed 27-Aug-2025 | 27-Aug-2025 10:26 ᵃ | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4560 |
| S-05 | P-019 | EMP-0874 | Employee | Resignation | Fri 12-Sep-2025 | 12-Sep-2025 18:00 | 0 | RDS role L-01 — dropped 12-Sep-2025 17:20 | ✓ | ✓ | ✓ | ✓ | ✓ | ITSM-4610 |
| S-06 | P-024 | EMP-1140 | Employee | Resignation | Tue 30-Sep-2025 | 30-Sep-2025 18:03 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4655 |
| S-07 | P-028 | EMP-0921 | Employee | Resignation | Fri 17-Oct-2025 | 17-Oct-2025 18:01 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4719 |
| S-08 | P-031 | EMP-1066 | Employee | Resignation | Fri 31-Oct-2025 | 31-Oct-2025 18:00 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4772 |
| **S-09** | P-036 | CTR-0219 | Contractor | Contract end | Fri 14-Nov-2025 | **20-Nov-2025 14:12** | **4** | None | ✓ | **✗** ᵇ | N/A | ✓ | **✗** ᵇ | ITSM-4858 |
| S-10 | P-040 | EMP-1175 | Employee | Resignation | Fri 28-Nov-2025 | 28-Nov-2025 18:02 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4890 |
| S-11 | P-043 | EMP-0812 | Employee | Resignation | Fri 12-Dec-2025 | 12-Dec-2025 18:01 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4951 |
| S-12 | P-046 | EMP-1203 | Employee | Resignation | Tue 23-Dec-2025 | 23-Dec-2025 18:00 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-4987 |
| S-13 | P-049 | EMP-0955 | Employee | Resignation | Fri 16-Jan-2026 | 16-Jan-2026 18:04 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5040 |
| S-14 | P-053 | CTR-0231 | Contractor | Contract end | Fri 30-Jan-2026 | 30-Jan-2026 17:40 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5096 |
| S-15 | P-055 | EMP-1218 | Employee | Involuntary | Fri 13-Feb-2026 | 13-Feb-2026 13:52 ᵃ | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5141 |
| S-16 | P-059 | EMP-0897 | Employee | Resignation | Fri 27-Feb-2026 | 27-Feb-2026 18:01 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5197 |
| S-17 | P-062 | EMP-1089 | Employee | Resignation | Fri 13-Mar-2026 | 13-Mar-2026 18:00 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5262 |
| **S-18** | P-066 | EMP-0743 | Employee | Resignation | Fri 27-Mar-2026 | 27-Mar-2026 18:02 | 0 | **RDS role L-03 — dropped 21-Apr-2026 11:37** | ✓ | ✓ | **✗** ᶜ | ✓ | **✗** ᶜ | ITSM-5318 |
| S-19 | P-068 | CTR-0244 | Contractor | Contract end | Tue 31-Mar-2026 | 01-Apr-2026 09:30 | 1 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5335 |
| S-20 | P-071 | EMP-1231 | Employee | Resignation | Fri 17-Apr-2026 | 17-Apr-2026 18:01 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5409 |
| S-21 | P-074 | EMP-1004 | Employee | Resignation | Thu 30-Apr-2026 | 30-Apr-2026 18:00 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5466 |
| S-22 | P-077 | EMP-1252 | Employee | Involuntary | Fri 15-May-2026 | 15-May-2026 09:21 ᵃ | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5530 |
| S-23 | P-080 | CTR-0238 | Contractor | Contract end | Fri 29-May-2026 | 02-Jun-2026 08:47 | 1 ᵈ | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5590 |
| S-24 | P-082 | EMP-0966 | Employee | Resignation | Fri 12-Jun-2026 | 12-Jun-2026 18:00 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5641 |
| S-25 | P-083 | EMP-1129 | Employee | Resignation | Fri 26-Jun-2026 | 26-Jun-2026 18:03 | 0 | None | ✓ | ✓ | N/A | ✓ | ✓ | ITSM-5702 |

ᵃ Involuntary exit — deactivation agreed to the HR meeting invitation (S-04 10:30 · S-15 14:00 · S-22 09:30): access removed **before** notification.
ᵇ Exception 1 — see §8.1.
ᶜ Exception 2 — see §8.2.
ᵈ Fri 29-May-2026 → next business day Tue 02-Jun-2026 (Mon 01-Jun-2026 public holiday). Within SLA as defined — see review note RN-03.

| Attribute | A | B | C | D | E |
|---|---|---|---|---|---|
| Passed / applicable | 25 / 25 | 24 / 25 | 1 / 2 | 25 / 25 | 23 / 25 |

**Sample items with at least one exception: 2 of 25 (S-09, S-18).**

## 8. Exception detail

### 8.1 Exception 1 — S-09 (CTR-0219, Sales Engineering contractor)

| | |
|---|---|
| Condition | Contract ended Fri 14-Nov-2025 (register). Offboarding ticket ITSM-4858 was opened by the hiring manager on Thu 20-Nov-2025 10:41; Okta was deactivated at 14:12 the same day — **4 business days after exit (3 beyond SLA), 6 calendar days of exposure**. |
| Access held | Kestrel AP Admin Console — read-only, demo tenants only |
| Cause | Ⓘ Hiring manager (23-Jul-2026) and IT Service Desk Lead (22-Jul-2026): the manager was on annual leave 14–19 Nov 2025 with no delegate; the Service Desk has no visibility of contract end dates; contractor accounts have no automated expiry. Corroborated by leave records and ticket timestamps. |
| Mitigating evidence | Ⓡ Last successful Okta sign-in 14-Nov-2025 16:48 (before exit). No authentication events between 14-Nov 18:00 and 20-Nov 14:12 in Splunk. |

### 8.2 Exception 2 — S-18 (EMP-0743, Platform Engineering)

| | |
|---|---|
| Condition | Okta was deactivated on time (27-Mar-2026 18:02), but legacy local PostgreSQL role **L-03** on production instance kap-prod-db-01 remained active until 21-Apr-2026 11:37 — **25 calendar days / 16 business days after exit**. The role had read/write access to schemas holding invoice data, supplier bank details and PHI. |
| Checklist | Item "Remove non-federated accounts" in ITSM-5318 was marked N/A by the technician. |
| Detection | The Q1 2026 user access review (IAM-09) — RDS certification by the Cloud Platform Lead — flagged L-03 as orphaned on 21-Apr-2026; the role was dropped the same day (DDL log). **The detective control worked.** |
| Cause | Ⓘ IT Service Desk Lead (05-Aug-2026) and Cloud Platform Lead (24-Jul-2026): legacy roles created before IAM database authentication was adopted (2024) were never migrated; no authoritative mapping of roles to people exists, so the checklist step relies on the technician's memory. |
| Mitigating evidence | Ⓡ Last successful L-03 session 19-Mar-2026 09:12 (before exit); **no** connection attempts — successful or failed — between exit and removal (pgaudit via Splunk). Production RDS is reachable only through an AWS session that requires an active Identity Center login, which was revoked at exit. |
| Residual concern | The credential remained valid and could have been used by another insider with network access — the exposure was real even though it was not exploited. |

## 9. Extended procedures (5–6 Aug 2026)

The planned 100% analytics over the contractor stratum and non-federated accounts were extended to the **full population of 84 exits** once the sample produced deviations, to determine whether the exceptions were isolated or systemic.

### 9.1 Full-population analytics — Okta deactivation vs effective date (all 84 exits)

| Stratum | Population | Within SLA | Beyond SLA | Deviation rate |
|---|---|---|---|---|
| Employees | 69 | 69 | 0 | 0.0% |
| Contractors | 15 | 10 | **5** | **33.3%** |
| **Total** | **84** | **79** | **5** | **6.0%** |

| Pop. ID | Worker | Effective date | Okta deactivated | BD | BD beyond SLA | Calendar days exposed | Post-exit authentication |
|---|---|---|---|---|---|---|---|
| P-021 | CTR-0198 | Fri 26-Sep-2025 | Tue 30-Sep-2025 | 2 | 1 | 4 | None |
| P-036 | CTR-0219 (S-09) | Fri 14-Nov-2025 | Thu 20-Nov-2025 | 4 | 3 | 6 | None |
| P-044 | CTR-0225 | Fri 19-Dec-2025 | Wed 31-Dec-2025 | 6 | 5 | 12 | None |
| P-058 | CTR-0236 | Fri 20-Feb-2026 | Tue 03-Mar-2026 | 7 | 6 | 11 | None |
| P-075 | CTR-0241 | Thu 07-May-2026 | Mon 11-May-2026 | 2 | 1 | 4 | None |

### 9.2 Non-federated account inventory

- Ⓞ Login-role listings re-run on all 3 production RDS instances (05-Aug-2026): **12 login roles** — 9 application roles (all in the NHI inventory, IAM-10) and 3 named legacy roles (L-02, L-04, L-05), all owned by current employees.
- DDL history for the period: 2 named roles dropped — L-01 on 12-Sep-2025 (timely, S-05) and L-03 on 21-Apr-2026 (late, S-18). No named roles created.
- Exits population matched to named roles: only the owners of L-01 and L-03 left during the period → **1 of 2 applicable exits deviated**.
- Other Tier-1 systems: AWS IAM credential report shows no IAM users for human access; GitHub enforces SAML SSO with 0 outside collaborators; Admin Console authenticates through Okta only.

### 9.3 Post-exit activity review

For all **6 deviations** (5 contractors + L-03), Splunk was queried for successful and failed authentication events between the effective exit time and revocation across Okta, CloudTrail, GitHub and pgaudit: **no events found**. Log completeness was confirmed (400-day retention; no ingestion gaps > 24 hours during the period — WP-IAM-07.4).

## 10. Evaluation of deviations

| Factor | Assessment |
|---|---|
| Sample deviation rate | 2 of 25 (8.0%). Indicative upper deviation limit ≈ 20% at 90% confidence — well above the 10% tolerable rate, so reliance on the control as designed is not supported |
| Population deviation rate | 6 of 84 (7.1%) — contractors 5 of 15 (33.3%); non-federated exits 1 of 2; employees via Okta 0 of 69 |
| Nature | Operating deviations caused by two **design weaknesses** → systemic within those sub-populations, not isolated errors |
| Sensitivity | High for L-03 (customer payment data and PHI); low for the contractors (limited, mostly read-only access) |
| Duration of exposure | Contractors 4–12 calendar days; L-03 25 calendar days |
| Mitigating factors | IAM-09 detected L-03 (a quarterly review cannot catch short contractor delays); production database reachable only with an active AWS session, revoked at exit |
| Evidence of misuse | None — logs complete for the whole exposure window |

## 11. Conclusion

**IAM-07 did not operate effectively throughout the review period — exceptions noted.**

| Lens | Outcome |
|---|---|
| SOC 2® Section 4 | Exceptions disclosed against IAM-07 (wording drafted in the [findings report](audit-finding-and-remediation.md)) |
| CC6.2 criterion | Met, on the overall evaluation: all 69 automated employee exits on time; deviations confined to two manual sub-populations; the other CC6.2 controls (IAM-05, IAM-06, IAM-11) effective; IAM-09 detected the most sensitive case; no access after exit in complete logs → **no opinion modification** proposed (summary WP-CC6.2-SUM; engagement partner concurrence 12-Aug-2026). Had post-exit access been found, a qualified opinion on CC6.2 would have been considered |
| ISO/IEC 27001 surveillance audit | Same agreed facts graded by the ISO team as minor nonconformity **NC-01** — Clause 8.1 with Annex A 5.18 and 6.5 |
| HIPAA | Termination procedure §164.308(a)(3)(ii)(C) not consistently applied. No access occurred, so no use or disclosure of PHI → control deficiency rather than a breach; management asked to document its breach risk assessment (see [findings report](audit-finding-and-remediation.md) §3.7) |
| Finding | **IAM-2026-01 — rated High** |

## 12. Review notes

| Ref | Raised | Note | Response | Cleared |
|---|---|---|---|---|
| RN-01 | M. Kelly · 04-Aug-2026 | Okta keeps its System Log for only 90 days. How was post-exit activity evidenced for 2025 exits? | Logs taken from Splunk (Okta add-on), 400-day retention — setting inspected (WP-IAM-07.4). Ingestion completeness: daily event counts agreed to the Okta System Log for 30 days within native retention; no ingestion-health alerts open > 24 h during the period. | 06-Aug-2026 |
| RN-02 | M. Kelly · 04-Aug-2026 | Contractors are not in HiBob — how do we know the contractor population is complete? | Register (15) reconciled both ways to Okta deactivations for the *Contractors* group and to final supplier invoices in accounts payable; no unmatched items (WP-IAM-07.1). | 06-Aug-2026 |
| RN-03 | M. Kelly · 04-Aug-2026 | S-23 kept access for 4 calendar days across a bank-holiday weekend. Exception? | Not against the control as described (next business day = Tue 02-Jun-2026). Design weakness raised with management and included in IAM-2026-01, recommendation R3 (scheduled deactivation at the effective date). | 06-Aug-2026 |
| RN-04 | M. Kelly · 04-Aug-2026 | Document why the S-18 checklist item was marked N/A. | Ⓘ IT Service Desk Lead (05-Aug-2026) and inspection of ITSM-5318: the technician relied on memory; no authoritative mapping of legacy roles to people exists. Root cause recorded in §8.2. | 10-Aug-2026 |

## 13. Tickmark legend and supporting workpapers

| Tickmark | Meaning |
|---|---|
| ✓ | Agreed to source evidence without exception |
| ✗ | Exception — see §8 |
| N/A | Attribute not applicable |
| Ⓡ | Re-performed by the auditor |
| Ⓞ | Observed by the auditor |
| Ⓘ | Inquiry — corroborated by other evidence |
| § | Agreed to a system-generated report whose completeness and accuracy were validated |

| Sub-workpaper | Content |
|---|---|
| WP-IAM-07.1 | Population sources and IPE validation |
| WP-IAM-07.2 | Sample selection output (seed, strata) |
| WP-IAM-07.3 | Log extracts for sampled items and deviations |
| WP-IAM-07.4 | Splunk retention and ingestion-health evidence |
| WP-IAM-07.5 | RDS login-role listings and DDL history |
| WP-IAM-07.6 | Full-population analytics output |

*Supporting sub-workpapers are referenced for realism and are not included in this public repository.*

## 14. Sign-off

| Role | Name | Date |
|---|---|---|
| Preparer | A. Huttener *(simulated)* | 28 Jul 2026 · extended procedures 6 Aug 2026 |
| Reviewer | M. Kelly, Engagement Manager *(fictitious)* | 4 Aug 2026 · notes cleared 10 Aug 2026 |
| Engagement partner | J. O'Connor *(fictitious)* | 12 Aug 2026 — concurrence with the CC6.2 conclusion |
