# HCP Terraform and policy as code

**Layer:** Provisioning
**Lives in:** SaaS, outside AWS (one workspace per account customization)
**Owned by:** Platform Engineering team, with Security for policies

## What it is
The Terraform engine that runs AFT's account customizations as workspaces, keeps remote state, and checks every plan against Sentinel or OPA policies before apply.

## Why it exists
Reviewing plans by eye does not scale to hundreds of accounts. A policy gate stops unsafe changes, such as a security group open to the internet on SSH, before they exist.

## What it is built to accommodate
Hundreds of workspaces with separate state, run history and policy results that feed security monitoring. It suits teams that want a managed Terraform service with a built-in policy gate. When licence cost matters more, open-source Terraform with OPA or Conftest in CodeBuild gives a similar gate with more to operate.

## How it works
Inputs: customization code from AFT and the policy set from the tcsp-policies repo. What it does: plans, runs the policy checks, stops on a hard failure, applies on pass. Outputs: changes in the member account (VPC, Transit Gateway attachment, tool hooks), state, and policy results sent to security monitoring.

## Connected flows
- [Flow 3: Additional customizations](../02-service-flows.md#3-additional-customizations): AFT hands account customizations to HCP Terraform, which checks policy and applies them into the account.
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
This component is the code-time guardrail layer (layer 3). Hard-mandatory policies cannot be overridden; soft-mandatory overrides are logged with a reason.

## TCSP defaults and options
Default: HCP Terraform with Sentinel or OPA policy sets, workspaces created by AFT, VCS-driven runs. Option: open-source Terraform with state in S3 in the AFT account and OPA or Conftest in CodeBuild.

## Watch out for
Cost grows with resources under management. Workspace sprawl needs naming rules. The SaaS dependency means an outage pauses customizations, though existing accounts keep running.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-hcp) · [Overview](../00-overview.md)
