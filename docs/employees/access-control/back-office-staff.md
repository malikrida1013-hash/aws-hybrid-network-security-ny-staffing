## Back Office Staff contains a group of team: 
- HR and Recruiting
- Compliance Team
- Payroll
- Finance and Billing
- Scheduling and Dispatch
- Reporting
- Legal, Risk and Insurance
- Client Account Management


*ACCESS TABLE;*

<p align="center">
  <img src="access-table.png.png" alt="Access table" width="900">
</p>



## *Workloads:* 
|  | HR and Recruiting | Compliance Team |
|---|---|---|
| **Workloads** | Hiring posting<br>Documents collection<br>Interviewing<br>Background Checks Verification<br>Selection<br>Onboarding<br>Records management<br>Receiving request<br>Relation b/w employee and clients<br>Scheduling and Dispatch Review<br>Performance and attendance<br>Off-boarding<br>Reporting team | Receiving HR data<br>Verifying eligibility wrt work<br>Review external background checks<br>Matching with eligibility criteria<br>Update the status<br>Verifying license and certification<br>Auditing Preparation<br>Incident solving wrt Compliance rules<br>Transfer high incidents to legal, law and insurance team |
| **Current Scope** | On-Premise | On-Premise |
| **Future Scope** | On-Premise | On-Premise |
| **Scope Of Access** | Business-level Access only | Business-level Access only |
| **In-system Access** | Compliance Team<br>Scheduling Team<br>Payrolls<br>Client Account Management Team<br>Reporting Team | HR Team<br>Reporting Team for dashboard<br>Payrolls and Finance (if required)<br>Legal, risk and Insurance<br>Client Account Management<br>Scheduling |
| **External Access** | External Background Checks | Regulators and Auditors |
| **Internal Access** | Field-Employees Website Access | --- |
| **Security Impact on Business** | Data breaching of employees | Weak audit trail<br>Loss of clients<br>Legal and financial<br>Physical and Data Security | 



> How the Access should be controlled?


> What kind of Access does they need?


> What we should used to authorized that Access?




| | Payrolls | Finance and Billing |
| --- | --- | --- |
| **Workloads** | Receives HR data(Who is employee , how much they should paid)<br>Receives Time and attendance<br>Calculate Gross Pay<br>Pay taxes<br>Review and approved<br>External Payment Processor/Bank for payment<br>Pay stubs<br>Reporting<br>Stop Pays when off-boarding| Receives Inputs(Client Contracts, Hours billed, labour cost), generate invoices , manages company money, pay company bills, produce financial reports |
| **Current Scope** | On-premise | On-premise |
| **Future Scope** | On-premise | On-premise |
| **Scope Of Access** | Business-level Access only | Business-level Access only|
| **In-system Access** | HR team<br>Scheduling and Time team<br>Reporting<br>Finance and biling | Payrolls<br>Client Account Management<br>Reporting<br>Scheduling<br>Legal,Risk and Insurance |
| **External Access** | External Payment Provider<br>Tax Authorities | Banks<br>Vendors and suppliers<br>Auditors and external accountants<br>Tax authorities<br>Insurers |
| **Internal Access** | --- | --- |
| **Security Impact on Business** | Fraud and financial loss, employee data breach, legal and tax penalties, loss of staff trust | Revenue generating mishaps, company loss | \



**Fetched Data:**


**Transferd Data:**


| | Client Account Management | Scheduling Team |
| --- | --- | --- |
| **Workloads** | Client queries<br>Client Account Setup<br>Record Contracts<br>Client Staffing Requirements<br>Renew contracts or end contracts  | Receive client requirements<br>receive employee information<br>receive eligibility from compliance<br>Scheduled the employee<br>Capture Clock-in,Clock-out<br>Approve HOURS<br>Send approved hours to Payroll and finance and billing<br>Send attendance to HR |
| **Current Scope** | On-premise | On-premise |
| **Future Scope** | AWS | AWS |
| **Scope Of Access** | Business-level Access only | Business level Access + AWS level Access |
| **In-system Access** | HR team<br>Compliance Team<br>Scheduling Team<br>Reporting Team<br>Finance and Billing Team<br>Legal, risk Insurance team | Field Employee App<br>HR team<br>Compliance Team<br>Payrolls<br>Finance team<br>Reporting team |
| **External Access** | Clients | --- |
| **Internal Access** | --- | Field Employee App |
| **Security Impact on Business** | Client Security Service | |  |

