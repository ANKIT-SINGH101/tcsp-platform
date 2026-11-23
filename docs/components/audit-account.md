# Audit account

**Layer:** Security
**Lives in:** tcsp-audit · Security OU
**Owned by:** Security

## What it is
The account where the security team sees findings from every account: it is the delegated administrator for GuardDuty, Detective, IAM Access Analyzer, Security Hub and the Config aggregator.

## Why it exists
Detection that is set up per team is uneven and invisible to security. One delegated administrator enrolls every account automatically and gives one place to triage.

## What it is built to accommodate
Organization-wide detection for hundreds of accounts, automatic enrollment of new accounts, read-only access into member accounts for investigation, and routing of findings to the owning team. It does not run workloads or fix application issues itself.

## How it works
Inputs: findings and compliance data from every account. What it does: aggregates GuardDuty, Detective, Access Analyzer, Config and Security Hub results, applies suppression rules, and sends findings to security monitoring and to team channels. Outputs: one prioritized view of risk across TCSP.

## Connected flows
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
Security OU SCP denies disabling detectors or changing delegated-admin settings except from break-glass. Only the SecurityAudit and Security admin permission sets reach this account.

## TCSP defaults and options
Default: auto-enable for all services on new accounts, findings routed by account tag to the owning team. Option: forward findings to an external SIEM or CSPM.

## Watch out for
Untuned findings bury real issues, so review suppression rules monthly. GuardDuty and Security Hub cost scales with accounts and data volume.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-audit) · [Overview](../00-overview.md)
