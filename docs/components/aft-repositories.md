# Platform VCS and the four AFT repositories

**Layer:** Provisioning
**Lives in:** Platform VCS (outside AWS, or in tcsp-shared-services)
**Owned by:** Platform Engineering team

## What it is
The Git repositories that define the platform: aft-account-request, aft-account-provisioning-customizations, aft-global-customizations and aft-account-customizations, alongside tcsp-org, tcsp-network and tcsp-policies.

## Why it exists
When every account and setting is a file in Git, review and merge replace separate approval tickets, history is the audit trail, and rollback is a revert.

## What it is built to accommodate
One small file per account request, provisioning hooks (for example registering the account in a CMDB or updating the ITSM ticket), one global baseline, and named customization sets such as project-standard, network and shared-ci. Settings specific to one application stay in that team's own repository.

## How it works
Inputs: merge requests from the Platform Engineering team or from an ITSM automation. What it does: on merge, a webhook starts the AFT pipeline for the changed repository. Outputs: new or updated accounts, with the merge request as the record of who approved what.

## Connected flows
- [Flow 1: Platform change](../02-service-flows.md#1-platform-change): The Platform Engineering team pushes Terraform to the platform VCS; the merge triggers AFT.
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.

## Guardrails that apply
Branch protection with required reviewers, policy-as-code checks on the Terraform plan, and signed commits on the main branch.

## TCSP defaults and options
Default: GitLab or GitHub Enterprise with protected main branches and CODEOWNERS per repo. Option: AWS CodeCommit when the platform must stay entirely inside AWS.

## Watch out for
Direct pushes to main bypass review. A malformed account request can block the queue, so validate request files in a pre-merge check.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-aftrepos) · [Overview](../00-overview.md)