**Fetched Data:**


**Transferd Data:**


| | Reporting Team | Legal,Risk and Insurance team |
| --- | --- | --- |
| **Workloads** | Collects In-system Data<br>Prepared Data<br>Make Dashboards & Reports | Review contract and policies<br>Maintain risk and insurance<br>Manage incidents and disputes<br>Oversight |
| **Current Scope** | On-premise | On-premise |
| **Future Scope** | AWS | On-premise |
| **Scope Of Access** | Business-level Access only | Business level Access  |
| **In-system Access** | HR team<br>Compliance Team<br>Scheduling Team<br>Payrolls<br>Finance<br>Legal Risk and Insurance team<br>Client Account Management | Finance Team<br> Compliance team<br>HR team<br>Client Account Management<br>Reporting Team |
| **External Access** | Auditors, Executive, | Legal Consultant and Insurance |
| **Internal Access** | --- | --- |




### *Permission Sets/Access Profiles:*

Each System is associated with different groups that are differentiated to their specific tasks or workloads so their permission sets/access profiles are also different.


### *Access Requirements of HR & Recruiting:*

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
### *Access Profiles Of HR & Recruiting:*

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


### *Access Requirements of Compliance:*

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


 ### *Access Profiles Of Compliance:*

 
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


### *Access Requirements Of Payrolls:*

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


### *Access Profiles of Payrolls:*

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



### *Access Requirements Of Finance & Billing:*


**FB-01** — Only Finance and Billing staff can create or change invoices, payments, and ledger entries

**FB-02** — Billing and Invoicing staff access client rates, billable hours, and invoice history

**FB-03** — Finance staff can read billable hours from the clock-in system but cannot change them

**FB-04** — Finance staff can view client contract rates and payment terms but cannot change contracts (owned by Client Account Management)

**FB-05** — Finance and Billing receive payroll totals only, with no access to individual pay details or employee personal records

**FB-06** — The person who enters a vendor bill or payment request cannot also approve it (separation of duties)

**FB-07** — The Finance Manager approves invoice adjustments, credit notes, write-offs, and vendor payments above the approval limit (limit requires client approval)

**FB-08** — Vendor and client bank details are restricted to Accounts Receivable and Payable staff who process payments

**FB-09** — Reporting and Legal have view-only access to approved financial data (requires client approval)

**FB-10** — Every change to an invoice, payment, or ledger entry is logged for audit

**FB-11** — Access is through the finance application, with no direct database access

**FB-12** — Financial records are retained for the legally required period (retention period requires client approval)

**FB-13** — Finance access is limited to named individuals and reviewed periodically (review frequency requires client approval)





### *Access Profiles Of Finance & Billing:*

| Field | Billing & Invoicing | Accounts Receivable & Payable |	Finance Manager |
| --- | --- | --- | --- | 
| Sub-Group / AD group |  FIN-Billing | FIN-Paying | FIN-Manager |
| Application and Level | Finance application (invoices, client rates): View/Edit; clock-in system (billable hours): View; client contracts (rates and terms): View; email |	Finance application (ledger, payments, vendor bills): View/Edit; bank payment portal: View/Edit at Lead level; email	| Finance application: View across all, Approve (adjustments, credit notes, write-offs, payments above limit); financial reports: Run |
| File shares | Invoices and billing folder: Read/Write |	Payments and ledger folder: Read/Write	| Finance shared folders: Read; approvals folder: Read/Write |
| Database Access | Application-only; no direct access (FB-11)	| Application-only; no direct access (FB-11)	| Application-only; no direct access (FB-11) |
| Explicitly denied | 	Individual pay data, employee personal records (FB-05), changing clock-in hours (FB-03), changing contracts (FB-04) |	Individual pay data, employee personal records (FB-05), approving own entries (FB-06), changing contracts (FB-04) |	Direct database access (FB-11), approving own entries (FB-06), individual pay data (FB-05) |
| Network path | Corporate LAN; VPN with MFA for remote work	| Corporate LAN; VPN with MFA for remote work |	Corporate LAN; VPN with MFA for remote work |
| Authentication | 	AD SSO; MFA for remote access	| AD SSO; MFA always (bank details), named individuals only |	AD SSO; MFA always, strictest rules |
| Status | Proposed	| Proposed; bank portal access requires client approval	| Proposed; approval limit requires client approval |


