# AFT management account

**Layer:** Provisioning
**Lives in:** tcsp-aft-management · AFT OU
**Owned by:** Platform Engineering team

## What it is
The account that runs Account Factory for Terraform (AFT): the pipelines, queue and state that turn a Git request into a governed account, plus the Service Catalog portfolio of best-practice templates.

## Why it exists
Account vending needs powerful roles. Putting that machinery in its own account, under its own OU and SCP, keeps those roles away from both the management account and project accounts.

## What it is built to accommodate
A steady stream of account requests and baseline changes, from a few a month to many a day, with requests queued rather than dropped. Use it only for AFT and the template portfolio. Do not run shared tools or CI here.

## How it works
Inputs: merges to the four AFT repositories. What it does: hosts CodePipeline, CodeBuild, Step Functions, Lambda, DynamoDB and SQS from the AFT module, and the cross-account roles AFT uses. Outputs: account requests sent to Control Tower, Terraform applied to member accounts, and template products shared to the Project OU.

## Connected flows
- [Flow 1: Platform change](../02-service-flows.md#1-platform-change): The Platform Engineering team pushes Terraform to the platform VCS; the merge triggers AFT.
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.
- [Flow 4: Template deploy](../02-service-flows.md#4-template-deploy): Service Catalog deploys a best-practice template as the project's Development space.

## Guardrails that apply
The AFT OU SCP denies everything except the AFT service roles and break-glass. Only the Platform Engineering team has a permission set here.

## TCSP defaults and options
Default: vended by Control Tower into the AFT OU during the build of the account factory, AFT module version pinned, Terraform state in this account when open-source Terraform is used.

## Watch out for
If this account is broken, no account can be created or updated. Upgrade the AFT module in a test organization first. Watch CodeBuild concurrency limits during large baseline rollouts.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-aftacct) · [Overview](../00-overview.md)
