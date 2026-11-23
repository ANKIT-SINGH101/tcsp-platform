# SCPs, RCPs and declarative policies

**Layer:** Governance
**Lives in:** tcsp-management · attached to Root and to each OU
**Owned by:** Platform Engineering team, with Security

## What it is
Organization policies that set the outer limit of what can happen in a member account. SCPs cap what principals may do, RCPs cap what may be done to resources, and declarative policies fix service settings across the organization.

## Why it exists
They are what makes 'guardrails, not gates' possible. Teams can hold full admin rights inside their account because a small set of organization policies blocks the few actions that would break shared security.

## What it is built to accommodate
Hard, organization-wide limits that must hold even for an account admin: approved Regions, protected logging and detection, no network bypass, no public data, stricter production rules. Use them for rules that are simple, universal and rarely excepted. Do not use them for fine-grained, per-application permissions, which belong in IAM inside the account.

## How it works
Inputs: policy JSON in the tcsp-org repo with unit tests. What it does: on every API call in a member account, AWS evaluates IAM and then every SCP and RCP on the OU path; any explicit deny wins. Declarative policies set the desired configuration (for example block public access for EBS snapshots) and keep it. Outputs: denied calls logged in CloudTrail, and a predictable outer boundary for every account.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 12: Guardrail check](../02-service-flows.md#12-guardrail-check): SCPs on the OU path cap every API call, even an admin's.

## Guardrails that apply
This component is the preventive guardrail layer (layer 1). The starter set and its OU targets are listed in docs/01-guardrails.md.

## TCSP defaults and options
Default: Region allow-list, protect the baseline, deny root user, deny leaving the organization at Root; OU-specific SCPs per the starter set; RCPs that limit S3, KMS and Secrets Manager access to the organization; declarative policies for EBS snapshot and AMI public access. Option: run a new SCP in a test OU with canary accounts for one week before attaching it widely.

## Watch out for
An SCP that is too broad blocks real work and turns the platform team into a gatekeeper. Keep a time-limited exception process. Character and attachment limits apply. SCPs do not apply to the management account or to service-linked roles.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-scp) · [Overview](../00-overview.md)