### *Access Requirements Of Scheduling & Dispatch:*

SD-01 - Scheduling and Dispatch staff assign employees to buildings and shifts, and supervisors do not

**SD-02** — Scheduling and Dispatch can view only whether a person is cleared to work, with no reasons given

**SD-03**  — Scheduling and Dispatch cannot view pay rates, pay data, or screening details

**SD-04** — Scheduling and Dispatch can read clock-in hours for the buildings they schedule and cannot change a punch

**SD-05** — Scheduling and Dispatch approve time-off requests, shift swaps, and missed-punch corrections (approver requires client approval)

**SD-06** — The person who requests a shift change or correction cannot also approve it (separation of duties)

**SD-07 - Scheduling and Dispatch can view building requirements and client coverage terms, and cannot change contracts (owned by Client Account Management)

**SD-08** — Scheduling and Dispatch can view an employee’s name, role, license status, and availability, and cannot view HR records or personal documents

**SD-09** — Every assignment, schedule change, and approved correction is logged for audit

**SD-10** — Access is through the scheduling application, with no direct database access

**SD-11** — Scheduling access is limited to named individuals and reviewed periodically (review frequency requires client approval)

### *Access Profiles Of Scheduling & Dispatch:*


| Field | Scheduling & Dispatch Staff | Scheduling & Dispatch Manager | 
| --- | --- | --- | --- | 
| Sub Group / AD-Group | SD-Staff | SD-Manager |
| Application & Level | Scheduling application (assignments, shifts, availability): View/Edit; clock-in system (hours, buildings they schedule): View; cleared-to-work status: View; client coverage terms: View; employee directory (name, role, license status): View; email	| Scheduling application: View across all, Approve (time-off, shift swaps, missed-punch corrections); schedule reports: Run; clock-in system (hours): View; cleared-to-work status: View |
| File Shares | Scheduling working files folder: Read/Write	| Scheduling shared folders: Read; approvals and reports folder: Read/Write |
| Database Access | Application-only; no direct access (SD-10)	| Application-only; no direct access (SD-10) | 
| Explicitly Denied | Pay rates and pay data (SD-03), screening details and reasons (SD-02, SD-03), HR records and personal documents (SD-08), changing clock-in hours (SD-04), changing contracts (SD-07), approving own requests (SD-06) |	Direct database access (SD-10), approving own requests (SD-06), pay rates and pay data (SD-03), screening details (SD-02), changing clock-in hours (SD-04), changing contracts (SD-07) |
| Network path	| Corporate LAN; VPN with MFA for remote work |	Corporate LAN; VPN with MFA for remote work |
| Authentication |	AD SSO; MFA for remote access, named individuals only (SD-11)	| AD SSO; MFA always, strictest rules |
| Status |	Proposed	| Proposed; approver for time-off, swaps, and corrections requires client approval (SD-05) |


### *Access Requirements Of Reporting:*

**RP-01** - Reporting staff build and run reports from approved, read-only data, and cannot create or change records in any source system

**RP-02** - Reporting receives aggregated payroll totals only, with no access to individual pay details (aligned with PR-08 and FB-05)

**RP-03** - Reporting has view-only access to approved financial data (requires client approval, aligned with FB-09)

**RP-04** - Reporting can view cleared or not-cleared counts only, and cannot view screening details or reasons (aligned with CC-02)

**RP-05** - Reporting can view workforce data (headcount, attendance and hours totals, assignments by building) without names, ID documents, or personal records

**RP-06** - Reports containing individual employee data require approval from the data owner before they are created or shared (data owner approval process requires client approval)

**RP-07** - The person who builds a report cannot also approve it for distribution (separation of duties)

**RP-08** - The Reporting Manager approves new reports, changes to report definitions, and distribution lists

**RP-09** - Every report run, export, and change to a report definition is logged for audit

**RP-10** - Access is through the reporting application, with no direct database access to source systems

**RP-11** - Reporting access is limited to named individuals and reviewed periodically (review frequency requires client approval)



### *Access Profiles Of Reporting:*



