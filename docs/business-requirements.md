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


