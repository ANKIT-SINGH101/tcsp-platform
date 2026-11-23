# Amazon ECR (shared registry)

**Layer:** Delivery
**Lives in:** tcsp-shared-services · Infra OU
**Owned by:** Platform Engineering team

## What it is
The container registry that stores images built by shared CI, readable only from accounts in the TCSP organization.

## Why it exists
Pulling images from public registries at deploy time is slow and risky. A private registry with scanning and an organization-only policy keeps what runs in TCSP known.

## What it is built to accommodate
One repository per service, image scanning on push, immutable tags, lifecycle rules, and pulls from project EKS and ECS clusters across accounts. Path B teams can also keep a registry in their own account. It is a recommended addition to the core design.

## How it works
Inputs: images pushed by CI runners. What it does: scans images, stores them with immutable tags, allows pulls by aws:PrincipalOrgID. Outputs: images that project clusters pull through VPC endpoints.

## Connected flows
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.

## Guardrails that apply
Repository policies limit pulls to the organization; RCPs back this up. Enhanced scanning findings flow to Security Hub.

## TCSP defaults and options
Default: immutable tags, enhanced scanning, lifecycle keeping the last 50 images per repository, pull-through cache for approved public images.

## Watch out for
Storage and cross-Region transfer cost grow with image count. Retagging old images bypasses scanning history, so keep tags immutable.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-ecr) · [Overview](../00-overview.md)
