# Shared VCS

**Layer:** Delivery
**Lives in:** tcsp-shared-services · Infra OU
**Owned by:** Platform Engineering team operates it; project teams own their repositories

## What it is
The Git service for application code and deployment manifests, reached privately over Client VPN and the Transit Gateway.

## Why it exists
One Git service with company sign-in, branch protection and audit logs replaces team-run repositories with uneven controls.

## What it is built to accommodate
Repositories for every project, group-based access from the company IdP, merge-request reviews, and webhooks that trigger shared CI. Path B teams may use CodeCommit in their own account instead.

## How it works
Inputs: pushes and merge requests from engineers. What it does: stores code, enforces branch rules, triggers CI pipelines. Outputs: CI jobs, and manifest repositories that project CD tools watch.

## Connected flows
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
Branch protection, required reviews, secret scanning on push. Network access only through Client VPN and the Shared route table.

## TCSP defaults and options
Default: GitLab or GitHub Enterprise Server, SSO with the company IdP, protected main branches. Option: a SaaS VCS with private runners in TCSP, if policy allows code to live outside AWS.

## Watch out for
Self-hosted VCS needs backups, upgrades and HA. Repository permissions should follow IdP groups, not individuals.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-vcs) · [Overview](../00-overview.md)
