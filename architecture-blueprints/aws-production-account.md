
# Production Account: Access and Connectivity

| | |
|---|---|
| **Project** | NY staffing company, multi-account AWS architecture |
| **Account** | Production (account 6 of 9) |
| **Region** | us-east-1 (primary), us-east-2 (DR, critical tier only) |
| **Document status** | Draft for client review |
| **Related documents** | [`06-production-account.md`](./06-production-account.md), [`requirements.md`](../../requirements.md) |

> [!NOTE]
> **Status labels used in this document**
> - **Confirmed**: stated by the client in the brief
> - **Proposed**: my design choice, to be validated with the client
> - **Needs approval**: a figure or choice the client must sign off

## Contents

1. [Purpose](#1-purpose)
2. [Access principles](#2-access-principles)
3. [People access matrix](#3-people-access-matrix)
4. [Who has no access](#4-who-has-no-access)
5. [Baseline controls](#5-baseline-controls)
6. [Connectivity: what Production talks to](#6-connectivity-what-production-talks-to)
7. [What Production must not connect to](#7-what-production-must-not-connect-to)
8. [Review checklist](#8-review-checklist)

---

## 1. Purpose

This document lists **who can sign in to the Production account, what each role can do, and which systems the account connects to**. It exists so that:

- the client can see that access follows least privilege (IR-04),
- an auditor can trace every role to a business reason (SC-04),
- the network layer knows which connections the architecture requires.

## 2. Access principles

| Principle | Meaning in this design | Requirement |
|---|---|---|
| Single identity source | Active Directory is the source of truth, federated into IAM Identity Center | IR-01 |
| Least privilege | Every role gets only what its job needs | IR-04 |
| Separation of duties | People who change systems do not approve keys or review security | AS-06 |
| Temporary external access | Vendor and consultant access expires automatically | IR-06 |
| Everything logged | All actions go to the Log Archive account, which IT cannot alter | AS-02 |

## 3. People access matrix

| Permission set | Who | Purpose | Access in Production | Key control | Status |
|---|---|---|---|---|---|
| `ITOps-Operator` | IT operations staff | Run the live systems day to day | Monitor, restart services, patch in approved windows; cannot create or delete core resources | MFA, actions logged | Proposed |
| `SysAdmin-Builder` | Senior system administrators | Make approved changes to servers and applications | Change servers and apps under an approved change request; cannot alter guardrails or logging | Per-change approval; no changes at payroll quarter-end or tax periods (CT-03) | Proposed |
| `DBA-Admin` | Database administrators | Run, tune, and repair databases | Administer databases; cannot read sensitive employee data without approval | Data access logged (SC-01, SC-02) | Proposed |
| `NetAdmin-Builder` | Network administrators | Check connectivity and troubleshoot | Read-only, because the network is owned by the Network account | No change rights | Proposed |
| `Security-Admin` | Security team (inside IT) | Review and maintain security settings | Read access and security settings; cannot change workloads | Separate from the people who run the systems (AS-06) | Proposed |
| `Security-IncidentResponder` | Security responders | Contain and investigate an incident | Emergency elevated access | Limited to hours, reason recorded, alert on every use | Proposed |
| `Security-KeyApprovers` | Named security staff | Approve use of encryption keys | Approve key policy changes; no access to the data itself | Two approvers for key changes (SC-07) | Proposed |
| `Compliance-Auditor` | Internal and external auditors | Gather SOC 2 style evidence | View-only; no changes and no data content | Read-only, time-boxed for audits (SC-04) | Confirmed |
| `Backup-DR-Operator` | Backup team | Restore systems after a failure | Start restores into Production from the Backup account; cannot delete backups | Backups stay in the Backup account (AS-03) | Proposed |
| `External-AfterHours-Operator` | After-hours IT provider | Cover nights and weekends | Run approved runbook actions only, with no open shell | Runbook-only, session logged (AR-11) | Confirmed |
| `External-Support` | Scheduling software vendor | Support the dispatch application | Dispatch application only | Time-limited, expires automatically (IR-06) | Proposed |
| `External-Builder` | Migration consultants | Help move workloads in waves 1 and 2 | Build and migrate during the waves only | Removed after wave 2 (IR-06) | Proposed |

> [!IMPORTANT]
> **Separation of duties:** the people who change systems (`SysAdmin-Builder`) are not the people who approve keys (`Security-KeyApprovers`) or review security (`Security-Admin`), and auditors can only look.

## 4. Who has no access

Field employees, supervisors, and back-office staff use the business applications with Active Directory login and MFA. They **never sign in to AWS** (IR-02).

## 5. Baseline controls

These apply to every row in section 3.

| Control | Purpose | Requirement |
|---|---|---|
| Sign in through IAM Identity Center, with Active Directory as the source | One set of accounts, nothing to keep in sync | IR-01 |
| MFA on every sign-in | Protect against stolen passwords | IR-02 |
| No long-lived IAM users and no standing admin rights | Remove permanent keys that can leak | IR-04 |
| Root user locked away with MFA, used only in emergencies | Protect the most powerful identity | IR-05 |
| External access expires automatically | No forgotten vendor access | IR-06 |
| All actions logged to the Log Archive account | Evidence that IT cannot delete | AS-02 |
| Access reviewed every quarter | Catch roles that are no longer needed | SC-04 |

## 6. Connectivity: what Production talks to

All on-prem traffic travels through one private path: Direct Connect with a VPN backup, ending at the Transit Gateway in the Network account (CR-01, CR-03). Ports and rules are defined in the network layer.

| # | Connects to | Purpose | Path | Requirement |
|---|---|---|---|---|
| 1 | Users (phones, kiosks, supervisors) | Use the clock in/out and dispatch applications | Load balancer (access path pending, OQ-P1) | CR-07 |
| 2 | Active Directory (on-prem) | Employee and supervisor login with MFA | Private link via Transit Gateway | IR-01, IR-02 |
| 3 | AD name lookups (on-prem) | Let servers in AWS find the domain and on-prem systems by name | Private link via Transit Gateway | IR-01 |
| 4 | Payroll and finance (on-prem) | Send approved weekly hours to payroll | Private link via Transit Gateway | WP-01, WP-08 |
| 5 | HR records database (on-prem) | Receive the employee roster and building assignments | Private link via Transit Gateway | WP-04, WP-09 |
| 6 | Network account | Single private doorway to on-prem | Transit Gateway | CR-01, CR-03 |
| 7 | Backup account | Store backups where a compromised account cannot reach them | Account to account, never over the internet | AS-03 |
| 8 | Log Archive account | Keep logs that IT staff cannot delete | Account to account | AS-02, SC-06 |
| 9 | Security / Audit account | Send security findings; give auditors read access | Account to account | SC-09 |
| 10 | us-east-2 (DR region) | Replicate the critical tier for recovery | Region to region, encrypted | AR-10 |

> [!NOTE]
> Rows 2 to 5 share the same on-prem path (row 6), so they appear as one pair of arrows in the diagram. Send only the fields the application needs. Do not copy Social Security numbers or background check details into AWS (SC-01, SC-02).

## 7. What Production must not connect to

| System | Reason |
|---|---|
| Non-Production (dev/test) | A mistake in a lower environment must not reach live data (AS-01) |
| Client-facing account | Isolated by default (AS-04). Any hours shown to building owners go through a defined interface (open question OQ-P4) |
| Sensitive document shares (on-prem) | Background checks and licensing files have no reason to be reached from Production (SC-02) |
| The public internet | No servers with public addresses (CR-02) |

## 8. Review checklist

- [ ] Every permission set matches the access profiles in the access control requirements
- [ ] Every row traces to at least one requirement ID
- [ ] The diagram and this document show the same connections
- [ ] The Client-facing interface question (OQ-P4) is added to the open questions
- [ ] The client has approved the `SysAdmin-Builder` change-approval process
- [ ] The client has confirmed the external access expiry periods

## Change log

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-10-22 | | First draft |
