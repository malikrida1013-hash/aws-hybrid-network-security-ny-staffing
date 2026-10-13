# WorkStation Cloud — AWS Solution Architecture
## Project Overview
This project documents the design of a secure, highly available AWS architecture for WorkStation Cloud,
a fictional business that provides rented computers and cloud-based management services to business customers.
The goal is to translate business requirements into an AWS architecture that supports customer access, internal operations,
network isolation, security, and future growth.

## Business Context

WorkStation Cloud serves approximately 250 business customers and manages around 12,000 computers.
The platform supports customer account management, computer rental management, and support requests.
Internal employees also require controlled access to operational functions and AWS infrastructure.

## Architecture Goals

- Provide access through a professional company domain.
- Support external customers and internal employees through appropriate access paths.
- Isolate public-facing entry points from private application resources.
- Design for availability across multiple Availability Zones.
- Apply least-privilege access controls.
- Support future growth and infrastructure changes.
- Document architecture decisions and unresolved requirements.

## Architecture Scope

The project covers:

- Business requirements and assumptions
- AWS architecture and service selection
- VPC networking and traffic flow
- DNS and application entry points
- Infrastructure access and IAM authorization
- Security monitoring and logging
- Deployment planning and validation

## Documentation

Detailed requirements, design decisions, and architecture documentation are maintained in the [`docs/`](docs/) directory.

## Architecture Diagrams

Architecture diagrams are maintained in the [`diagrams/`](diagrams/) directory.

## Implementation Status

This repository currently focuses on requirements gathering, architecture design, and technical documentation.
AWS deployment and validation will be added as the design progresses.

## Design Principles

- Business requirements drive technical decisions.
- Apply least privilege and defense in depth.
- Keep application and data resources private where possible.
- Prefer managed AWS services where appropriate.
- Document assumptions, trade-offs, and open decisions.

## Disclaimer

WorkStation Cloud is a fictional business created for educational and portfolio purposes. The architecture is a proposed
design, not a description of a deployed production environment.

