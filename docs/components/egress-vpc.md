# Egress VPC (NAT + Network Firewall)

**Layer:** Network
**Lives in:** tcsp-network · Infra OU
**Owned by:** Platform Engineering team

## What it is
A central VPC that handles outbound internet traffic for all project VPCs, through NAT Gateways and AWS Network Firewall, reached via the Transit Gateway.

## Why it exists
Per-account NAT Gateways cost more and give no central view of outbound traffic. One inspected exit makes outbound rules, logging and cost visible.

## What it is built to accommodate
Outbound traffic for package downloads, SaaS APIs and updates from every project and from shared CI, with domain allow-lists and logging. Inbound public traffic does not go through it; that uses a separate, optional public entry pattern.

## How it works
Inputs: default routes from project route tables pointing to the egress attachment. What it does: inspects traffic with Network Firewall rules, then translates it through NAT Gateways in two AZs. Outputs: controlled internet access and flow and alert logs.

## Connected flows
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
Project OU SCP denies Internet Gateways, so this is the only path out. Firewall rule changes go through the network pipeline.

## TCSP defaults and options
Default: two AZs, domain allow-list for Prod, alert-only mode for Non-Prod. Option: per-account NAT for workloads with very high outbound volume.

## Watch out for
NAT and firewall processing charges are shared costs. A tight allow-list can break builds; give teams a fast way to request a domain.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-egress) · [Overview](../00-overview.md)
