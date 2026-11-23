# AWS Control Tower

**Layer:** Governance
**Lives in:** tcsp-management · Organization root
**Owned by:** Platform Engineering team

## What it is
The AWS service that sets up and runs the landing zone: it creates accounts through Account Factory, places them in OUs, and applies its own baseline and controls.

## Why it exists
Building a landing zone by hand is slow and error-prone. Control Tower gives every new account the same starting point, logs and controls without manual steps.

## What it is built to accommodate
Account creation at any pace AFT sends requests, enrollment of existing accounts, and preventive (SCP-based), detective (Config rule) and proactive (CloudFormation hook) controls managed per OU. It suits organizations that want AWS-managed landing-zone upgrades. It is not where account-specific Terraform runs; that is AFT's job.

## How it works
Inputs: an Account Factory request from AFT (name, email, OU, SSO user). What it does: creates the account, moves it to the target OU, deploys the CT Baseline (organization trail, Config recorder, execution roles) and enables controls. Outputs: a governed account and an 'account created' event that AFT picks up.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 8: Logging](../02-service-flows.md#8-logging): CloudTrail, Config and flow logs from every account land in Log Archive.

## Guardrails that apply
Root SCPs protect the AWSControlTowerExecution role and Control Tower resources in member accounts. Landing-zone drift is reported and must be repaired before the next update.

## TCSP defaults and options
Default: landing zone 4.0 or later in the home Region with one DR Region governed, organization CloudTrail and a service-linked Config aggregator. Option: enable proactive controls for teams that deploy with CloudFormation.

## Watch out for
Manual changes to Control Tower resources cause drift and block updates. Account creation is serialized, so large batches queue. Region deny settings interact with the Region allow-list SCP; keep them aligned.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-ct) · [Overview](../00-overview.md)
