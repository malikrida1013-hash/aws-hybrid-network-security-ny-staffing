# External Party Access Profile: Scheduling and Time and Attendance Software Vendor

## 1. Who they are
The company that makes and supports the scheduling and time and attendance system the client uses to assign staff to buildings and record their hours. When the system has a problem, such as a failed service, a bad upgrade, or a data error, the vendor's support team helps fix it. They are not employees and they are not running the client's platform, so their access is **on request, limited to their own system, and time-limited**.

## 2. Access Profile

| Field | Details |
|---|---|
| **Party** | Scheduling and time and attendance software vendor |
| **Purpose** | Support, troubleshoot, and upgrade the vendor's application hosted in the client's AWS environment, without access to other systems or to business data by default |
| **Sponsors** | Technical sponsor: a named IT employee (for example the head of IT). Business owner of the system: the Operations Manager. Both approve access requests. |
| **Contract period** | Fixed term set by the support contract. Access ends on the contract end date, and renewal needs a new approval. |
| **Identity** | Named individual guest identities in IAM Identity Center, or federation from the vendor's own identity system, with MFA. No shared support accounts. |
| **AD / Identity Center group** | `External-Vendor-Support` |
| **AWS Permission Set** | `External-Support` |
| **Access model** | **On request, not standing.** Access is turned on for a specific ticket and expires automatically when the issue closes, with a default maximum of one business day, extendable by approval. |
| **AWS account scope** | Production and test or dev: only resources tagged for the scheduling and time and attendance system. No access to any other resources, and no access to the network, security/log archive, backup, management, or billing accounts. |
| **Allowed in AWS** | View CloudWatch dashboards and logs for the tagged system. Open a Session Manager session on tagged instances (time-limited, logged, and recorded). Restart the application services and reboot tagged instances. Read-only CloudTrail. |
| **Upgrades and patches** | The vendor supplies the update. It is tested first in the test environment, then applied to production by System and Server Administration, or by the vendor under a change ticket. Production upgrades avoid the blackout windows set by the Operations Manager (shift start and end times, when staff clock in and out). |
| **Database** | No direct access to the production database by default. If the vendor needs to inspect or repair data, a DBA performs the action, or Database Administration supervises the session through an approved ticket. |
| **Business data** | Not read by default. The system holds worker names, shift assignments, and hours. If the vendor needs to see data to solve a problem, it is an approved, time-limited, logged and supervised session, or the vendor works from a masked copy in the test environment. |
| **On-prem access** | Only if part of the system still runs on-prem during the migration. Through the VPN and jump host with a named account and MFA, limited to the vendor's servers, with the same time limits. |
| **Explicitly denied** | Access to any resource not tagged for their system, creating or terminating servers, IAM changes, KMS key administration, network and firewall changes, backup deletion or restore, security log changes, direct production database queries, reading business data by default, billing, Domain Admin |
| **Network path** | IAM Identity Center sign-in with MFA, and Session Manager for server access, with no inbound ports opened. On-prem: VPN to the jump host on the management segment. |
| **Authentication** | MFA required. No long-lived access keys. |
| **Request flow** | The client opens a ticket → the vendor asks for access → approval by the technical sponsor and the business owner → time-limited access turned on → automatic removal when the ticket closes |
| **Vendor requirements** | Signed NDA and data processing agreement, background-checked support staff, MFA on their side, incident reporting to the client, disclosure of subcontractors, notice when a support engineer leaves, and an independent security report (such as SOC 2) if available |
| **Logging and audit** | CloudTrail, Session Manager session logs and recordings, Systems Manager history, and tickets. SIEM alerts on every vendor session. |
| **Review** | Security and Compliance review vendor sessions regularly. The client reviews the contract, access, and the vendor's security practices each year. |
| **Offboarding** | When a support engineer leaves the vendor, the vendor notifies the client and the identity is removed. When the contract ends, the group, identities, and any roles are deleted. The sponsor confirms in the ticket, and Compliance verifies at the next review. |
| **Status** | Draft, pending confirmation of how the system is hosted (self-hosted in AWS or vendor-hosted), the support contract end date, and the sponsors |

