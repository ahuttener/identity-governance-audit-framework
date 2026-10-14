# Audit Findings & Remediation Report — Identity and Access Management

**Client:** Kestrel Ledger Ltd. *(fictitious)* · **Engagement:** KL-SOC2-FY26

| Field | Detail |
|---|---|
| Report reference | KL-SOC2-FY26-FR-01 |
| Version | 1.0 issued 28 Aug 2026 (with the draft SOC 2® report) · 1.1 updated 9 Oct 2026 — remediation status as reported by management; governance reporting |
| Addressees | CISO · Head of People Operations · Cloud Platform Lead · IAM Lead · IT Service Desk Lead |
| Copied | CFO · GRC Manager · Information Security Steering Committee (ISSC) |
| Prepared by | A. Huttener — GRC Analyst *(simulated engagement)* |
| Reviewed by | M. Kelly — Engagement Manager *(fictitious)* |
| Source workpapers | [Control testing program](control-testing-program.md) · [WP-IAM-07](sample-testing-workpaper.md) · WP-IAM-09 |

---

## 1. Executive summary

Kestrel's identity controls are **largely effective**: 10 of 12 controls tested operated without exception, including SSO, universal MFA, just-in-time privileged access and authentication monitoring. Both findings arise where work happens **outside the automated HR-driven lifecycle** — contractors and legacy database accounts — and in a manual access-review process that has since been replaced.

| ID | Title | Rating | Control(s) | Status at 9 Oct 2026 |
|---|---|---|---|---|
| IAM-2026-01 | Untimely revocation of access for contractors and non-federated database accounts | 🟠 **High** | IAM-07, IAM-11 | **Open** — 4 of 5 actions implemented; retest December 2026 |
| IAM-2026-02 | Quarterly access review completed late, with an unvalidated user listing | 🟡 **Moderate** | IAM-09 | **Closed** — remediated in Q4 2025 and validated by audit |
| OBS-2026-01 | Phishing-resistant MFA not yet enforced for privileged identities | ⚪ **Observation** | IAM-03 | **In progress** — 13 of 21 privileged identities (62%) |

## 2. Rating methodology

| Rating | Definition | Expected remediation |
|---|---|---|
| 🔴 **Critical** | Control absent or failed, with evidence or a high likelihood of compromise of Tier-1 systems or customer data | Immediate containment; plan within 5 business days |
| 🟠 **High** | Control failure or design gap exposing Tier-1 systems or customer data to a credible risk of unauthorised access; systemic in nature | Action plan within 10 business days; remediation ≤ 90 days |
| 🟡 **Moderate** | Weakness that reduces control effectiveness but is limited in scope or mitigated by other controls | Remediation ≤ 180 days |
| 🟢 **Low** | Minor deviation or documentation weakness with limited risk | Remediation ≤ 12 months |
| ⚪ **Observation** | No control deviation; opportunity to improve against good practice | At management discretion; tracked |

> **Three different lenses.** These ratings are advisory. They are separate from (1) **SOC 2®**, where every exception is disclosed in Section 4 and the opinion is modified only if a criterion is not met, and (2) **ISO/IEC 27001**, where findings are graded as major or minor nonconformities or opportunities for improvement. A finding can be rated High here without qualifying the SOC 2® opinion.

---

## 3. Finding IAM-2026-01 — Untimely revocation of access for contractors and non-federated database accounts

**Rating: 🟠 High** · **Controls:** IAM-07 (design weakness and operating exceptions), IAM-11 (design weakness) · **Workpaper:** [WP-IAM-07](sample-testing-workpaper.md)

### 3.1 Condition

For **6 of 84 exits (7.1%)** during the period, access was not revoked within the required timeframe:

- **5 of 15 contractor exits (33%)** — Okta accounts were deactivated 1–6 business days after the required date (4–12 calendar days after exit).
- **1 of 2 employee exits involving a legacy local database role** — role L-03, with read/write access to customer invoice data, supplier bank details and PHI, remained active for **25 calendar days** after exit. It was found and removed by the Q1 2026 quarterly access review.

All 69 employee exits were deactivated in Okta on time. Authentication logs show **no successful or failed authentication** for any of the six accounts after the exit dates.

