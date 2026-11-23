# TF Baseline

**Layer:** Governance
**Lives in:** every member account · all OUs (applied from aft-global-customizations)
**Owned by:** Platform Engineering team

## What it is
The Terraform baseline that AFT applies to every account after Control Tower creates it: the TCSP security defaults, alarms, and the hooks for observability and cost.

## Why it exists
Security settings written as code in one baseline replace per-team checklists and tickets. Every account starts with the same safe defaults, and a fix to the baseline reaches every account.

## What it is built to accommodate
Defaults that every account needs regardless of workload: S3 Block Public Access, EBS default encryption, IAM password policy, default-VPC removal, IMDSv2 defaults, security alarms, observability integration roles, metric streams and a budget. Project-specific settings do not belong here; they go in account customizations or the team's own IaC.

## How it works
Inputs: the aft-global-customizations repo, versioned modules. What it does: AFT's pipeline runs terraform plan and apply against each account, on creation and on every change to the repo. Outputs: an account that matches the baseline, with state kept per account.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 10: Telemetry and cost](../02-service-flows.md#10-telemetry-and-cost): Every account reports to observability and cost management.

## Guardrails that apply
Plans are checked by Sentinel or OPA policies before apply. The 'protect the baseline' SCP stops teams from undoing the settings it creates. Config rules detect drift.

## TCSP defaults and options
Default: pinned module versions, plan-only runs on a canary account set first, then a staged rollout by OU. Option: split the baseline into a security module and an integrations module so that observability changes cannot block a security fix.

## Watch out for
One bad commit reaches every account, so stage rollouts and use plan-only runs. Long baselines make account creation slower. Keep secrets out of it.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-tfbase) · [Overview](../00-overview.md)
