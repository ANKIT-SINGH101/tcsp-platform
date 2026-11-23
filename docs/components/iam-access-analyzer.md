# IAM Access Analyzer

**Layer:** Security
**Lives in:** tcsp-audit · Security OU (delegated admin)
**Owned by:** Security

## What it is
A service that flags resources shared outside the organization and access that is granted but never used.

## Why it exists
Accidental public or cross-organization sharing is one of the most common cloud data exposures. Access Analyzer finds it continuously instead of at audit time.

## What it is built to accommodate
An organization-wide external-access analyzer with the organization as the zone of trust, plus unused-access analysis to shrink roles over time, and policy validation in CI. Use it in every account; it needs no agents.

## How it works
Inputs: resource policies (S3, KMS, IAM roles, Lambda, SQS, Secrets Manager and others) and access activity. What it does: reasons about who can reach each resource. Outputs: findings in tcsp-audit and Security Hub, and policy checks usable in pipelines.

## Connected flows
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
Detective control (layer 5); also usable at code time to validate policies before merge.

## TCSP defaults and options
Default: organization analyzer in tcsp-audit, unused-access analyzer for production accounts, archive rules for approved cross-account shares.

## Watch out for
Approved sharing needs archive rules or findings pile up. Unused-access analysis is priced per role analysed.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-iaa) · [Overview](../00-overview.md)