### 3.2 Criteria

- **IAM-POL-001 v3.0 / v3.1 §6.4** — access revoked by the end of the next business day (same day for involuntary exits), including non-federated accounts.
- **SOC 2® CC6.2** — credentials are removed when a user's access is no longer authorised (paraphrased).
- **ISO/IEC 27001:2022** — Clause 8.1, Annex A 5.18 (access rights) and 6.5 (responsibilities after termination), as declared implemented in the Statement of Applicability.
- **HIPAA Security Rule** — §164.308(a)(3)(ii)(C), termination procedures.

### 3.3 Cause

**Root-cause analysis (5 whys) — contractor exits**

1. Why was CTR-0219 deactivated three business days later than required? → The offboarding ticket was raised four business days after the contract ended.
2. Why? → The hiring manager was on leave, and nobody else knew the contract had ended.
3. Why did nobody else know? → Contract end dates existed only in the procurement register, which IT could not see.
4. Why did the identity system not act on the end date? → Contractors were not mastered in the HRIS, so Okta had no end date.
5. **Root cause:** contractor identities sat **outside the authoritative HR source and its automated lifecycle**.

**Non-federated database roles:** five named PostgreSQL roles created before IAM database authentication was adopted in 2024 were never migrated, and no inventory linked them to individuals. Removal depended on a technician remembering them during offboarding.

**Contributing factor:** no detective reconciliation existed between authoritative sources and accounts other than the quarterly access review — up to four months of potential latency.

### 3.4 Effect

- Former workers retained valid credentials to Tier-1 systems for up to 25 days — in one case with write access to supplier bank details and PHI.
- Potential consequences: unauthorised disclosure or alteration of customer payment data (including payment-redirection fraud); GDPR and HIPAA notification obligations; breach of customer agreements; exceptions in the SOC 2® report that customers will read.
- **Why the rating remains High despite no misuse:** the mitigating factors (no post-exit activity, database reachable only with an active AWS session, detection by IAM-09) reduce the likelihood that the exposure was exploited, but they do not fix the causes. The gaps were systemic for two populations and would recur without design changes.

### 3.5 Recommendations

| # | Recommendation |
|---|---|
| R1 | Master all contractor identities in HiBob with mandatory end dates, and extend the HR-driven Okta lifecycle to contractors; extensions approved by the sponsor before the end date |
| R2 | Eliminate non-federated named accounts: migrate the remaining legacy roles to IAM database authentication and prohibit new ones; any exception registered with an owner and expiry |
| R3 | Schedule Okta deactivation at the effective date and time for all planned exits, removing reliance on the "next business day" allowance |
| R4 | Introduce a weekly automated reconciliation (Okta ↔ HiBob ↔ legacy account inventory) with discrepancies ticketed and reviewed by GRC |
| R5 | Brief hiring managers and add the KRI "exits beyond SLA" to the monthly ISSC dashboard |

### 3.6 Management response

> *"Management agrees with the finding. Contractor lifecycle management will move into HiBob so that every identity has an authoritative end date, and the remaining legacy database accounts will be migrated to centralised authentication. A weekly reconciliation will provide early detection while these changes bed in."* — CISO, Kestrel Ledger Ltd. *(fictitious)*, 21 Aug 2026

| Action | Recommendation | Owner | Target | Status at 9 Oct 2026 |
|---|---|---|---|---|
| 1.1 | R1 — contractors mastered in HiBob; HR-driven deactivation | Head of People Operations | 30 Sep 2026 | ✅ Implemented 24 Sep 2026 |
| 1.2 | R2 — migrate L-02, L-04, L-05; prohibit named local roles | Cloud Platform Lead | 31 Oct 2026 | 🔄 In progress — 2 of 3 migrated |
| 1.3 | R3 — scheduled deactivation; SLA updated in IAM-POL-001 v3.2 | IAM Lead · CISO | 30 Sep 2026 | ✅ Implemented — policy approved 30 Sep 2026 |
| 1.4 | R4 — weekly automated reconciliation | IAM Lead | 15 Sep 2026 | ✅ Implemented 11 Sep 2026 |
| 1.5 | R5 — manager briefing and KRI | GRC Manager | 30 Sep 2026 | ✅ Complete |

