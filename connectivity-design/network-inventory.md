# Network Inventory

Which accounts have their own network (VPC), what each one holds, and which ones connect to the shared network.

| | |
|---|---|
| **Project** | NY staffing company, multi-account AWS design |
| **Document status** | Draft |
| **Related documents** | [`04-network-account.md`](../architecture/accounts/04-network-account.md), [`02-address-plan.md`](./02-address-plan.md) |

## 1. Why this list exists

Every later step in the network layer (addresses, subnets, routes, rules) starts from this list. It answers one question: where do we need a network, and what is it for?

## 2. Accounts and their networks

| Account | Own network (VPC)? | Zones | What it holds | Connects to the shared network? |
|---|---|---|---|---|
| Production | Yes | 2 | App servers, databases, its own internet entry, domain controllers | Yes |
| Client-facing | Yes | 2 | Owner portal servers, portal database, load balancer | No |
| Non-Production | Yes | 1 | Test servers and test database | No |
| Network | Yes, small | 1 | Name-lookup endpoints only | It is the hub |
| Backup | Yes, tiny, if approved | 1 | Only what is needed to attach to the hub | Yes, if approved |
| Management | No | | Managed services only | No |
| Security / Audit | No | | Managed services only | No |
| Log Archive | No | | Managed services only | No |
| IT Tooling | No | | Managed services only | No |

In us-east-2 there is one more network: a small standby copy of the Production network for the critical systems, if the client approves it.

## 3. What connects to what

- **Only Production and Backup connect to the shared network.** Everything else stays off it.
- **Production and Backup do not reach each other through the hub.** Each one reaches the company's own sites only.
- **Client-facing, Non-Production, and IT Tooling have no network link** to the shared network or to the company's sites.
- Accounts without a VPC talk to each other account to account, not over a network.

## 4. Changes this list causes in other files

| Change | File to update |
|---|---|
| Production needs two domain controllers, one in each zone, so sign-ins keep working if both links to the company's sites fail. This adds a small "identity" subnet in each zone | `06-production-account.md` and its diagram, and the failure table in `04-network-account.md` |
| Backup needs a small network if it attaches to the hub | `05-backup-account.md` |

## 5. Open points

- Does Backup attach to the hub, or do on-prem backups reach AWS another way?
- Does the standby network in us-east-2 get built? It depends on the client approving the critical-tier standby.
