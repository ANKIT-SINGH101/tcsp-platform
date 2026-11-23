# Route 53 Resolver

**Layer:** Network
**Lives in:** tcsp-network · Infra OU (rules shared with AWS RAM)
**Owned by:** Platform Engineering team

## What it is
Central DNS resolution: inbound and outbound Resolver endpoints in the network account, with forwarding rules shared to every VPC.

## Why it exists
Hybrid name resolution set up per account is inconsistent and fragile. Central endpoints and shared rules give every VPC the same view of corporate and AWS names.

## What it is built to accommodate
Resolving corporate names from TCSP, resolving private TCSP names from the corporate network, private hosted zones shared across accounts, and DNS Firewall rules. It is a recommended addition to the core design.

## How it works
Inputs: forwarding rules and private hosted zone associations. What it does: forwards queries for corporate domains to on-premises DNS and answers on-premises queries for TCSP zones. Outputs: consistent name resolution for every attached VPC.

## Connected flows
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.

## Guardrails that apply
Rule changes only through the network pipeline. DNS query logs can be sent to tcsp-log-archive.

## TCSP defaults and options
Default: endpoints in two AZs, rules shared to the organization with RAM, DNS Firewall in alert mode. Option: per-environment rule sets if Prod and Non-Prod resolve different corporate zones.

## Watch out for
Endpoints are charged per ENI-hour. A broken forwarding rule affects every VPC that uses it.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-r53) · [Overview](../00-overview.md)
