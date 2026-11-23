# CD on project EKS (Path A)

**Layer:** Delivery
**Lives in:** each project account · Project OU
**Owned by:** Project team (the Platform Engineering team provides the template)

## What it is
A pull-based delivery tool, such as Argo CD, running in the project's own EKS cluster. It watches the manifest repository and deploys new image tags.

## Why it exists
If shared CI pushed into project accounts it would need admin credentials for every project. Pulling from inside the account keeps the account boundary intact and makes Git the record of what runs.

## What it is built to accommodate
Teams with many microservices or existing Kubernetes skills, progressive delivery (canary, blue/green with Argo Rollouts), and many environments driven from one repository. Small teams without Kubernetes skills are better served by Path B.

## How it works
Inputs: a manifest change made by shared CI or by an engineer. What it does: Argo CD detects the change, pulls the image from ECR and syncs the cluster to the manifest. Outputs: the new version running, with sync status and history in Git and in the observability platform.

## Connected flows
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
Admission policies (for example Kyverno or OPA Gatekeeper) block privileged pods and unsigned images. Project OU SCPs apply to the cluster's IAM roles. GuardDuty EKS and runtime protection watch the cluster.

## TCSP defaults and options
Default: EKS managed node groups or Karpenter across two AZs, Argo CD per cluster, image signature verification. Option: Flux instead of Argo CD.

## Watch out for
Cluster upgrades are the team's job and come every few months; the platform template should make them routine. Drift from manual kubectl changes is overwritten by sync.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-cd) · [Overview](../00-overview.md)
