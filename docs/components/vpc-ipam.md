# VPC IPAM

**Layer:** Network
**Lives in:** tcsp-network · Infra OU (pools shared with AWS RAM)
**Owned by:** Platform Engineering team

## What it is
IP address management that hands out non-overlapping CIDR ranges to every VPC from planned pools.

## Why it exists
Overlapping ranges make routing impossible and are expensive to fix later. Central allocation prevents them from the first VPC.

## What it is built to accommodate
Pools per Region and environment, automatic allocation when AFT creates a project VPC, and checks of existing accounts before they join TCSP. It is a recommended addition to the core design.

## How it works
Inputs: the TCSP address plan and allocation requests from AFT customizations or templates. What it does: allocates the next free CIDR from the right pool and tracks usage. Outputs: unique ranges and a record of who uses which range.

## Connected flows
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.

## Guardrails that apply
IPAM allocation rules enforce pool sizes. Policy-as-code checks reject VPCs created outside IPAM.

## TCSP defaults and options
Default: top-level pool per Region, child pools for Prod, Non-Prod and Shared, /20 per project VPC. Option: import existing ranges to detect overlap during migration.

## Watch out for
A pool that is too small runs out; leave room for growth. Ranges must not clash with on-premises networks.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-ipam) · [Overview](../00-overview.md)
