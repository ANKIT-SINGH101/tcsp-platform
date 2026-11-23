# Development space

**Layer:** Provisioning
**Lives in:** each project account · Project OU
**Owned by:** Project team

## What it is
A pre-approved sandbox VPC and toolset that a project team launches from Service Catalog inside its own account, to experiment before choosing a delivery path.

## Why it exists
Teams want to try things on day one. A ready-made, compliant space lets them start immediately without asking for a network design or a security review.

## What it is built to accommodate
Prototypes, spikes, load tests and learning, with private subnets attached to the Transit Gateway and the same logging and detection as production. It is not for production traffic and not for long-lived shared services.

## How it works
Inputs: a launch of the Development space product with a few parameters (size, AZ count). What it does: creates a VPC from an IPAM allocation, private subnets, endpoints and a small set of developer tools. Outputs: a working space the team controls, inside the account guardrails.

## Connected flows
- [Flow 4: Template deploy](../02-service-flows.md#4-template-deploy): Service Catalog deploys a best-practice template as the project's Development space.

## Guardrails that apply
Project OU SCPs (no Internet Gateways, no VPC peering, no public data) and the TF Baseline apply. Budgets alert when spend passes the threshold.

## TCSP defaults and options
Default: two private subnets, VPC endpoints for common services, egress through the central egress VPC. Option: a time-to-live tag that tears the space down after a set number of days.

## Watch out for
Forgotten spaces cost money, so keep budgets on. Teams sometimes promote a sandbox straight to production; require Path A or Path B for anything serving users.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-devspace) · [Overview](../00-overview.md)
