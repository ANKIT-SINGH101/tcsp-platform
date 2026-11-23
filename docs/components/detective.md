# Amazon Detective

**Layer:** Security
**Lives in:** tcsp-audit · Security OU (delegated admin)
**Owned by:** Security

## What it is
An investigation service that links GuardDuty findings, CloudTrail and VPC Flow Logs into graphs, so analysts can see what happened around a finding.

## Why it exists
A finding says something is wrong; an investigation needs context. Detective cuts the time spent joining logs by hand during an incident.

## What it is built to accommodate
Investigations that span accounts, with up to a year of behaviour graphs. Use it during triage of GuardDuty and Security Hub findings. It is not a detection source on its own.

## How it works
Inputs: GuardDuty findings, CloudTrail and VPC Flow Logs from member accounts. What it does: builds and keeps behaviour graphs per entity (role, IP, instance). Outputs: investigation views linked from each finding.

## Connected flows
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
Security OU SCP protects its configuration. Access is limited to the Security team.

## TCSP defaults and options
Default: enabled organization-wide from tcsp-audit, new accounts auto-enabled.

## Watch out for
Cost scales with ingested data. It only helps if analysts use it, so include it in incident runbooks.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-detective) · [Overview](../00-overview.md)
