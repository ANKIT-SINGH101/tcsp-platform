# AWS Client VPN

**Layer:** Network
**Lives in:** tcsp-network · Infra OU
**Owned by:** Platform Engineering team

## What it is
Authenticated remote access that lets engineers reach private TCSP resources, such as the shared VCS, without exposing them to the internet.

## Why it exists
Private-by-default workloads still need human access. Client VPN gives that access through the company identity provider, with per-group authorization and logs.

## What it is built to accommodate
Engineers working from anywhere, group-based authorization rules, and connection logs for audits. With native Transit Gateway attachment, it needs no intermediate VPC and keeps each user's source IP visible. It is not an internet gateway and not a site-to-site link.

## How it works
Inputs: a user connecting with the VPN client and signing in through the company IdP (SAML). What it does: authenticates, applies authorization rules by group, and routes traffic into the Access route table of the Transit Gateway. Outputs: private access to only the networks that group is allowed.

## Connected flows
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.

## Guardrails that apply
Infra OU SCP limits endpoint changes to the network pipeline. Connection logs go to tcsp-log-archive.

## TCSP defaults and options
Default: SAML federation with the company IdP, split tunnel, native TGW attachment. Option: AWS Verified Access for application-level access without a network tunnel.

## Watch out for
Charged per association-hour and per connection-hour. Over-broad authorization rules undo the Access route-table design.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-cvpn) · [Overview](../00-overview.md)
