# Shared CI on EKS

**Layer:** Delivery
**Lives in:** tcsp-shared-services · Infra OU
**Owned by:** Platform Engineering team

## What it is
One CI system every project uses, so teams do not each build and secure their own runners. Build, test, source scan, image scan and SBOM generation run as pods on a shared EKS cluster.

## Why it exists
Per-team CI means duplicated effort, uneven security scanning and runners holding credentials in many places. A shared, hardened CI gives every team the same checks without the upkeep.

## What it is built to accommodate
Container workloads and teams that ship often. Many projects at once, each isolated by namespace and runner tags, with runners that scale with demand across two AZs. It hands off to each project's CD instead of deploying into project accounts itself, so it never holds project admin credentials.

## How it works
Inputs: a push or merge request in the shared VCS. What it does: schedules runner pods that build the image, run tests, scan source and dependencies, scan the image, create an SBOM and push the image to ECR; then it updates the image tag in the project's manifest repository. Outputs: a scanned, signed image and a manifest change for project CD to pull.

## Connected flows
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
This component carries the delivery-time guardrail layer (layer 4): builds fail on critical vulnerabilities or found secrets. Infra OU SCP limits cluster changes. Runners use per-namespace IAM roles with push rights only to their project's ECR repositories.

## TCSP defaults and options
Default: GitLab Runner or GitHub Actions Runner Controller, Trivy or a similar scanner, Checkov for IaC, an SBOM generator, Karpenter for node scaling. Option: dedicated runner node pools for Prod builds.

## Watch out for
Noisy neighbours: one team's heavy build can slow others, so set namespace quotas. Image caches and spot interruptions affect build times.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-ci) · [Overview](../00-overview.md)
