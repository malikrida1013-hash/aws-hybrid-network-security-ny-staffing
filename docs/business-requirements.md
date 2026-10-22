## Business understanding

Before designing anything, I mapped how the business works: who uses its
systems, what they need, which data is sensitive, and what could go wrong.
The map below shows the path from the client's situation to its requirements,
problems, and possible solutions. Every design decision later in this project
traces back to it.

<p align="center">
  <img src="business-understanding-map.png.png" alt="Business understanding map" width="650">
</p>

*Figure 1: Business understanding map. Scenario details are assumptions for portfolio purposes.*

Now after analyzing the map, there are different group of people who needs to access differently according
to their needs and the company want structured and control access of these groups whether to their business application or 
their AWS application.
The groups are: 
| Groups | Sub-Groups |
| --- | --- |
| Employees | Field Employees |
| Employees | IT Team |
| Employees | Security Team |
| Employees | Back-Office Staff |
| Employees | Supervisors |
| Employees | Managers |
| Clients | Buildings Owner |
| External parties | Vendors and software providers | 
| External parties | Auditors and compliance reviewers | 
| External parties | Contractors and consultants |
| Executives | C-suite (CEO, COO, CFO) |
| Executives | Department heads and directors |
| Executives | Board members and owners |


### *Authorization Requirements:*

**C-01** — Field employees use the business application only, with no AWS or server access

**C-02** — Field employees can clock in/out and view only their own hours, pay, assigned building, and weekly dashboard

**C-03** — Supervisors see start and end times and attendance for their own buildings and team only

**C-04** — Supervisors cannot assign staff or view pay rates

**C-05** — Scheduling and Dispatch assigns staff to buildings and shifts, without access to pay or personal records

**C-06** — Screening results are visible only to Compliance and Background Checks

**C-07** — Only Payroll can change pay data

**C-08** — Finance and Client Account Management have no access to employee personal records

**C-09** — Reporting has view-only access to approved datasets

**C-10** — IT and cloud admins get role-based admin access, with no permanent full access

**C-11** — Security and audit roles have read-only access to logs

**C-12** — Executives see summary reports, not individual records

**C-13** — Cross-team access requires approval

**C-14** — The person who enters a record is not the only approver

**C-15** — AD login (SSO) for all, with MFA for sensitive systems

**C-16** — Same-day access removal on role change or exit

**C-17** — Periodic access reviews (frequency requires client approval)

**C-18** — Contractors and temporary workers get limited access that expires, requires client approval



### *Authentication Requirements:*

**R-01** — HR staff authenticate with their existing AD credentials (SSO), with no separate passwords. 

**R-02** — MFA is required for access to restricted HR data (ID documents, background checks, personal records). 

**R-03** — MFA is required every time for remote access. 

**R-04** — HR Managers get the strictest authentication rules. 

**R-05** — Access is removed the same day an employee leaves. 

**R-06** — The MFA method must work for staff without company phones.



### *Architecture Requirements:*


# Architecture Requirements

**Client:** New York building staffing company
**Project:** Multi-account AWS architecture (hybrid, ~50% of workloads to AWS)
**Source:** Client brief (section numbers in the Source column refer to it)

## How to read this document

Every requirement has an ID, a source, and a status.

| Status | Meaning |
|---|---|
| Confirmed | The client stated it directly |
| Proposed | Inferred from the brief, to be validated with the client |
| Needs approval | A number or choice the client must sign off before design is final |

Requirement IDs are referenced from the architecture diagrams and decision records.

---

## 1. Business context

| ID | Requirement | Source | Status |
|---|---|---|---|
| BR-01 | Place cleaners, doormen, porters, and maintenance staff in about 220 buildings across the five boroughs and Westchester | §1 | Confirmed |
| BR-02 | Support about 1,800 field employees, 120 supervisors and managers, 90 back-office staff, and 14 IT staff | §1 | Confirmed |
| BR-03 | Move about half of the workloads to AWS within 12 months; the rest stays on-prem | §2 | Confirmed |
| BR-04 | Avoid a full migration and a big-bang cutover | §2, §10 | Confirmed |
| BR-05 | Every design decision must be explainable to building owners | §2 | Confirmed |
| BR-06 | Reduce reliance on aging data centers and avoid costly hardware renewal | §2 | Confirmed |

## 2. Workload placement

