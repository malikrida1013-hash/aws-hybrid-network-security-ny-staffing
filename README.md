# Hybrid AWS Network & Security Architecture
### Migrating half of a New York building-services workforce platform to AWS, without giving up the data center

> **A portfolio project by an independent AWS Solution Architect.**
> The company and requirements in this repository are a realistic, fictional scenario. The design, however, is built the way I would deliver it for a real client.

---

## The situation

A large New York company supplies staff to residential apartment buildings across the city: doormen, concierges, porters, cleaners, maintenance technicians, and front desk teams. They employ thousands of people and serve a long list of buildings, which means a lot of scheduling, time clocks, payroll, background checks, and client portals.

Everything runs out of two aging data centers in New York. They are expensive to keep up and hard to modernize. Leadership wants to move roughly **half** of the workload to AWS and keep the rest on-premises for now, mainly payroll, legacy databases, and file shares.

The ask was clear: design the **network layer** and the **security layer** for a hybrid environment that can pass an audit and keep running 24/7.

## What makes this hard

- **Sensitive data everywhere.** Employee PII, payroll, background check results, client records, and building access information.
- **Real compliance pressure.** The environment has to stand up to audits, including NY SHIELD Act obligations, FCRA handling of background checks, and a SOC 2-style control review.
- **A true hybrid, not a lift-and-shift.** Two data centers and AWS have to behave like one network, with one identity system.
- **No tolerance for long outages.** Buildings need staff on the clock and on the schedule every day of the year.
- **Latency matters.** Time clocks and scheduling tools are used by people standing in lobbies across the five boroughs.
- **Budget awareness.** Spend should be sensible, not stripped to the bone.

## What this project delivers

| Area | What you will find |
|---|---|
| Business context | Requirements, constraints, assumptions, and success criteria |
| Architecture | Current state and target state, with diagrams |
| Network design | Hybrid connectivity, VPCs, subnets, routing, DNS, egress, load balancing |
| Security design | Identity, segmentation, firewalls, detection, encryption, key management, logging |
| Resilience | High availability and disaster recovery, with recovery targets |
| Migration | A wave-by-wave plan for moving about 50% of workloads |
| Operations | Monitoring, incident response, and day-two runbooks |
| Cost | Estimate, main cost drivers, and optimization levers |
| Governance | Risk register and compliance control mapping |
| Code | Infrastructure-as-code examples for the core building blocks |

## Architecture at a glance

```
                        ┌──────────────────────────────┐
                        │   NYC Data Centers (x2)      │
                        │  Payroll · Legacy DBs · AD   │
                        └──────┬───────────────┬───────┘
                               │               │
               Primary: Direct Connect     Backup: Site-to-Site VPN
                               │               │
                        ┌──────▼───────────────▼───────┐
                        │        Transit Gateway       │
                        └──┬─────┬──────┬──────┬───────┘
                           │     │      │      │
                   ┌───────▼┐ ┌──▼────┐ ┌▼─────┐ ┌▼──────────┐
                   │Shared  │ │ Prod  │ │ Dev  │ │ Client-   │
                   │Services│ │       │ │      │ │ facing    │
                   └────────┘ └───────┘ └──────┘ └───────────┘
                           Inspection VPC (AWS Network Firewall)
              Separate Security, Log Archive, and Management accounts
```

The environment is organized as a **multi-account AWS Organization**, with accounts split by purpose and risk:

- **Management**: billing and organization control only
- **Security**: GuardDuty, Security Hub, and audit tooling
- **Log Archive**: immutable, centralized logs
- **Network / Shared Services**: Transit Gateway, Direct Connect, DNS, inspection
- **Corporate**: internal business applications
- **Production**: scheduling, time tracking, and other live workloads
- **Client-facing**: client portals and external entry points
- **Development**: build and test, isolated from production data

## Design highlights

**Hybrid connectivity.** Direct Connect from a carrier-neutral facility in the NYC metro area is the primary path. A Site-to-Site VPN provides an encrypted backup path. Routing is dynamic (BGP), with clear preferences so failover is predictable. Primary region is chosen with latency to New York in mind.

**Segmentation.** Each environment lives in its own account and VPC. Traffic between them goes through Transit Gateway with separate route tables, and anything crossing a trust boundary is inspected. Production and development cannot reach each other by default.

**Identity.** The existing Active Directory is extended, not replaced. Workforce access runs through AWS IAM Identity Center with SSO and enforced MFA. Permissions follow least privilege, and guardrails are set at the organization level with Service Control Policies.

**Defense in depth.** AWS Network Firewall for stateful inspection and IDS/IPS rules, AWS WAF and Shield on public endpoints, security groups and NACLs for layered filtering, and no direct internet exposure for sensitive tiers.

**Detection and logging.** CloudTrail, Config, VPC Flow Logs, GuardDuty, and Security Hub feed a central log archive. Alerts route into a defined incident response process.

**Encryption.** Data is encrypted in transit and at rest. KMS customer-managed keys are separated by data classification, with clear ownership and rotation. Secrets live in Secrets Manager.

**Resilience.** Workloads span multiple Availability Zones. A second region handles disaster recovery, with documented recovery time and recovery point targets per workload tier.

## Migration approach

The move happens in waves so that risk stays low and the business keeps running.

1. **Foundation.** Accounts, guardrails, identity, connectivity, logging.
2. **Low-risk workloads.** Development, test, and internal tools to build confidence.
3. **Client-facing and scheduling services.** Moved with rollback plans and parallel running.
4. **Selected corporate and production systems.** Cutover in planned windows.
5. **Optimization and decommissioning.** Right-size, tune cost, retire what is no longer needed.

Payroll, legacy databases, and file shares stay on-premises for now, and the design leaves a clean path to move them later.

## Repository structure

```
.
├── README.md
├── 01-business-requirements/     Client brief, goals, constraints, assumptions
├── 02-architecture/              Current and target state, diagrams
├── 03-network-design/            Connectivity, VPCs, routing, IP addressing plan
├── 04-security-design/           Identity, firewalls, encryption, logging, detection
├── 05-resilience/                High availability and disaster recovery
├── 06-migration-plan/            Waves, cutover steps, rollback
├── 07-cost/                      Estimate and optimization notes
├── 08-governance/                Risk register and compliance control mapping
├── 09-runbooks/                  Operations and incident response guidance
└── 10-iac/                       Terraform examples for core components
```

## How to read this project

- **Hiring manager or business reader:** start with `01-business-requirements` and the executive summary, then skim the diagrams in `02-architecture`.
- **Network or DevOps engineer:** go to `03-network-design` and `10-iac` for CIDR ranges, route tables, and deployable examples.
- **Security or audit reviewer:** go to `04-security-design` and `08-governance` for controls and how they map to requirements.

## Assumptions

- The two data centers are in the New York metro area and are already connected to each other.
- Active Directory is the authoritative identity source today.
- The business accepts a phased migration with short, planned cutover windows.
- Exact bandwidth, workload counts, and recovery targets are placeholders to be confirmed in a real engagement.

## About this work

I design AWS architectures that are clear enough for a business owner to approve and detailed enough for an engineer to build. This project shows how I take a rough, real-world request, turn it into structured requirements, and deliver a design with the documentation a team needs to implement it.

If you have a similar migration or hybrid network challenge, I would be glad to talk.

Contact: Rida Malik, malikrida1013@gmail.com
---

*This is a fictional scenario created for portfolio purposes. Any resemblance to a real company is coincidental.*


