# *Here is the material that devops will use to deploy security and network configuration into AWS or On-premise!*

### *Back-Office Staff:*
1) HR & Recruiting
2) Compliance
3) Payrolls
4) Finance & Billing
5) Scheduling & Dispatch
6) Client Account Management
7) Reporting
8) Legal, Risk & Insurance

### *Permission Sets/Access Profiles:*

Each System is associated with different groups that are differentiated to their specific tasks or workloads so their permission sets/access profiles are also different.

# *Access Profiles Of HR & Recruiting:*

## Access Requirements of HR & Recruiting:

**A-01** — Recruiters access candidate data only, with no access to current employee records

**A-02** — ID documents and tax forms are accessible only to Onboarding

**A-03** — Access to new-hire documents is limited after onboarding is complete

**A-04** — Generalists can view attendance but cannot view pay data

**A-05** — Employee Relations see only assignment history and complaint history

**A-06** — The HR Manager approves hiring, leave, and offboarding and runs HR reports

**A-07** — The person who enters a record cannot also approve it (separation of duties)

**A-08** — Background-check results are visible only to those in the screening process, owned by Compliance

**A-09** — HR has view-only access to pay stubs for employee inquiries (requires client approval)

**A-10** — Access is through the HR application, with no direct database access

**A-11** — Field employees have no access to HR data

**A-12** — Access is reviewed periodically, and old access is removed when people change roles


| Field | Recruiters | Verification & On-boarding | HR Manager | Performance & Attendance | Employee Relation |
| --- | --- | --- | --- | --- | --- | 
| Sub-group / AD group | HR-Recruiters | HR-Onboarding | HR-Manager | HR-Generalist | HR-EmployeeRelation | 
| Applications and level | Recruiting module: View/Edit; email and calendar	| HR system (new-hire records): View/Edit; document verification: Edit/Approve at Lead level; email	| HR system: View across HR, Approve (hiring, leave, offboarding); HR reports: Run |	HR system (records, leaves, attendance, performance, offboarding): View/Edit; email | Case management: View/Edit; assignment history: View; email |
| File shares    | Candidate documents folder: Read/Write | New-hire documents folder: Read/Write (access limited once onboarding is complete, A-03) |	HR shared folders: Read; approvals and reports folder: Read/Write	|Employee records, attendance, and leave folders: Read/Write |	Case notes folder: Read/Write |
| Database access    | Application-only; no direct access (A-10) |	Application-only; no direct access (A-10) |	Application-only; no direct access (A-10) |	Application-only; no direct access (A-10)	| Application-only; no direct access (A-10) | 
| Explicitly denied    | Current employee records, ID documents, pay data, screening results (A-01, A-08) |	Pay data, performance and attendance records, screening results (A-08) |	Direct database access, approving own entries (A-07), changing pay data (C-07), screening results (A-08) |	Pay data (A-04), ID documents, screening results (A-08) |	Pay data, ID documents, performance reviews, full employee records, screening results (A-05, A-08) |
| Network path | Corporate LAN; VPN with MFA for remote work | Corporate LAN; VPN with MFA for remote work | Corporate LAN; VPN with MFA for remote work | Corporate LAN; VPN with MFA for remote work |	Corporate LAN; VPN with MFA for remote work |
| Authentication | AD SSO; MFA for remote access |	AD SSO; MFA always (restricted data) | AD SSO; MFA always, strictest rules | AD SSO; MFA for remote access and restricted records |	AD SSO; MFA for remote access |
| Status | Proposed | Proposed | Proposed |Proposed | Proposed |Proposed | 

### *Access Profiles Of Compliance:*

## Access Requirements of Compliance:

**CC-01** — Screening results are visible only to staff who run or review background checks

**CC-02** — HR and Scheduling receive only a pass/fail or cleared/not-cleared status, never the screening details

**CC-03** — Licensing and Compliance Tracking staff manage license and certification records and expiry dates, and cannot view screening results

**CC-04** — Scheduling and Dispatch can view only whether a person is cleared to work, with no reasons given

**CC-05** — The person who ran a screening cannot also approve its result (separation of duties)

**CC-06** — The Compliance Manager approves screening decisions and exceptions

**CC-07** — Every access to a screening result is logged for audit

**CC-08** — Access is through the compliance application, with no direct database access

