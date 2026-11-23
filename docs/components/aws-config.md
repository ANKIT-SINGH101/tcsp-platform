# AWS Config and Config Aggregator

**Layer:** Security
**Lives in:** recorders in every account · aggregator in tcsp-audit
**Owned by:** Security

## What it is
Config records every resource change in every account and checks resources against rules. The aggregator in tcsp-audit shows compliance across all accounts and Regions in one view.

## Why it exists
You cannot prove compliance without a history of configuration. Config gives that history and turns drift into findings rather than surprises.

## What it is built to accommodate
Continuous recording for every governed Region, managed and custom rules, conformance packs per OU, and evidence for audits. It is also the detective half of Control Tower controls. It is not a preventive control; pair it with SCPs.

## How it works
Inputs: resource changes in every account. What it does: records configuration items, evaluates rules, delivers history to tcsp-log-archive and aggregates compliance in tcsp-audit. Outputs: compliance status, drift findings in Security Hub, and an audit trail of changes.

## Connected flows
- [Flow 8: Logging](../02-service-flows.md#8-logging): CloudTrail, Config and flow logs from every account land in Log Archive.
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
The 'protect the baseline' SCP denies stopping or deleting recorders and delivery channels.

## TCSP defaults and options
Default: recorder deployed by the CT Baseline, service-linked aggregator, conformance packs aligned to the TCSP security standard. Option: periodic recording for high-churn resource types to control cost.

## Watch out for
Cost follows the number of configuration items, so busy accounts (autoscaling, containers) can be expensive. Large-scale account moves can hit aggregator change limits.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-config) · [Overview](../00-overview.md)