| Field | Reporting Analyst | Reporting Manager | 
| --- | --- | --- | 
| Sub-Group / AD-Group | RPT-Analyst | RPT-Manager | 
| Application & Level | Reporting application (approved datasets, report builder): View/Edit; dashboards: View/Edit; email	| Reporting application: View across all, Approve (new reports, report changes, distribution lists); audit and usage reports: Run | 
| File Shares | Reporting working files folder: Read/Write |	Reporting shared folders: Read; approvals and distribution folder: Read/Write |
| Database Access |	Application-only; no direct access (RP-10)	| Application-only; no direct access (RP-10) |
| Explicitly Denied | Individual pay data (RP-02), screening details (RP-04), HR personal records and ID documents (RP-05), creating or changing source records (RP-01), approving own reports (RP-07)	| Direct database access (RP-10), approving own reports (RP-07), individual pay data (RP-02), screening details (RP-04), changing source records (RP-01) |
| Authentication | AD SSO; MFA for remote access, named individuals only (RP-11) |	AD SSO; MFA always, strictest rules |
| Network Path | Corporate LAN; VPN with MFA for remote work | Corporate LAN; VPN with MFA for remote work | 
| Status | 	Proposed | 	Proposed; financial data access and individual-data approval process require client approval (RP-03, RP-06) | 

### *Access Requirements Of Legal, Risk & Insurance:*


**LR-01** - Legal staff manage legal matters, claims files, and insurance policy records, and cannot create or change records in any source system

**LR-02** - Legal and Risk have view-only access to approved financial data (requires client approval, aligned with FB-09)

**LR-03** - Legal can view client contracts for review, and cannot change them (owned by Client Account Management, aligned with FB-04)

**LR-04** - Legal and Risk can view cleared or not-cleared status only, and cannot view screening details or reasons (aligned with CC-02)

**LR-05** - Legal and Risk can view incident reports and complaint history for open claims or legal matters, and cannot view pay data or general employee records

**LR-06** - Access to an individual employee’s records for a legal matter requires approval from the Legal Manager and the data owner (approval process requires client approval)

**LR-07** - Risk and Insurance staff manage insurance policies, certificates of insurance, and claims records

**LR-08** - The person who opens or prepares a claim or settlement cannot also approve it (separation of duties)

**LR-09** - The Legal Manager approves settlements, legal holds, and external counsel engagements above the approval limit (limit requires client approval)

**LR-10** - Legal holds suspend deletion of the records they cover, and records are released only after Legal Manager approval

**LR-11** - Every access to a legal matter, claim, or contract is logged for audit

**LR-12** - Access is through the legal and risk application, with no direct database access

**LR-13** - Legal and insurance records are retained for the legally required period (retention period requires client approval)

**LR-14** - Legal and Risk access is limited to named individuals and reviewed periodically (review frequency requires client approval)



### *Access Profiles Of Legal, Risk & Insurance:*


| Field | Legal & Contracts	| Risk & Insurance |	Legal & Risk Manager |   
| --- | --- | --- | --- | 
| Sub-Group / AD-Group | LGL-Legal | LGL-Risk | LGL-Manager | 
| Application & Level | Legal and risk application (matters, claims files): View/Edit; client contracts (view only): View; incident and complaint history (open matters): View; cleared-to-work status: View; email | Legal and risk application (insurance policies, certificates, claims): View/Edit; incident reports (open claims): View; cleared-to-work status: View; approved financial data: View (requires client approval); email	| Legal and risk application: View across all, Approve (settlements, legal holds, counsel engagements above limit); audit reports: Run |
| File Shares | Legal matters folder: Read/Write |	Insurance and claims folder: Read/Write	| Legal and risk shared folders: Read; approvals and holds folder: Read/Write | 
| Database Access | Application-only; no direct access (LR-12) |	Application-only; no direct access (LR-12)	Application-only; no direct access (LR-12) | 
| Explicitly Denied | Pay data, general employee records (LR-05), screening details (LR-04), changing contracts (LR-03), approving own settlements (LR-08)	| Pay data, general employee records (LR-05), screening details (LR-04), changing contracts (LR-03), approving own claims (LR-08)	| Direct database access (LR-12), approving own settlements (LR-08), pay data (LR-05), screening details (LR-04) | 
| Authentication | AD SSO; MFA always (legal matters), named individuals only (LR-14) |	AD SSO; MFA for remote access, named individuals only (LR-14) |	AD SSO; MFA always, strictest rules | 
| Network Path | Corporate LAN; VPN with MFA for remote work | Corporate LAN; VPN with MFA for remote work | 
| Status | 	Proposed | 	Proposed | Proposed | 








