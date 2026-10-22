
# Management Account: Organization, Guardrails, and Billing


| | |
|---|---|
| **Project** | NY staffing company, multi-account AWS architecture |
| **Account** | Management (account 1 of 9) |
| **Position** | Root of the AWS Organization |
| **Region** | us-east-1 (organization services are managed here) |
| **Document status** | Draft for client review |


> [!NOTE]
> **Status labels used in this document**
> - **Confirmed**: stated by the client in the brief
> - **Proposed**: my design choice, to be validated with the client
> - **Needs approval**: a figure or choice the client must sign off

## Contents

1. [Purpose](#1-purpose)
2. [What runs here and what must never run here](#2-what-runs-here-and-what-must-never-run-here)
3. [Organization structure](#3-organization-structure)
4. [Guardrails](#4-guardrails)
5. [Access](#5-access)
6. [Services managed from this account](#6-services-managed-from-this-account)
7. [Billing and cost controls](#7-billing-and-cost-controls)
8. [Requirements covered](#8-requirements-covered)
9. [Decision records](#9-decision-records)
10. [Risks](#10-risks)
11. [Open questions](#11-open-questions)
12. [Review checklist](#12-review-checklist)

---

## 1. Purpose

The Management account is the **control point for the whole organization**. It creates and groups the other eight accounts, applies the rules every account inherits, and receives the single consolidated bill.

It exists so that:

- production, dev/test, logs, backups, and client-facing systems can be kept in separate accounts (AS-01, AS-02, AS-03, AS-04),
- one small team can set rules once and have them apply everywhere (CT-04),
- the client can explain every guardrail to building owners and auditors (BR-05, SC-04).

> [!IMPORTANT]
> Rules called service control policies (SCPs) apply to every account in the organization **except this one**. That is the main reason it never hosts workloads, and why its access is the most tightly held.

## 2. What runs here and what must never run here

| Runs here | Purpose | Requirement |
|---|---|---|
| AWS Organizations | Create accounts, group them into OUs, attach guardrails | AS-05 |
| Service control policies | Rules that limit what member accounts can do | SC-08 |
| Consolidated billing, budgets, and cost alerts | One bill, early warning on overspend | CT-01 |
| IAM Identity Center (Proposed, see ADR-M3) | Federated sign-in with Active Directory | IR-01 |
| Organization-wide CloudTrail configuration | The trail itself is created here and writes to the Log Archive account | AS-02, SC-06 |

| Must never run here | Where it lives instead | Reason |
|---|---|---|
| Application servers, databases, load balancers | Production, Non-Production, Client-facing | SCPs do not protect this account, and a mistake here affects everything |
| Network connections (Direct Connect, VPN, Transit Gateway) | Network account | Shared connectivity is managed in one place |
| Backups | Backup account | AS-03 |
| Logs | Log Archive account | AS-02 |
| Security tooling and findings | Security / Audit account | AS-06 |
| Admin tools and monitoring | IT Tooling account | Keeps daily IT work out of the control point |

## 3. Organization structure



The organization has five OUs. Four hold accounts, and `Workloads` is a parent that groups the two environment OUs. The structure is two levels deep, which a 14-person IT team can follow without a map (CT-04).

| OU | Accounts | Why grouped | Status |
|---|---|---|---|
| Root | Management | Holds only Organizations, billing, and guardrails | Proposed |
| Security | Security / Audit, Log Archive | Strictest rules and the fewest people; logs and findings must be out of reach of workload teams (AS-02) | Proposed |
| Infrastructure | Network, Backup, IT Tooling | Shared services that every other account depends on | Proposed |
| Workloads > Production | Production, Client-facing | Live systems that get the tightest workload guardrails | Proposed |
| Workloads > Non-Production | Non-Production | Dev and test with lighter rules, kept apart from live data (AS-01) | Proposed |

The account count of nine fits the client's request for about ten accounts (AS-05). IT Tooling has the weakest justification of the nine, and it is reviewed in ADR-M4.

## 4. Guardrails

A guardrail is a rule that **denies** an action, whatever permissions a person holds inside an account. Rules attached higher in the tree apply to everything below.

| # | Guardrail | Attached to | Purpose | Requirement | Status |
|---|---|---|---|---|---|
| 1 | Deny leaving the organization | Root | Keeps every account under central control | SC-08 | Proposed |
| 2 | Deny stopping or deleting CloudTrail, Config, and GuardDuty | Root | Preserves the audit trail | SC-08, SC-04 | Proposed |
| 3 | Deny any region except us-east-1 and us-east-2 | Root | Keeps employee data in approved US regions and limits what can be misused | SC-01 | Proposed |
| 4 | Deny use of the root user in member accounts | Root | Removes the most powerful identity from daily use | IR-05 | Proposed |
| 5 | Deny creating long-lived IAM users and access keys | Root | Removes permanent keys that can leak | IR-04 | Proposed |
| 6 | Deny unencrypted storage | Production OU | Encryption everywhere for employee data | SC-07 | Proposed |
| 7 | Deny public access to storage | Production OU | Protects employee and client data | SC-01 | Proposed |
| 8 | Deny deleting or shortening backup retention | Infrastructure OU | Backups must survive an attack | AS-03 | Proposed |

> [!WARNING]
> The region rule (guardrail 3) must **exempt global services**, such as identity, DNS, and billing, which run outside the two approved regions. Without the exemption, it blocks services the organization needs.

**How guardrails are changed:** every guardrail is tested in the Non-Production OU first, then attached to the Production OU. Nobody edits a guardrail on the live organization without a recorded approval (see section 5).

## 5. Access

Very few people ever sign in to this account. Field employees, supervisors, back-office staff, and external parties have **no access** (IR-02, IR-06).

| Permission set | Who | Purpose | Access in the Management account | Key control | Status |
|---|---|---|---|---|---|
| `Org-Admin` (new) | Two or three named senior IT staff | Create accounts and change guardrails | Full Organizations access, no workload access | Two-person approval for guardrail changes; actions alerted | Proposed |
| `Billing-Viewer` (new) | IT lead and finance contact | See costs and budgets | View-only billing and cost data | Read-only | Proposed |
| `Compliance-Auditor` | Internal and external auditors | Gather SOC 2 style evidence | View-only | Read-only, time-boxed for audits (SC-04) | Confirmed |
| `Security-IncidentResponder` | Security responders | Contain an incident | Emergency elevated access | Limited to hours, reason recorded, alert on every use | Proposed |

> [!NOTE]
> `Org-Admin` and `Billing-Viewer` are new permission sets that the workload accounts do not need. Check them against your existing access profiles before approval.

**Baseline controls**

| Control | Purpose | Requirement |
|---|---|---|
| Sign in through IAM Identity Center, with Active Directory as the source | One set of accounts, nothing to keep in sync | IR-01 |
| MFA on every sign-in | Protect against stolen passwords | IR-02 |
| Root user held under dual control: a hardware MFA key and a shared mailbox, used only in emergencies | Protect the most powerful identity | IR-05 |
| No long-lived IAM users and no standing admin rights | Remove permanent keys that can leak | IR-04 |
| Alerts on every root sign-in and every organization change | Instant notice of unusual activity | SC-09 |
| All actions logged to the Log Archive account | Evidence that IT cannot delete | AS-02 |
| Access reviewed every quarter | Catch roles that are no longer needed | SC-04 |

## 6. Services managed from this account

The Management account **turns on** organization-wide services and **hands day-to-day control to the account built for it**. This is called delegated administration, and it keeps work out of the control point.

| Service | Enabled here | Day-to-day control | Purpose | Requirement |
|---|---|---|---|---|
| CloudTrail (organization trail) | Yes | Log Archive account holds the logs | One trail for every account; logs IT cannot delete | AS-02, SC-06 |
| GuardDuty and Security Hub | Yes | Security / Audit account | Central threat detection and findings | SC-09 |
| AWS Config | Yes | Security / Audit account | Track configuration changes for audits | SC-04 |
| AWS Backup (organization policy) | Yes | Backup account | Consistent backup rules across accounts | AS-03 |
| IAM Identity Center | Yes (Proposed) | Management account | Federated sign-in from Active Directory | IR-01 |

## 7. Billing and cost controls

| Control | Detail | Requirement | Status |
|---|---|---|---|
| Consolidated billing | One bill for all nine accounts | CT-01 | Confirmed |
| Monthly budget | Target spend of $25,000 to $35,000 at steady state | CT-01 | Needs approval |
| Budget alerts | Notify at 80 percent forecast and at 100 percent of the budget | CT-01 | Proposed |
| Cost anomaly detection | Alert on unusual daily spend | CT-01 | Proposed |
| Cost allocation tags | Every resource carries owner, workload, environment, and migration wave | CT-02 | Proposed |
| Cost report per account | Monthly view so the client can see where spend goes | BR-05 | Proposed |

## 8. Requirements covered

AS-01 to AS-06, IR-01, IR-02, IR-04 to IR-06, SC-01, SC-04, SC-06 to SC-09, CT-01, CT-04, BR-05.

## 9. Decision records

| ID | Decision | Options considered | Decision | Status |
|---|---|---|---|---|
| ADR-M1 | OU layout | Flat (all accounts at Root), two levels (as shown), deeper by function | Two levels, grouped by purpose and by environment | Proposed |
| ADR-M2 | Guardrail approach | Deny list, allow list | Deny list: start from full access and block specific risky actions, which is simpler for a small team to maintain | Proposed |
| ADR-M3 | Where IAM Identity Center lives | Management account, or delegated to another account | Management account, because it is the simplest setup for a company only half migrating | Proposed |
| ADR-M4 | IT Tooling account | Keep it, or merge into Network or Production | Keep for now, review after wave 1 if no workload justifies it | Needs approval |

## 10. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| The Management account is compromised | Attacker controls every account | No workloads here, hardware MFA, two or three named admins, alerts on every sign-in and change |
| A guardrail blocks a service the business needs | An outage in Production | Test every guardrail in Non-Production first; exempt global services from the region rule |
| Root access is lost, or the mailbox is unreachable | Cannot recover the organization | Shared mailbox owned by IT, documented recovery steps, tested once a year |
| Costs grow beyond the budget | Surprise bill for the client | Budget alerts, anomaly detection, a monthly review per account |
| The guardrails are too strict for the small IT team to manage | Staff work around them | Keep the list short, document each one, review after wave 1 |

## 11. Open questions

| ID | Question | Why it matters |
|---|---|---|
| OQ-M1 | Who are the two or three named `Org-Admin` holders? | They hold the most powerful access in the design |
| OQ-M2 | Which shared mailbox and hardware key will own the root user? | Needed to create the organization |
| OQ-M3 | Should IAM Identity Center stay in the Management account or move to a delegated account? | Affects the identity design |
| OQ-M4 | Is $25,000 to $35,000 per month realistic for this scope? (same as OQ-05) | Sets the budget alerts |

## 12. Review checklist

- [ ] The OU diagram and the structure table show the same accounts and OUs
- [ ] Every guardrail traces to at least one requirement ID
- [ ] The region guardrail exempts global services
- [ ] `Org-Admin` and `Billing-Viewer` are agreed with the client
- [ ] Every delegated service names the account that runs it day to day
- [ ] Open questions OQ-M1 to OQ-M4 are added to the central list in `requirements.md`

## Change log

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-10-22 | | First draft |