### 3.7 How the finding is reported

**SOC 2® report — Section 4 (tests of controls and results)**

| Control activity specified by the service organisation | Tests performed by the service auditor | Results of tests |
|---|---|---|
| IAM-07: Logical access for terminated workers is revoked by the end of the next business day following the termination effective date (same day for involuntary terminations), including access to non-federated accounts. | Inspected the HR terminations report and the contractor register to determine whether the populations were complete. For a sample of terminations, inspected identity-provider deactivation logs, database role listings and offboarding tickets to determine whether access was revoked within the required timeframe. Compared termination dates with identity-provider deactivation dates for all terminations during the period. | **Exceptions noted.** For 6 of 84 terminations, access was not revoked within the required timeframe. Five contractor accounts were deactivated between one and six business days after the required date. For one employee, a non-federated database account remained active for 25 calendar days and was removed through the quarterly user access review. The service auditor inspected authentication logs for these accounts and noted no successful authentication after the termination dates. |

**SOC 2® report — Section 5 (management's response, not covered by the auditor's opinion):** *"Management is moving contractor identities into the HR system with mandatory end dates and automated deactivation (target: 30 September 2026), is migrating the remaining non-federated database accounts to centralised authentication (target: 31 October 2026), and will introduce a weekly automated reconciliation of HR and identity records by 15 September 2026."*

**Opinion impact:** none. CC6.2 was considered met on the overall evaluation — all automated employee exits on time, deviations confined to two manual sub-populations, the other CC6.2 controls effective and no access after exit (see [WP-IAM-07 §11](sample-testing-workpaper.md)).

**ISO/IEC 27001 surveillance audit — minor nonconformity NC-01** (Clause 8.1; Annex A 5.18 and 6.5), graded by the ISO team from the same agreed facts. *Minor* rather than *major* because the control exists and operated for 79 of 84 exits at the identity-provider level, the failures were confined to two identifiable sub-populations, and the quarterly review detected the most sensitive case. A major nonconformity would indicate the absence or total breakdown of a requirement, or doubt that the ISMS can achieve its intended outcomes. Kestrel submitted a corrective action plan on 21 Aug 2026, accepted by the ISO team; effectiveness will be verified at the next surveillance audit. Corrective action follows Clause 10.2: correct the immediate issue, determine root cause, implement actions, verify effectiveness and update the ISMS (policy v3.2 and risk register).

**Privacy and HIPAA assessment:** because no access occurred during the exposure windows, there was no use or disclosure of PHI and no personal data breach under GDPR Art. 4(12). The Privacy Officer documented this conclusion on 25 Aug 2026; no notifications were required. The matter is treated as a control deficiency, not a breach.

### 3.8 Retest plan and closure criteria

| Element | Detail |
|---|---|
| Timing | December 2026, after at least 60 days of operation (exits 24 Sep – 30 Nov 2026) |
| Procedures | (1) Full-population comparison of HiBob effective dates (employees and contractors) with Okta deactivation timestamps. (2) Inspect RDS login-role listings for named roles. (3) Inspect every weekly reconciliation run in the window and its follow-up tickets. (4) Inspect approval and communication of IAM-POL-001 v3.2 |
| Closure criteria | Zero deviations · reconciliation run every week with discrepancies resolved within 2 business days · no named local database roles without a registered exception · policy communicated |
| Retest owner | Kestrel's Internal Audit Lead (independent of IAM operations); the service auditor tests the redesigned control in the next examination period |

---

## 4. Finding IAM-2026-02 — Quarterly access review completed late, with an unvalidated user listing

**Rating: 🟡 Moderate** · **Status: Closed** · **Control:** IAM-09 · **Workpaper:** WP-IAM-09

