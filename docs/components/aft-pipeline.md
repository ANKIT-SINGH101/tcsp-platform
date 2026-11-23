# AFT pipeline

**Layer:** Provisioning
**Lives in:** tcsp-aft-management · AFT OU
**Owned by:** Platform Engineering team

## What it is
The automation inside the AFT account: CodePipeline and CodeBuild run Terraform, a DynamoDB table and an SQS FIFO queue hold account requests, Lambda talks to Control Tower, and Step Functions runs the provisioning framework.

## Why it exists
It replaces console clicks and hand-offs between teams with one repeatable path. The same steps run for every account, so the result does not depend on who handled the request.

## What it is built to accommodate
Many requests at once, retried safely from the queue; updates to existing accounts when a request or a customization changes; and re-applying customizations when an account moves between OUs. It is not a general CI system for applications.

## How it works
Inputs: a merged account request or a change to any customization repo. What it does: stores the request in DynamoDB, queues it in SQS FIFO, calls Control Tower Account Factory, then runs Step Functions to create the AFT execution role, tag the account, run provisioning customizations, apply the TF Baseline and the chosen account customizations. Outputs: a baselined account and a per-account customization pipeline.

## Connected flows
- [Flow 1: Platform change](../02-service-flows.md#1-platform-change): The Platform Engineering team pushes Terraform to the platform VCS; the merge triggers AFT.
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.

## Guardrails that apply
Terraform plans are checked by policy as code before apply. Plan-only mode lets reviewers see changes before they reach accounts. The AFT OU SCP limits who can change the pipeline.

## TCSP defaults and options
Default: plan-only runs on canary accounts before any baseline change goes wide, notifications to the platform channel on failure. Option: run customizations through HCP Terraform workspaces instead of CodeBuild.

## Watch out for
Failed runs can leave an account half-customized; Step Functions shows where it stopped. CodeBuild and Control Tower concurrency limits slow very large batches.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-aftpipe) · [Overview](../00-overview.md)