**CC-09** — Screening data is retained only for the legally required period and then removed (retention period requires client approval)

**CC-10** — Compliance staff cannot view or change pay data or general employee records

**CC-11** — Access to screening results is limited to named individuals and reviewed periodically (review frequency requires client approval)

| Field | Screening | Tracking | Manager |
| --- | --- | --- | --- | 
| Sub-group / AD group | COMP-Screening | COMP-Tracking & Verification | COMP-Manager |
| Applications and level | 	Screening portal: View/Edit; compliance application (screening cases): View/Edit; email |	Compliance application (licenses and certifications): View/Edit; expiry alerts: View; email	| Screening portal: View across cases, Approve (decisions and exceptions); compliance application: View, Approve; audit reports: Run |
| File shares    | Screening documents folder: Read/Write |	License and certification records folder: Read/Write |	Compliance shared folders: Read; approvals and audit folder: Read/Write | 
| Database access    | Application-only; no direct access (CC-08)	| Application-only; no direct access (CC-08) |	Application-only; no direct access (CC-08) |
| Explicitly denied    | Pay data, general employee records, approving own screenings (CC-05, CC-10)	| Screening results (CC-03), pay data, general employee records (CC-10)	| Direct database access, approving own screenings (CC-05, CC-08), pay data (CC-10)| 
| Network path | 	Corporate LAN; VPN with MFA for remote work |	Corporate LAN; VPN with MFA for remote work |	Corporate LAN; VPN with MFA for remote work |
| Authentication | AD SSO; MFA always (restricted data), named individuals only |	AD SSO; MFA for remote access	| AD SSO; MFA always, strictest rules | 
| Status | Proposed | Proposed | Proposed |


### *Access Profiles Of Payrolls:*

**PR-01** — Only Payroll staff can create or change pay data (rates, deductions, adjustments)

**PR-02** — Payroll Processing prepares payroll runs and issues pay stubs

**PR-03** — The person who prepared a payroll run cannot also approve it (separation of duties)

**PR-04** — Payroll Audit and Approval reviews and approves payroll runs, corrections, and one-off payments

**PR-05** — Payroll can read hours from the clock-in system but cannot change them

**PR-06** — Field employees can view only their own pay stubs

**PR-07** — HR has view-only access to pay stubs for employee inquiries (requires client approval)

**PR-08** — Finance and Billing receive payroll totals only, with no access to individual pay details

**PR-09** — Supervisors and Scheduling and Dispatch cannot view pay rates

**PR-10** — Bank details and tax information are restricted to Payroll staff who process pay

**PR-11** — Every change to pay data and every payroll run is logged for audit

**PR-12** — Access is through the payroll application, with no direct database access

**PR-13** — Payroll records are retained for the legally required period (retention period requires client approval)

**PR-14** — Payroll access is limited to named individuals and reviewed periodically (review frequency requires client approval)


## Access Requirements of Payrolls:

| Field | Payroll Processing | Payroll Audit & Approval | 
| --- | --- | --- | 
| Sub-Group / AD group | PR-Processing | PR-Audit & Approval | 
| Application and Level | Payroll application: View/Edit (rates, deductions, runs, pay stubs); clock-in system (hours): View; email |	Payroll application: View across runs, Approve (runs, corrections, one-off payments); audit reports: Run; clock-in system (hours): View |
| File shares | Payroll working files folder: Read/Write	| Payroll approvals and audit folder: Read/Write; payroll working files: Read |
| Database Access | Application-only; no direct access (PR-12) |	Application-only; no direct access (PR-12) |
| Explicitly denied |	Approving own payroll runs (PR-03), changing clock-in hours (PR-05), screening results, HR case notes |	Preparing runs they approve (PR-03), changing clock-in hours (PR-05), direct database access (PR-12) | 
| Network path | Corporate LAN; VPN with MFA for remote work |	Corporate LAN; VPN with MFA for remote work |
| Authentication | AD SSO; MFA always (restricted data), named individuals only |	AD SSO; MFA always, strictest rules |
| Status | Proposed | Proposed |












| Field | Payroll Processing | Payroll Audit & Approval | 
| Sub-Group / AD group | 
| Application and Level | 
| File shares | 
| Database Access | 
| Explicitly denied | 
| Network path | 
| Authentication | 
| Status |


