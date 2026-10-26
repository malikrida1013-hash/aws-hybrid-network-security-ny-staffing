# Address Plan

The IP address ranges for the company's own sites and for each AWS network, chosen so none of them overlap.

| | |
|---|---|
| **Project** | NY staffing company, multi-account AWS design |
| **Document status** | Draft |
| **Related documents** | [`01-network-inventory.md`](./01-network-inventory.md), [`04-network-account.md`](../architecture/accounts/04-network-account.md) |

## 1. Why this matters

Two networks that use the same addresses cannot talk to each other. The company's sites already use some ranges, so the AWS ranges must be different. This plan picks AWS ranges that do not overlap with any of them.

## 2. How to read an address range

A range like `10.128.0.0/16` has a number after the slash. The smaller that number, the bigger the range.

| Size | Addresses | Usually used for |
|---|---|---|
| `/16` | 65,536 | A whole VPC |
| `/20` | 4,096 | A group of subnets |
| `/24` | 256 | A single subnet |
| `/28` | 16 | The smallest subnet AWS allows |

AWS keeps 5 addresses in every subnet for itself. A `/24` therefore gives 251 usable addresses.

## 3. The company's existing ranges

These came from the client.

| Where | Range | Notes |
|---|---|---|
| Queens data center | `10.20.0.0/16` | Domain controllers in `10.20.5.0/24`, payroll in `10.20.20.0/24`, HR in `10.20.21.0/24` |
| Westchester data center | `10.30.0.0/16` | One domain controller in `10.30.5.0/24` |
| Offices | `10.40.0.0/16` | |
| Building kiosks and site VPNs | `10.60.0.0/16` | Reached through Queens |

**Ranges to avoid**

| Range | Reason |
|---|---|
| `10.0.0.0/16` | An old lab network |
| `172.16.0.0/16` | The scheduling vendor's VPN |
| `192.168.0.0/16` | Office Wi-Fi |

**Domain name:** `corp.nystaffing.example`

## 4. The AWS ranges

All AWS ranges sit inside `10.128.0.0/12`, which does not touch any range above. It is split by region.

| Region | Block |
|---|---|
| us-east-1 | `10.128.0.0/13` |
| us-east-2 | `10.136.0.0/13` |

**us-east-1**

| Network | Range | Notes |
|---|---|---|
| Production | `10.128.0.0/16` | Connects to the shared network |
| Client-facing | `10.129.0.0/16` | Does not connect |
| Non-Production | `10.130.0.0/16` | Does not connect |
| Network | `10.131.0.0/16` | Name-lookup endpoints only |
| Backup | `10.132.0.0/16` | Only if Backup attaches to the hub |
| Spare | `10.133.0.0/16` to `10.135.0.0/16` | Room to grow |

**us-east-2**

| Network | Range | Notes |
|---|---|---|
| Production standby | `10.136.0.0/16` | Only if the client approves the critical-tier standby |
| Spare | `10.137.0.0/16` to `10.143.0.0/16` | Room to grow |

## 5. Rules behind the plan

1. **No overlap.** Every AWS range starts at `10.128` or above. Every company range is below that.
2. **One `/16` per network.** It is more space than needed, but private addresses cost nothing, and no network can run out of room.
3. **Every network gets its own range,** even the ones that do not connect, so a future link needs no re-addressing.
4. **The same pattern in both regions.** Production is `10.128` in us-east-1 and `10.136` in us-east-2.

## 6. Overlap check

| AWS block | Checked against | Result |
|---|---|---|
| `10.128.0.0/12` | `10.0.0.0/16`, `10.20.0.0/16`, `10.30.0.0/16`, `10.40.0.0/16`, `10.60.0.0/16`, `172.16.0.0/16`, `192.168.0.0/16` | No overlap |

## 7. Assumptions

- The client's list of existing ranges is complete. If a range is missing, a conflict will only show up when the link is tested, so ask the client to confirm.
- The vendor's range `172.16.0.0/16` stays as it is.

## 8. What comes next

The next file, `03-subnet-design.md`, splits each `/16` into subnets.
