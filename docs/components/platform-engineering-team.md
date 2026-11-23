# Platform Engineering team

**Layer:** People
**Lives in:** outside AWS · works through the platform VCS
**Owned by:** Platform Engineering team

## What it is
The people who build and run TCSP's shared parts: the organization and its policies, the account factory, the network, shared CI and the templates. They change the platform only by pushing Terraform to the platform VCS.

## Why it exists
A platform needs an owner. Working through Git, not tickets, keeps the team from becoming an approval queue and makes every change reviewable.

## What it is built to accommodate
Account requests reviewed as merge requests, baseline and policy changes rolled out in stages, support for project teams on request, and on-call for AFT, the Transit Gateway and shared CI. They do not run project applications; teams own their own workloads.

## How it works
Inputs: ITSM requests, team feedback, security requirements. What it does: writes and reviews Terraform for tcsp-org, AFT repositories, tcsp-network, tcsp-shared-ci, tcsp-policies and tcsp-templates; publishes golden paths and dashboards. Outputs: platform changes merged through Git, and documentation like this repository.

## Connected flows
- [Flow 1: Platform change](../02-service-flows.md#1-platform-change): The Platform Engineering team pushes Terraform to the platform VCS; the merge triggers AFT.
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.

## Guardrails that apply
Their changes pass the same branch protection and policy-as-code checks as anyone's. Management-account access is limited and alarmed.

## TCSP defaults and options
Default: a small core team with clear service ownership, platform SLOs, a time-limited SCP exception process, and a quarterly baseline review with Security.

## Watch out for
Ticket-driven work creeping back in. Single points of knowledge: pair on AFT, network and policy changes.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-pe) · [Overview](../00-overview.md)
