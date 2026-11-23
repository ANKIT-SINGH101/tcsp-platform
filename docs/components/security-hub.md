# Security Hub CSPM

**Layer:** Security
**Lives in:** tcsp-audit · Security OU (delegated admin)
**Owned by:** Security

## What it is
The cloud security posture service that checks every account against security standards and collects findings from GuardDuty, Config, Access Analyzer and other tools in one place.

## Why it exists
Findings spread across many services and accounts are hard to act on. One normalized list with severity and ownership makes triage and reporting possible.

## What it is built to accommodate
Central configuration of standards per OU, cross-Region aggregation, automation rules that route findings by account tag, and integration with ticketing and chat. It is a recommended addition to the core design.

## How it works
Inputs: findings from AWS services and third-party tools in every account. What it does: normalizes, deduplicates and scores them, runs security standard checks. Outputs: a prioritized findings list in tcsp-audit, sent on to security monitoring and to owning teams.

## Connected flows
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
Detective control (layer 5). Security OU SCP protects its configuration.

## TCSP defaults and options
Default: AWS Foundational Security Best Practices standard, central configuration, automation rules for routing. Option: replace or complement with a third-party CSPM.

## Watch out for
Controls that do not fit TCSP should be disabled centrally, not ignored. Cost scales with checks and findings.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-sechub) · [Overview](../00-overview.md)