| Element | Detail |
|---|---|
| **Condition** | The Q3 2025 review cycle (access as at 30 Sep 2025, due 30 Oct 2025) was completed on **10 Nov 2025** for all five Tier-1 systems — 41 days after quarter end, 11 days late. For the AWS production review, the user listing was a **manually compiled spreadsheet** with no evidence of source, query, parameters or extraction date, so its completeness and accuracy (IPE) could not be validated. Extended testing of the Q4 2025 cycle (completed 27 Jan 2026) and testing of the Q1 2026 cycle (completed 21 Apr 2026) found no exceptions — both used system-generated listings in Okta Identity Governance. |
| **Criteria** | IAM-09 control description (completion within 30 days of quarter end, using system-generated listings); IAM-POL-001 v3.0 §10; SOC 2® CC6.2 (periodic review of access credentials); ISO/IEC 27001:2022 A.5.18; HIPAA §164.308(a)(1)(ii)(D) |
| **Cause** | Spreadsheet-based process with no reminders or escalation; reviewer workload during the Q3 release cycle; no documented standard for how listings must be produced |
| **Effect** | Inappropriate access could remain undetected for longer, and the review evidence for one system could not be relied on |
| **Recommendation** | Run reviews in Okta Identity Governance with system-generated listings and campaign metadata; send reminders from day 15 and escalate to the CISO at day 25; document the IPE standard in the review procedure |
| **Management response** | Implemented from the Q4 2025 cycle (January 2026) and reflected in IAM-POL-001 v3.1 |
| **SOC 2® Section 4** | *"Exceptions noted. For the review of the quarter ended 30 September 2025, reviews for all five in-scope systems were completed 41 days after quarter end rather than within 30 days, and for one system the service auditor was unable to determine that the user listing used was complete and accurate. No exceptions were noted for the reviews of the quarters ended 31 December 2025 and 31 March 2026."* The system description discloses the change in the review process from Q4 2025. |
| **ISO/IEC 27001** | Minor nonconformity **NC-02** — closed at the surveillance audit; correction and corrective action verified through the Q4 2025 and Q1 2026 cycles |
| **Validation** | Remediation evidenced by the engagement team's testing of the Q4 2025 and Q1 2026 cycles (WP-IAM-09, 7 Aug 2026) |

---

## 5. Observation OBS-2026-01 — Phishing-resistant MFA for privileged identities

**Rating: ⚪ Observation** · **Control:** IAM-03 (operated effectively) · **ISO/IEC 27001:** opportunity for improvement OFI-01

| Element | Detail |
|---|---|
| **Condition** | At 30 Jun 2026, **8 of 21** privileged identities (38%) used phishing-resistant authenticators (FIDO2/WebAuthn). The other 13 used push notifications with number challenge — resistant to MFA-fatigue attacks, but not to adversary-in-the-middle phishing because the approval is not bound to the website's origin. The policy in force required MFA, which was met, so this is **not an exception**. |
| **Good practice** | NIST SP 800-63B-4 (phishing-resistant authenticators at the highest assurance level); CISA guidance on phishing-resistant MFA; ISO/IEC 27002:2022 guidance on control 8.5 |
| **Why it matters** | Privileged sessions are the most valuable target for an attacker (risk R-01: residual High) |
| **Recommendation** | Enforce phishing-resistant authenticators for all administrative roles through Okta authentication policies; issue hardware security keys; start with Okta super-administrators and AWS production administrators; track through KRI-02 |
| **Management response** | Agreed. IAM-POL-001 v3.2 §7.2 makes phishing-resistant MFA mandatory for privileged roles by 31 Mar 2027. Progress at 9 Oct 2026: **13 of 21 (62%)** |

---

## 6. Remediation tracker (status as reported by management, 9 Oct 2026)