## 3. Access Flow

```
Support contract approved (NDA + data processing agreement + end date)
        |
Vendor staff added as named identities in External-Vendor-Support (no standing permissions)
        |
Problem occurs → client opens a ticket
        |
Vendor requests access → technical sponsor + business owner approve
        |
Time-limited assignment of External-Support, scoped to resources tagged for the system
        |
Vendor works through Session Manager (logged and recorded) → fix applied or escalated
        |
Ticket closes → access removed automatically → alert and review
```

**Upgrade path:**
```
Vendor releases an update → tested in the test environment → change ticket
→ scheduled outside the blackout windows → applied by System and Server Administration (or the vendor, supervised)
→ confirmed by the Operations Manager → recorded in the change calendar
```

**Data access (exception):**
```
Vendor needs to see data → approved ticket → time limit → supervised session or masked copy
→ access removed on expiry → SIEM alert and audit entry
```

## 4. Permission Set: External-Support

**Allowed:**
- CloudWatch dashboards and logs for resources tagged for the scheduling system
- Session Manager sessions on tagged instances (time-limited, logged, recorded)
- Restart of application services and reboot of tagged EC2 instances
- Systems Manager Run Command for the vendor's approved support documents
- Read-only CloudTrail

**Denied:**
- Any resource not tagged for the scheduling system
- Launching, modifying, or terminating servers
- IAM changes, KMS key administration
- Network changes (VPC, routing, security groups, VPN, Direct Connect)
- Backup deletion or restore
- Security and log archive changes
- Direct queries to the production database and reading business data (S3 objects, database contents)
- Billing and the management account

## 5. Security Controls
- Named individual identities with MFA, and no shared support accounts
- **On request, not standing:** access exists only for an approved ticket and ends automatically
- Scope limited by resource tags, so the vendor can reach only its own system
- Session Manager with no inbound ports, with session logs and recordings
- Production database and business data reached only through the DBA path or an approved, supervised session
- Upgrades tested first, scheduled outside blackout windows, and recorded in the change calendar
- SIEM alert on every vendor session
- Vendor contract requires an NDA, a data processing agreement, background-checked staff, incident reporting, subcontractor disclosure, and notice of leavers
- Regular access review by Security and Compliance, and an annual review by the client
- Separation of duties: the vendor supports its own software, internal teams run the platform, Database Administration owns the database, Security Administration owns identity, keys, and logs

## 6. Requirements Traceability

| Requirement | Met by |
|---|---|
| External access is limited to a defined purpose | Tag-based scope to the scheduling system only |
| Someone inside the company is responsible | Technical sponsor and business owner |
| Access is only open when needed | On-request assignment, automatic expiry |
| No shared accounts | Named identities with MFA |
| Vendor cannot read business data by default | No data permissions. Data reached only through an approved, supervised path. |
| Vendor cannot disrupt the business | Upgrades through change tickets, outside blackout windows |
| Vendor cannot weaken security | No IAM, KMS, network, backup, or log permissions |
| Activity is traceable | CloudTrail, Session Manager logs and recordings, SIEM, tickets |
| Access ends cleanly | Auto-expiry, vendor notification of leavers, contract-end removal, Compliance verification |

## 7. Assumptions and Open Items
- How the system is hosted: **self-hosted in AWS** (assumed in this profile) or **vendor-hosted**. If vendor-hosted, this party needs no AWS access and moves to the `no-aws-access-parties.md` file, with only a data integration to document.
- Support contract end date, named sponsors, and default access duration to be confirmed
- Whether any part of the system still runs on-prem during migration to be confirmed
- Blackout windows to be provided by the Operations Manager and recorded in the change calendar
- Vendor's identity system (guest accounts or federation) to be confirmed
- Exact IAM policy actions and tag conditions for `External-Support` to be documented in the build guide
