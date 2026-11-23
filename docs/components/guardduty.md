# Amazon GuardDuty

**Layer:** Security
**Lives in:** detectors in every account · delegated admin in tcsp-audit
**Owned by:** Security

## What it is
Threat detection that watches CloudTrail, VPC Flow Logs, DNS logs and runtime signals in every account for signs of compromise.

## Why it exists
Prevention never catches everything. GuardDuty spots behaviour that only shows up at runtime, such as crypto-mining, credential misuse or calls from known-bad addresses.

## What it is built to accommodate
Every account and Region, enabled organization-wide so new accounts join automatically, with optional protection plans for S3, EKS, runtime, RDS and Lambda. Turn on the EKS and runtime plans for accounts running Path A clusters.

## How it works
Inputs: AWS-managed log sources, no agents needed for the base service. What it does: analyses them with threat intelligence and anomaly detection. Outputs: findings sent to the delegated admin in tcsp-audit and on to Security Hub.

## Connected flows
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
This is a detective control (layer 5). Root and Security OU SCPs deny disabling detectors or removing members.

## TCSP defaults and options
Default: enabled in all governed Regions with S3 and EKS protection; runtime monitoring for Path A clusters. Option: malware protection for EBS on production accounts.

## Watch out for
Cost follows log volume; high-traffic accounts cost more. Findings in shared accounts need clear ownership.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-guardduty) · [Overview](../00-overview.md)
