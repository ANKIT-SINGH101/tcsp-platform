# Direct Connect and Site-to-Site VPN

**Layer:** Network
**Lives in:** tcsp-network · Infra OU
**Owned by:** Platform Engineering team

## What it is
The private link between TCSP and the company's data centres and corporate network, with Site-to-Site VPN as an optional backup path.

## Why it exists
Many workloads still need on-premises systems. A private, predictable link avoids sending that traffic over the internet.

## What it is built to accommodate
Hybrid applications, data transfer and corporate access with steady bandwidth and latency; a VPN backup for resilience or as a quick start while the dedicated link is ordered. Skip it on day one if no workload needs on-premises access yet.

## How it works
Inputs: a Direct Connect connection or partner circuit, and optionally VPN tunnels from the corporate edge. What it does: terminates on a Direct Connect gateway associated with the Transit Gateway; BGP routes are filtered into the right route tables. Outputs: private routes between TCSP and the corporate network.

## Connected flows
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.

## Guardrails that apply
Infra OU SCP limits changes to the network pipeline role. Routes from on-premises propagate only to approved route tables.

## TCSP defaults and options
Default: two connections at two locations for production, Site-to-Site VPN as backup. Option: start with VPN only and add Direct Connect when traffic justifies it.

## Watch out for
Lead time for physical connections is long, so order early. Overlapping IP ranges with on-premises networks must be fixed before routing.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-dx) · [Overview](../00-overview.md)
