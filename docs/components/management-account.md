# Management account

**Layer:** Governance
**Lives in:** tcsp-management · Organization root
**Owned by:** Platform Engineering team

## What it is
The account that owns the AWS organization: the OU tree, the organization policies, Control Tower, sign-in and consolidated billing. It runs no workloads.

## Why it exists
Every rule that no project may override needs a home that projects cannot reach. Keeping that home empty of workloads keeps its attack surface and its list of admins as small as possible.

## What it is built to accommodate
One organization of any size, from a handful of accounts to several hundred, with one payer and one place to set policy. Use it for organization-wide settings, billing and identity only. Do not use it for applications, CI runners, shared tools or experiments, even temporarily.

## How it works
Inputs: Terraform from the tcsp-org repo, applied by a narrow pipeline role, and Control Tower landing-zone settings. What it does: holds AWS Organizations, the OU tree, SCPs, RCPs and declarative policies, Control Tower, IAM Identity Center, the organization CloudTrail trail and the cost and usage report. Outputs: policies inherited by every member account, permission-set roles in every account, and one bill.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 10: Telemetry and cost](../02-service-flows.md#10-telemetry-and-cost): Every account reports to observability and cost management.
- [Flow 11: Sign-in](../02-service-flows.md#11-sign-in): Company IdP → IAM Identity Center → permission set role in the account.
- [Flow 12: Guardrail check](../02-service-flows.md#12-guardrail-check): SCPs on the OU path cap every API call, even an admin's.

## Guardrails that apply
SCPs never apply to the management account itself, so it is protected by access, not policy: only the PlatformAdmin and BreakGlass permission sets reach it, root user MFA is hardware-backed, and every sign-in raises an alert in tcsp-audit.

## TCSP defaults and options
Default: a fresh account created for TCSP that becomes the payer. Option: reuse an existing payer account only if it already has no workloads and a short, known admin list. Delegate Identity Center, Security Hub, GuardDuty and Config administration to other accounts so that day-to-day work happens outside it.

## Watch out for
Any workload placed here escapes every SCP. Too many people with access turns it into a single point of compromise. Billing commitments such as Savings Plans follow the payer, so plan payer changes with Finance.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-mgmt) · [Overview](../00-overview.md)
