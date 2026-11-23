# CT Baseline

**Layer:** Governance
**Lives in:** every member account · all OUs
**Owned by:** Platform Engineering team

## What it is
What Control Tower installs in every account it creates or enrolls: the organization trail, the Config recorder, the execution roles and the controls enabled on the account's OU.

## Why it exists
Logging and recording must exist from the first minute of an account's life, before any team has touched it. The CT Baseline guarantees that without any project work.

## What it is built to accommodate
Every account type, new or enrolled, in every governed Region. It is deliberately small and AWS-managed, so it is the same everywhere. Settings that TCSP chooses for itself go in the TF Baseline instead.

## How it works
Inputs: the landing-zone configuration and the controls enabled per OU. What it does: Control Tower deploys and maintains the baseline stack sets in the account. Outputs: CloudTrail and Config data flowing to tcsp-log-archive, and roles that let Control Tower and AFT manage the account.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 8: Logging](../02-service-flows.md#8-logging): CloudTrail, Config and flow logs from every account land in Log Archive.

## Guardrails that apply
The 'protect the baseline' SCP denies changes to CloudTrail, Config and Control Tower roles. Drift is detected and reported by Control Tower.

## TCSP defaults and options
Default: managed by Control Tower, updated with each landing-zone version, rolled out OU by OU starting with a canary OU.

## Watch out for
Landing-zone updates touch every account. Re-registering an OU re-applies the baseline to all its accounts, which takes time in large OUs.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-ctbase) · [Overview](../00-overview.md)
