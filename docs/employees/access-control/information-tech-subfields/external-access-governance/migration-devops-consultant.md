# External Party Access Profile: Migration and DevOps Consultants

## 1. Who they are
Outside consultants who design and build the AWS environment and move workloads from the on-prem data centers into AWS, in repeatable migration waves. They are not employees, so their access is temporary, sponsored, and more closely watched than an employee's in the same role.

## 2. Access Profile

| Field | Details |
|---|---|
| **Party** | Migration and DevOps consultants |
| **Purpose** | Build the AWS environment, run migration waves, and document the implementation, without access to business data or control of identity, keys, or logs |
| **Sponsor** | A named employee (for example the head of IT), responsible for the engagement and for confirming offboarding |
| **Engagement period** | Fixed start and end date set by the contract. Access expires automatically on the end date. Renewal needs a new approval. |
| **Identity** | Individual guest identity per named consultant in IAM Identity Center. No shared accounts. Not members of employee groups. |
| **AD / Identity Center group** | `External-Consultants` |
| **AWS Permission Set** | `External-Builder` |
| **AWS account scope** | Dev: full build rights. Shared services and network account: build tooling and connectivity work through the change process. Production, client-facing, and corporate: read-only by default, with time-limited write access only for approved cutover windows. No access to security/log archive, backup, management, or billing accounts. |
| **Migration tooling** | Application Migration Service, Database Migration Service, and infrastructure as code deployments (Terraform or CloudFormation), run in dev and through the change process elsewhere |
| **Business data** | Not read. Migration tools replicate data into encrypted staging, with keys controlled by Security Administration. Consultants operate the tools and validate counts and status, not contents. Cutover testing of contents is done by client staff. |
| **On-prem access** | Limited, scoped access to source servers for installing and checking replication agents, through the jump host, time-limited, supervised, and logged. Alternatively performed by System and Server Administration. |
| **Initial environment setup** | Creating the account structure, baseline guardrails, and landing zone needs elevated rights. This is done through a supervised, time-limited elevation approved by Security Administration, not through a standing permission. |
| **Explicitly denied** | IAM changes, KMS key administration, security log and log archive changes, backup vault settings, billing, the management account, reading business data, Domain Admin |
| **Network path** | IAM Identity Center sign-in to the AWS console with MFA. On-prem through the jump host on the management segment. Session Manager for server access, with no inbound ports opened. |
| **Authentication** | MFA required. No long-lived access keys. Pipelines and scripts use temporary credentials. |
| **Production exception** | Change ticket, approval by the sponsor and the system owner, time-limited access, automatic removal, and review afterward |
| **Deliverables ownership** | Code, runbooks, diagrams, and documentation are stored in the client's repository and handed over at the end of the engagement |
| **Logging and audit** | CloudTrail, Session Manager logs, and SIEM alerts on external accounts. Reviewed regularly by Security and Compliance. |
| **Offboarding** | Access expires on the end date. The sponsor confirms in the ticket. Security removes the identities. Compliance verifies removal at the next review. |
| **Status** | Draft, pending client confirmation of the engagement scope, end date, and sponsor |

## 3. Access Flow

```
Engagement approved (contract + NDA + end date)
        |
Sponsor raises a request ticket → Security Administration approves
        |
Named consultants added as guest identities in IAM Identity Center
        |
AD / Identity Center group: External-Consultants (end date set)
        |
Permission set: External-Builder
        |
AWS accounts: Dev (build) | Shared services and network (change process) | Production (read-only, exception path)
        |
Access expires on the end date → sponsor confirms → Security removes → Compliance verifies
```

**Production exception (time-limited):**
```
Cutover window planned → change ticket → approval by sponsor and system owner
→ time-limited write access → automatic removal → alert and review afterward
```

## 4. Permission Set: External-Builder

**Allowed:**
- EC2, EBS, AMIs, and launch templates in dev
- VPC, subnets, and route tables in dev, and in the network account through the change process
- Application Migration Service and Database Migration Service
- Infrastructure as code deployments (Terraform or CloudFormation)
- Systems Manager (Session Manager, Run Command)
- CloudWatch and read-only CloudTrail
- Read-only access to production, client-facing, and corporate accounts

**Denied:**
- IAM changes, KMS key administration, security and log archive changes
- Backup vault settings
- Reading business data (S3 objects, database contents)
- Billing and the management account

## 5. Security Controls
- Individual named accounts only, with MFA, and no shared logins
- No long-lived access keys. Temporary credentials only.
- Access limited to the engagement period, with automatic expiry
- Production changes only through an approved change ticket
- Data replication encrypted with keys controlled by Security Administration
- No inbound ports. Session Manager for server access, and the jump host for on-prem.
- Elevated rights for initial setup are supervised, time-limited, and approved
- SIEM alerts on external account activity
- Separation of duties: consultants build, internal teams run, Security Administration owns identity, keys, and logs

## 6. Requirements Traceability

| Requirement | Met by |
|---|---|
| External access is temporary | Fixed end date, automatic expiry |
| Someone inside the company is responsible | Named sponsor |
| No shared accounts | Individual guest identities with MFA |
| Consultants cannot read business data | No data permissions. Keys held by Security. |
| Consultants cannot weaken security | No IAM, KMS, or log permissions |
| Production is protected | Read-only by default, with a ticketed exception |
| Activity is traceable | CloudTrail, Session Manager logs, SIEM |
| Access ends cleanly | Sponsor confirmation, Security removal, Compliance verification |

## 7. Assumptions and Open Items
- Sponsor, engagement scope, and end date to be confirmed with the client
- Number of consultants and whether each is a named individual or part of a company to be confirmed
- Whether source server work is done by consultants or by System and Server Administration to be decided
- Cutover windows and who approves them to be defined in the migration plan
- Exact IAM policy actions for `External-Builder` to be documented in the build guide
