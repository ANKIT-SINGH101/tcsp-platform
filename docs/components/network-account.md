# Network account

**Layer:** Network
**Lives in:** tcsp-network · Infra OU
**Owned by:** Platform Engineering team

## What it is
The account that owns all routing in TCSP: the Transit Gateway, Client VPN, the corporate link, the egress VPC, IP address management and DNS resolution.

## Why it exists
If every team builds its own network edge, nobody can see or control traffic. One network account gives one place for routing, inspection and corporate connectivity.

## What it is built to accommodate
A hub-and-spoke network that grows to hundreds of VPCs without a mesh of peerings, with separate reachability for Prod, Non-Prod, shared services and remote access. Project accounts only attach to it. It does not host applications.

## How it works
Inputs: Terraform from the tcsp-network repo and TGW attachment requests from AFT customizations. What it does: shares the Transit Gateway, IPAM pools and Resolver rules to the organization with AWS RAM, and runs central egress and remote access. Outputs: connectivity between accounts, to the internet and to the corporate network.

## Connected flows
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.

## Guardrails that apply
Infra OU SCP denies Transit Gateway, Direct Connect, Client VPN and route-table changes except from the network pipeline role.

## TCSP defaults and options
Default: deployed early in the build, in the home Region, with two AZs for every component. Option: a second network account in the DR Region with inter-Region TGW peering.

## Watch out for
An outage here affects every project. Transit Gateway data processing and NAT are shared costs that need a charge-back rule.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-netacct) · [Overview](../00-overview.md)