| ID | Workload | Target | Wave | Source | Status |
|---|---|---|---|---|---|
| WP-01 | Clock in/out and weekly dashboard | AWS | 1 | §4 | Confirmed |
| WP-02 | Reporting and analytics | AWS | 1 | §4 | Confirmed |
| WP-03 | Dev/test environments | AWS | 1 | §4 | Confirmed |
| WP-04 | Scheduling and dispatch application (vendor software) | AWS | 2 | §4 | Confirmed |
| WP-05 | Client / building owner portal | AWS | 2 | §4 | Confirmed |
| WP-06 | Backups and DR copies | AWS | 1 | §4 | Confirmed |
| WP-07 | Active Directory | On-prem, extended to AWS | n/a | §4, §6 | Confirmed |
| WP-08 | Payroll and finance systems | On-prem for now, review later | n/a | §4 | Needs approval |
| WP-09 | HR records database | On-prem | n/a | §4 | Confirmed |
| WP-10 | Sensitive document shares (background checks, licensing) | On-prem | n/a | §4 | Confirmed |
| WP-11 | Vendor support for the scheduling software must continue after migration | AWS | 2 | §6 | Proposed |

## 3. Availability and recovery

| ID | Workload | Max downtime (RTO) | Max data loss (RPO) | Source | Status |
|---|---|---|---|---|---|
| AR-01 | Clock in/out and dashboard | 1 hour | 5 minutes | §4 | Needs approval |
| AR-02 | Scheduling and dispatch | 2 hours | 15 minutes | §4 | Needs approval |
| AR-03 | Client portal | 4 hours | 1 hour | §4 | Needs approval |
| AR-04 | Reporting and analytics | 24 hours | 24 hours | §4 | Needs approval |
| AR-05 | Payroll and finance | 8 hours | 1 hour | §4 | Needs approval |
| AR-06 | HR records | 8 hours | 1 hour | §4 | Needs approval |
| AR-07 | Active Directory | 1 hour | Near zero | §4 | Needs approval |
| AR-08 | Dev/test | Days | Not critical | §4 | Confirmed |

| ID | Requirement | Source | Status |
|---|---|---|---|
| AR-09 | Clock in/out and dispatch are the most critical services and must be protected first | §9 | Confirmed |
| AR-10 | A tested recovery plan exists, with a second AWS region for the critical tier only | §9 | Confirmed (scope Needs approval) |
| AR-11 | After-hours coverage is by an external provider with runbook-only access | §9 | Confirmed |

## 4. Connectivity

| ID | Requirement | Source | Status |
|---|---|---|---|
| CR-01 | A private, reliable connection from both data centers (Queens, Westchester) to AWS | §5 | Confirmed |
| CR-02 | Payroll and HR traffic must not travel over the public internet only | §5 | Confirmed |
| CR-03 | Backup path so clock in/out keeps working if the primary link fails | §5 | Confirmed |
| CR-04 | Start at about 1 Gbps steady traffic, with room to grow | §5 | Needs approval |
| CR-05 | Dedicated link plus an encrypted backup, with a limited number of circuits (budget) | §5 | Confirmed |
| CR-06 | Client has no Direct Connect experience, so ordering and lead time must be documented | §5 | Confirmed |
| CR-07 | Field employees (phones, kiosks) and supervisors (laptops) reach business applications without AWS access | §3, §6 | Confirmed |

## 5. Identity and access

| ID | Requirement | Source | Status |
|---|---|---|---|
| IR-01 | Active Directory is the single source of truth for identities; no second set of user accounts | §6 | Confirmed |
| IR-02 | Field employees and supervisors use business applications only, with AD login and MFA, and never access AWS | §6 | Confirmed |
| IR-03 | AWS access limited to IT, compliance auditors, the backup team, and defined external parties | §6 | Confirmed |
| IR-04 | Access granted through the defined permission sets, with least privilege | §6 | Confirmed |
| IR-05 | Domain Admin limited to two or three named people | §6 | Confirmed |
| IR-06 | External parties get time-limited, least-privilege access only | §6 | Confirmed |
| IR-07 | AD stays on-prem and is extended to AWS (AD Connector vs AWS Managed AD to be decided) | §12 | Needs approval |

## 6. Account structure and separation

