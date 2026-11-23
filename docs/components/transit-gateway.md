# Transit Gateway and route tables

**Layer:** Network
**Lives in:** tcsp-network · Infra OU (shared to the organization with AWS RAM)
**Owned by:** Platform Engineering team

## What it is
The routing hub that every VPC in TCSP attaches to, with separate route tables that decide which networks can reach which.

## Why it exists
Pairwise VPC peering does not scale and hides who can reach what. One hub with explicit route tables makes reachability a reviewed design, denied by default.

## What it is built to accommodate
Hundreds of attachments, separate routing domains (Shared, Prod, Non-Prod, Access), central egress and inspection, and the corporate link. Use it for all cross-VPC and on-premises traffic. Traffic inside a single VPC never touches it.

## How it works
Inputs: VPC attachments created by AFT account customizations, Client VPN and Direct Connect attachments. What it does: associates each attachment with a route table and propagates only allowed routes. Shared reaches every project; Prod and Non-Prod reach Shared, egress and on-premises but not each other; Access reaches only what each user group is allowed. Outputs: controlled connectivity.

## Connected flows
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
Infra OU SCP limits changes to the network pipeline role. Project OU SCP denies creating TGW attachments outside the platform role.

## TCSP defaults and options
Default: four route tables (Shared, Prod, Non-Prod, Access), attachment auto-accept off, appliance mode for inspected flows.

## Watch out for
Data processing charges add up for chatty services; keep high-volume traffic inside a VPC where possible. Route-table mistakes can expose environments to each other, so test reachability in the pipeline.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-tgw) · [Overview](../00-overview.md)