| Action | Finding | Description | Owner | Due | Status | Evidence / validation |
|---|---|---|---|---|---|---|
| 1.1 | IAM-2026-01 | Contractors mastered in HiBob with mandatory end dates; HR-driven Okta deactivation | Head of People Operations | 30 Sep 2026 | ✅ Implemented 24 Sep 2026 | December 2026 retest |
| 1.2 | IAM-2026-01 | Migrate L-02, L-04, L-05 to IAM database authentication; prohibit named local roles | Cloud Platform Lead | 31 Oct 2026 | 🔄 2 of 3 migrated | DDL log; login-role listing |
| 1.3 | IAM-2026-01 | Scheduled deactivation at 18:00 on the effective date; SLA in IAM-POL-001 v3.2 | IAM Lead · CISO | 30 Sep 2026 | ✅ Implemented 30 Sep 2026 | Okta lifecycle configuration |
| 1.4 | IAM-2026-01 | Weekly reconciliation Okta ↔ HiBob ↔ legacy account inventory | IAM Lead | 15 Sep 2026 | ✅ Implemented 11 Sep 2026 — 5 weekly runs; 1 discrepancy (failed sync left an account active after termination) resolved within 1 business day | Run outputs; tickets |
| 1.5 | IAM-2026-01 | Hiring-manager briefing; KRI on ISSC dashboard | GRC Manager | 30 Sep 2026 | ✅ Complete | Briefing record; dashboard |
| 2.1 | IAM-2026-02 | Reviews in OIG with system-generated listings; reminders and escalation | GRC Manager | 31 Jan 2026 | ✅ Closed — validated 7 Aug 2026 | WP-IAM-09 |
| 3.1 | OBS-2026-01 | Phishing-resistant authenticators for all privileged roles | IAM Lead | 31 Mar 2027 | 🔄 13 of 21 (62%) | Okta authenticator report |

---

## 7. Communicating results to stakeholders

### 7.1 Exception notice — full-population update (sent Monday 10 Aug 2026)

Initial written notices for the two sampled exceptions (S-09, S-18) were sent on 27 Jul 2026, within two business days of confirming them with the control owners. This update followed the full-population analytics:

> **Subject:** KL-SOC2-FY26 — Exception notice update: IAM-07 (leaver access revocation)
> **To:** CISO · Head of People Operations · IT Service Desk Lead — **Cc:** GRC Manager · M. Kelly
>
> Hi all,
>
> Further to our notice of 27 July, we extended our testing of control IAM-07 and compared termination dates with account deactivation dates for all 84 leavers between 1 July 2025 and 30 June 2026. We identified six instances where access was not removed within the timeframe described in your system description:
>
> - Five contractor accounts were deactivated 1–6 business days after the required date (detail attached).
> - One legacy database account (role L-03) remained active for 25 days after the employee left. It was removed during the Q1 2026 access review.
>
> We reviewed authentication logs for all six accounts and found no activity after the exit dates.
>
> To complete our evaluation, could you please:
> 1. confirm the facts above, or share any evidence we may have missed, by **Wednesday 12 August**;
> 2. share your view of the root cause; and
> 3. provide management's response and planned actions, with owners and dates, by **Friday 21 August**.
>
> These items will be reported as exceptions in Section 4 of the SOC 2® report. Happy to walk through the detail on a call.
>
> Kind regards,
> A. Huttener

### 7.2 Handling pushback

At the validation meeting on 12 Aug 2026, People Operations initially argued that contractor exits were outside its remit because Procurement managed contractors. The engagement team did not debate ownership inside the finding. Instead, it anchored the discussion on **the control as described in Kestrel's own system description** and on the **agreed facts**. The ownership gap became part of the root cause, and the action plan assigned contractor mastering to People Operations with Procurement's agreement — a better outcome than a disputed finding.

### 7.3 Closing meeting (27 Aug 2026)

1. Results summary — 12 controls, 10 without exception
2. Agreed facts for IAM-2026-01 and IAM-2026-02
3. Management responses and action plans
4. How exceptions will appear in Sections 4 and 5 of the report
5. Report timeline (draft 28 Aug, final 11 Sep) and the management representation letter

### 7.4 Governance reporting

| Forum | Date | Purpose |
|---|---|---|
| Information Security Steering Committee | 2 Sep 2026 | Results presented as input to management review (ISO/IEC 27001 Clause 9.3); KRIs added to the monthly dashboard |
| Risk Committee | 22 Sep 2026 | Approved treatment plans for residual High risks R-01, R-03 and R-07 ([risk assessment v1.1](../01-governance-and-risk/authentication-risk-assessment.md)) |

---

## 8. Distribution and sign-off

| Role | Name | Date |
|---|---|---|
| Prepared by | A. Huttener — GRC Analyst *(simulated)* | 28 Aug 2026 · tracker 9 Oct 2026 |
| Reviewed by | M. Kelly — Engagement Manager *(fictitious)* | 28 Aug 2026 |
| Management response | CISO, Kestrel Ledger Ltd. *(fictitious)* | 21 Aug 2026 |
