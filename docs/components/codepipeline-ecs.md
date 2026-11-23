# CodePipeline to ECS (Path B)

**Layer:** Delivery
**Lives in:** each project account · Project OU
**Owned by:** Project team

## What it is
An AWS-native pipeline that runs entirely inside one project account: CodeCommit, CodeBuild, CodeDeploy and an ECS service on Fargate.

## Why it exists
Not every team wants to run Kubernetes. This path gives a managed container runtime and a managed pipeline with nothing shared to depend on.

## What it is built to accommodate
Small teams with a few services, no Kubernetes skills and a wish for fewer moving parts. Blue/green deployments roll back automatically on failed health checks. Teams with many services or existing Kubernetes practice are better on Path A.

## How it works
Inputs: a push to CodeCommit (or a connection to the shared VCS). What it does: CodePipeline runs CodeBuild to build, test and scan, pushes the image to the project's ECR, and CodeDeploy shifts traffic to the new task set. Outputs: a new version on ECS with automatic rollback.

## Connected flows
- [Flow 7: AWS-native delivery (Path B)](../02-service-flows.md#7-aws-native-delivery-path-b): CodeCommit → CodeBuild → CodeDeploy → ECS inside one project account.

## Guardrails that apply
Project OU SCPs, the TF Baseline, scans in CodeBuild, ECR image scanning, and the same detection as every account.

## TCSP defaults and options
Default: Fargate, blue/green with CodeDeploy, Path B starter from Service Catalog. Option: source from the shared VCS through a CodeConnections link instead of CodeCommit.

## Watch out for
Each team maintains its own pipeline, so keep the starter template updated. Fargate cost is higher per vCPU than EC2 at large steady scale.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-pathb) · [Overview](../00-overview.md)