| ID | Requirement | Source | Status |
|---|---|---|---|
| AS-01 | Production is separated from dev/test | §7 | Confirmed |
| AS-02 | Logs are stored where IT staff cannot delete them | §7 | Confirmed |
| AS-03 | Backups are stored where a compromised account cannot reach them | §7 | Confirmed |
| AS-04 | Client-facing systems are isolated from internal systems | §7 | Confirmed |
| AS-05 | Around ten accounts, not dozens, because the IT team is small | §7, §10 | Confirmed (preference) |
| AS-06 | Security sits inside IT, so audit and security functions need their own account boundary | §1, §7 | Proposed |

## 7. Security and compliance

| ID | Requirement | Source | Status |
|---|---|---|---|
| SC-01 | Protect employee personal data (including SSNs) in line with the New York SHIELD Act | §8 | Confirmed |
| SC-02 | Background check records follow FCRA rules: strict access limits and access logging | §8 | Confirmed |
| SC-03 | Payroll and hours records retained for six years | §8 | Confirmed |
| SC-04 | Provide SOC 2 style audit evidence when building owners ask | §8 | Confirmed |
| SC-05 | Card data is out of scope; a third party handles payments | §8 | Confirmed |
| SC-06 | Logs searchable for 12 months and archived for 7 years | §8 | Confirmed |
| SC-07 | Encryption everywhere, with encryption keys approved by the security team | §8 | Confirmed |
| SC-08 | Organization-wide guardrails prevent disabling logging or leaving the organization | §7, §8 | Proposed |
| SC-09 | Centralized threat detection and security findings | §8 | Proposed |

## 8. Cost, timeline, and operations

| ID | Requirement | Source | Status |
|---|---|---|---|
| CT-01 | Target steady-state AWS spend of about $25,000 to $35,000 per month | §10 | Needs approval |
| CT-02 | Three migration waves over 12 months | §10 | Confirmed |
| CT-03 | No payroll changes during quarter-end and tax periods | §10 | Confirmed |
| CT-04 | IT has 14 people and no AWS experts, so the design must stay simple and fully documented for handover | §10 | Confirmed |
| CT-05 | Spending approval for Direct Connect has an owner who is not yet named | §12 | Open |

## 9. Deliverables requested by the client

| ID | Deliverable | Source |
|---|---|---|
| DL-01 | Architecture diagrams | §11 |
| DL-02 | Workload placement table | §11 |
| DL-03 | Account structure with access matrix | §11 |
| DL-04 | Connectivity design | §11 |
| DL-05 | Identity design | §11 |
| DL-06 | Security and logging design | §11 |
| DL-07 | Backup and DR design | §11 |
| DL-08 | Decision records | §11 |
| DL-09 | Risk register | §11 |

The detailed network layer (IP plan, route tables, security rules) is out of scope for this phase and follows the architecture.

## 10. Assumptions

| ID | Assumption | Status |
|---|---|---|
| AX-01 | A New York-area Direct Connect location is available to both data centers | Proposed |
| AX-02 | us-east-1 is the primary region and us-east-2 is the DR region | Proposed |
| AX-03 | The scheduling vendor's software can run on AWS | Proposed |
| AX-04 | The existing AD forest is healthy and its domain controllers can be reached over the private link | Proposed |
| AX-05 | Sixty virtual servers is the full scope of the migration inventory | Proposed |

## 11. Open questions for the client

| ID | Question | Why it matters |
|---|---|---|
| OQ-01 | Should payroll and HR move to AWS at all? | Decides Direct Connect sizing and the wave plan |
| OQ-02 | Should AD run on AWS or stay on-prem only? | Decides the identity design and the failure behavior |
| OQ-03 | Should DR cover everything or only the critical tier? | Decides cost and the second-region design |
| OQ-04 | Who approves Direct Connect spending? | Blocks the connectivity order |
| OQ-05 | Is $25,000 to $35,000 per month realistic for this scope? | Decides the sizing and service choices |
| OQ-06 | How much bandwidth will the migration waves need beyond steady state? | Decides whether one link is enough during migration |
| OQ-07 | What is the AD forest version and the domain functional level? | Affects directory integration options |

## 12. Traceability to the architecture

| Architecture area | Requirements |
|---|---|
| Workload placement and migration waves | BR-03, BR-04, WP-01 to WP-11, CT-02, CT-03 |
| Account structure | AS-01 to AS-06, SC-08 |
| Hybrid connectivity | CR-01 to CR-07, AR-01, AR-09 |
| Identity | IR-01 to IR-07, CR-07 |
| Security and logging | SC-01 to SC-09, AS-02 |
| Backup and DR | AR-01 to AR-11, AS-03 |
| Cost and handover | CT-01, CT-04, CT-05 |



