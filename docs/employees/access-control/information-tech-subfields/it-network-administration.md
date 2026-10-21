##  *Here is the workloads associated with the IT-Network Administration Field!*


<p align="center">
  <img src="network-administration.png.png" alt="Workload flow Chart" width="900">
</p>


### *ACCESS PROFILES / PERMISSION SETS:*

| Field | Details | 
| --- | --- | 
| Team | Network Administration (IT Administration) |
| Purpose |	Design, build, and maintain the network that connects the New York data centers, offices, and AWS, without access to servers or business data |
| Applications and level |	Network device management tools (administrator), network monitoring (full), firewall management console (administrator), ticketing and change calendar (full), monitoring platform (read-only), SIEM (read-only) |
| Network devices (on-prem) |	Administrator rights on switches, routers, firewalls, VPN gateways, and wireless controllers: configure, upgrade, and troubleshoot. Every configuration change is recorded in the change calendar. |
| Servers and databases (on-prem) |	No administrator rights. Read-only views of server and monitoring status only. |
| Active Directory |	Delegated tasks only: membership of their own network groups. No Domain Admin. |
| File shares |	IT and network folders only (network diagrams, IP plan, route tables, firewall rule records, runbooks). No business file shares. |
| DNS and DHCP | Manage on-prem  DNS and DHCP services and Route 53 resolver rules. Route 53 records for company zones only if the client’s brief assigns them. |
| AWS Permission Set |	NetAdmin-Builder: VPC, subnets, route tables, internet and NAT gateways, Transit Gateway, Direct Connect, Site-to-Site VPN, security groups, network ACLs, VPC Flow Logs, Reachability Analyzer, CloudWatch, read-only CloudTrail |
| AWS account scope |	Network account: full builder rights. Dev: full network rights. Production, client-facing, and corporate: same permission set, with changes only through the change process. Shared services: resolver and connectivity tooling. No access to security/log archive, management, or billing accounts. |
| Data access |	None to business data. They manage how traffic flows, not what it contains. | 
| Explicitly denied	| IAM changes, KMS key administration, business data, server and database administration (EC2 administration, RDS, S3 object reads), security log administration, billing, Domain Admin |
| Network path | 	Management segment through the jump host (on-prem) and IAM Identity Center sign-in to the AWS console (AWS) |
| Authentication |	Separate admin account with MFA. AWS sign-in through IAM Identity Center with AD credentials. | 
| Privileged access	| Break-glass account: time-limited, logged, and alerted when used |
| Change control | Firewall, routing, and Direct Connect/VPN changes require an approved ticket, with emergency changes reviewed afterward |
| Logging and audit |	Device configuration changes logged on-prem and sent to the SIEM, CloudTrail and VPC Flow Logs in AWS | 
| Status | Draft, pending client confirmation of the workload list and of the split between Network and Security Administration | 






