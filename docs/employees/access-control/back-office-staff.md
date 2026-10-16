## Back Office Staff contains a group of team: 
- HR and Recruiting
- Compliance Team
- Payroll
- Finance and Billing
- Scheduling and Dispatch
- Reporting
- Legal, Risk and Insurance
- Client Account Management


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



How the Access should be controlled?
What kind of Access does they need?
What we should used to authorized that Access?



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


