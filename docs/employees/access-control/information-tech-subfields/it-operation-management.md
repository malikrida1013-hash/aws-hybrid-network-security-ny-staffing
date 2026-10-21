##  *Here is the workloads associated with the IT-Operation-Management Field!*


<p align="center">
  <img src="it-operation-manager-workload.png.png" alt="Workload flow Chart" width="900">
</p>


The current scope of IT-Operation Management Field where mostly its workload and data lives is on On-Premsie \
and the owner wants to migrate it to the AWS now!


### *ACCESS PROFILES / PERMISSION SETS:*


| Field | Details | 
| --- | --- | 
| Team | IT Operations Management (part of IT Administration) |
| Purpose | Keep servers and platforms running, monitored, backed up, patched without having access to business data. |
| Application & Level | Hypervisor console (operator), monitoring platform (full), backup software (operator), patch management (operator), ticketing/helpdesk (full), change calendar (full), SIEM (read-only), network monitoring (read-only) |
| Active Directory | Delegated tasks only: password resets, account unlocks, assigned group membership. No Domain Admin. |
| File Shares | 	IT and operations folders only (runbooks, SOPs, change records). No business file shares. |
| Database Access | 	None to business databases (payroll, HR, background checks). Database servers can be restarted and backed up, but not queried. |
| AWS Permission Sets | ITOps-Operator: EC2 reboot/stop/start, Systems Manager (Session Manager, Patch Manager, Run Command), CloudWatch, SNS, AWS Backup, read-only CloudTrail |
| AWS Account Scope | Monitoring/shared services account (read, dashboards, alarms). Operator role in production and dev. Monitoring view in client-facing, corporate, and network accounts. |
| Explicitly Denied | Business databases and file shares, Domain Admin, network and firewall changes, security log administration, IAM changes, billing, security/log archive account, management account |
| Network Path | 	Management segment, through the jump host (on-prem) and Session Manager (AWS). No direct access from regular desktops. |
| Authentication | 	Separate admin account (for example adm-username) with MFA. AWS sign-in through IAM Identity Center with AD credentials. |
| Privileged Access | Break-glass account for emergencies: time-limited, logged, and alerted when used |
| Scheduling Inputs | Blackout windows from the Operations Manager, recorded in the change calendar. No access to the scheduling and dispatch application. |
| Logging & Audit | Privileged sessions logged on-prem and in AWS (CloudTrail, Session Manager logs) | 
| Status | Pending, requires client informaion | 



