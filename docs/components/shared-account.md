# Shared account

**Layer:** Delivery
**Lives in:** tcsp-shared-services · Infra OU
**Owned by:** Platform Engineering team

## What it is
The account that hosts tools every project uses: the shared VCS for application code, the shared CI cluster on EKS and the shared container registry.

## Why it exists
Running one secured copy of each developer tool is cheaper and safer than every team running its own. Teams get working tools on day one instead of building them.

## What it is built to accommodate
Many projects at once, each isolated by repository permissions, CI namespaces and registry repositories. Private subnets in two AZs keep tools off the internet. It is not a place for project workloads or project-specific databases.

## How it works
Inputs: the shared-ci customization set from AFT and Terraform from the tcsp-shared-ci repo. What it does: runs the VCS, the EKS CI cluster and ECR, attached to the Transit Gateway's Shared route table so it can reach project VPCs. Outputs: build results and images that project CD pulls.

## Connected flows
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
Infra OU SCP limits cluster and network changes to pipeline roles. Project teams have no admin permission set here.

## TCSP defaults and options
Default: two AZs, private subnets only, access through Client VPN. Option: a separate shared account per environment tier if Prod builds must be fully isolated.

## Watch out for
An outage stops builds for every team, so it needs on-call ownership. Shared compute is a shared cost; charge back by namespace.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-sharedacct) · [Overview](../00-overview.md)
