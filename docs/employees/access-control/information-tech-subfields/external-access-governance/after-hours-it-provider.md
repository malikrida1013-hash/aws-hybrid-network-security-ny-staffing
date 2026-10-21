# External Party Access Profile: After-Hours IT Provider

## 1. Who they are
An outside company that watches the client's systems at night, on weekends, and on holidays, when the internal IT team is off. They triage alerts, follow written runbooks, perform a short list of approved fixes, and escalate to the on-call employee when something is beyond the runbook. They are not employees, so their access is narrow, runbook-based, and closely monitored.

## 2. Access Profile

| Field | Details |
|---|---|
| **Party** | After-hours IT provider (outsourced monitoring and first-response) |
| **Purpose** | Provide 24/7 first-line monitoring and approved fixes, without access to business data or the ability to make changes outside the runbooks |
| **Sponsor** | A named employee (for example the head of IT), responsible for the contract, the runbook list, and the annual review |
| **Contract period** | Fixed term set by the contract. Access ends on the contract end date, and renewal needs a new approval. |
| **Identity** | Each engineer signs in with a named identity from the provider's own identity system, with MFA enforced on their side. No shared accounts. Each session records the engineer's name. |
| **AWS access method** | Cross-account role in the client's AWS accounts, protected by an **external ID**, with the trust limited to the provider's AWS account |
| **AWS role / Permission Set** | `External-AfterHours-Operator` |
| **AWS account scope** | Production, client-facing, and corporate: only resources tagged for after-hours coverage. Shared services: view monitoring dashboards. No access to dev, backup, security/log archive, network, management, or billing accounts. |
| **Allowed in AWS** | View CloudWatch dashboards, logs, and alarms. Receive alerts through SNS. Reboot tagged EC2 instances. Run **pre-approved Systems Manager documents** only (a fixed list of runbook actions). Read-only CloudTrail. |
| **Not an open shell** | No interactive Session Manager sessions and no free-form commands. Only the approved runbook actions. |
| **On-prem access** | Named accounts through the VPN and jump host, with MFA, in the `External-AfterHours` group. Read-only monitoring, plus restart of services and servers on the covered list. |
| **Covered systems and actions** | A written runbook list of systems, alerts, and actions (for example restart the scheduling service, confirm the overnight backup finished). Anything not on the list is escalated. |
| **Escalation** | Call the on-call employee, then the client's emergency contacts. Every escalation is recorded in the ticket. |
| **Business data** | Never read. They watch systems, not payroll, HR, or background check content. |
| **Explicitly denied** | Interactive shell access, creating, modifying, stopping, or terminating servers, patching, installing software, IAM changes, KMS key administration, network and firewall changes, backup deletion or restore, security log changes, resetting administrator passwords or MFA, reading business data, billing, Domain Admin |
| **Network path** | AWS: cross-account role from the provider's account, limited to the provider's source IP ranges where possible. On-prem: VPN to the jump host on the management segment. |
| **Authentication** | MFA required on the provider's side and for on-prem accounts. No long-lived access keys. |
| **Standing access** | Narrow standing role, because 24/7 coverage needs it to be available. Every role assumption is alerted in the SIEM. |
| **Handover** | Written morning summary of alerts, actions, and open issues, delivered through the ticketing tool to IT Operations |
| **Provider requirements** | Signed NDA, background-checked staff, MFA on their side, incident reporting to the client, notice within 24 hours when an engineer leaves the provider, and an independent security report (such as SOC 2) if available |
| **Logging and audit** | CloudTrail, Systems Manager execution history, VPN and jump host logs, and tickets. SIEM alerts on every role assumption and on any action outside the runbook list. |
| **Review** | Security and Compliance review the provider's activity regularly. The client reviews the contract, runbooks, and the provider's security practices each year. |
| **Offboarding** | When an engineer leaves the provider, the provider notifies the client and removes the person from its side, and the client removes the on-prem account. When the contract ends, the trust relationship and accounts are deleted. Sponsor confirms in the ticket, and Compliance verifies at the next review. |
| **Status** | Draft, pending client confirmation of the covered systems, runbook list, and whether on-prem access is needed |

## 3. Access Flow

```
Contract approved (NDA + security requirements + end date)
        |
Sponsor raises a request ticket → Security Administration approves
        |
Cross-account role created with the provider's account as the only trusted party
(external ID + MFA on the provider's side + tag-based scope)
        |
Provider engineers sign in through their own identity system, MFA required
        |
Role: External-AfterHours-Operator
        |
AWS: tagged resources in production, client-facing, and corporate | On-prem: named accounts through VPN and jump host
```

**Nightly alert handling:**
```
Alert fires → provider sees it in monitoring → triage
        |-- Matches a runbook → run the approved action → record in the ticket
        |-- No runbook match → call the on-call employee → record the escalation
        |
Morning handover to IT Operations
```

**Anything outside the runbook list:**
```
Escalate to the on-call employee → employee decides and acts, or approves a one-time action through the change process
```

## 4. Role: External-AfterHours-Operator

**Allowed:**
- CloudWatch dashboards, logs, and alarms (view)
- SNS alert delivery
- EC2 reboot on instances tagged for after-hours coverage
- Systems Manager Run Command using **only the approved list of documents**
- Read-only CloudTrail
- Read-only status of AWS Backup jobs

**Denied:**
- Interactive Session Manager sessions and free-form commands
- Launching, modifying, stopping, or terminating servers
- Patching and software installation
- IAM changes, KMS key administration
- Network changes (VPC, routing, security groups, VPN, Direct Connect)
- Backup deletion or restore
- Security and log archive changes
- Reading business data (S3 objects, database contents)
- Billing and the management account

## 5. Security Controls
- Cross-account role protected by an external ID, trusting only the provider's AWS account
- MFA required on the provider's side, with each engineer's name recorded in the session
- Scope limited by resource tags, so the role only reaches systems covered by the runbooks
- Approved Systems Manager documents instead of an open shell, so actions are limited to a written list
- SIEM alert on every role assumption and on any action outside the runbook list
- No long-lived access keys
- Source IP restrictions to the provider's known ranges where possible
- Provider contract requires an NDA, background-checked staff, incident reporting, and notice when staff leave
- Regular access review by Security and Compliance, and an annual review by the client
- Separation of duties: the provider watches and applies approved fixes, IT Operations runs and changes systems, Security Administration owns identity, keys, and logs

## 6. Requirements Traceability

| Requirement | Met by |
|---|---|
| External access is limited to a defined purpose | Runbook list of systems and actions |
| Someone inside the company is responsible | Named sponsor |
| No shared accounts | Named engineers, with MFA, recorded in each session |
| Provider cannot read business data | No data permissions in the role |
| Provider cannot change the environment beyond approved fixes | No shell access. Approved documents only. Tag-based scope. |
| Provider cannot weaken security | No IAM, KMS, network, or log permissions |
| Activity is traceable | CloudTrail, Systems Manager history, VPN and jump host logs, SIEM alerts |
| Access ends cleanly | Provider notification of leavers, contract-end removal, sponsor confirmation, Compliance verification |

## 7. Assumptions and Open Items
- Covered systems, runbook list, and approved Systems Manager documents to be defined with the client
- Whether the provider needs on-prem access, or AWS only, to be confirmed
- Whether access is always on or restricted to after-hours windows to be decided
- Provider's identity system and source IP ranges to be obtained from the provider
- Escalation contacts and on-call rotation to be defined by the client
- Exact IAM policy actions and trust policy for `External-AfterHours-Operator` to be documented in the build guide
