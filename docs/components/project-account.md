# Project account

**Layer:** Delivery
**Lives in:** tcsp-prj-<name>-<env> · Project OU / Prod or Non-Prod
**Owned by:** Project team, within the guardrails

## What it is
An AWS account per project and environment, where a team builds and runs its application with admin rights inside the account.

## Why it exists
An account is the strongest isolation boundary AWS offers. A mistake or breach in one project cannot reach another's resources, and cost lands per account without tagging work.

## What it is built to accommodate
Any team size, from one service to many, with either delivery path: Path A on a project EKS cluster or Path B to ECS. Prod and Non-Prod are separate accounts. Accounts arrive with both baselines, a VPC attached to the Transit Gateway, observability and budgets already wired in.

## How it works
Inputs: an account request merged in Git. What it does: AFT and Control Tower create and baseline the account; the team then signs in as ProjectAdmin or ProjectDeveloper and deploys with its own IaC. Outputs: a running application whose logs, findings, telemetry and cost flow to central accounts.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.
- [Flow 4: Template deploy](../02-service-flows.md#4-template-deploy): Service Catalog deploys a best-practice template as the project's Development space.
- [Flow 6: Container delivery (Path A)](../02-service-flows.md#6-container-delivery-path-a): VCS → CI on shared EKS → image to ECR → CD on the project's EKS cluster.
- [Flow 7: AWS-native delivery (Path B)](../02-service-flows.md#7-aws-native-delivery-path-b): CodeCommit → CodeBuild → CodeDeploy → ECS inside one project account.
- [Flow 11: Sign-in](../02-service-flows.md#11-sign-in): Company IdP → IAM Identity Center → permission set role in the account.
- [Flow 12: Guardrail check](../02-service-flows.md#12-guardrail-check): SCPs on the OU path cap every API call, even an admin's.

## Guardrails that apply
Root and Project OU SCPs (Region allow-list, protected baseline, no network bypass, no public data), stricter Prod SCPs, the TF Baseline, policy-as-code on platform Terraform, CI scans, and central detection.

## TCSP defaults and options
Default: one account per project per environment, named tcsp-prj-<name>-<env>, project-standard customization set, budget alert at 80 percent. Option: a single account for very small internal tools that have no production users.

## Watch out for
Account sprawl without ownership tags; require an owner and cost centre in every request. Teams must still own application security: authorization, data handling, secrets and their own alerts.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-projacct) · [Overview](../00-overview.md)
