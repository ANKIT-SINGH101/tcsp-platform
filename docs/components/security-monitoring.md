# Security monitoring

**Layer:** Observability
**Lives in:** tcsp-audit (Security Hub) and an optional external CSPM or SIEM
**Owned by:** Security

## What it is
The place where findings from the Audit account and policy results from HCP Terraform are triaged and routed to the team that must act.

## Why it exists
Findings that reach nobody fix nothing. Routing each finding to its owner with context turns detection into action.

## What it is built to accommodate
Findings from every account, policy-check results from Terraform runs, routing by account tag to team channels and tickets, and escalation for critical findings. It can be Security Hub alone or an external CSPM or SIEM fed by it.

## How it works
Inputs: Security Hub findings and Terraform policy results. What it does: deduplicates, prioritizes and routes; tracks time to fix. Outputs: tickets and alerts for owning teams, and reports for security leadership.

## Connected flows
- [Flow 9: Security findings](../02-service-flows.md#9-security-findings): GuardDuty, Detective, Access Analyzer and Config feed Audit, then security monitoring.

## Guardrails that apply
Part of the detective layer (layer 5). Its configuration is owned by Security.

## TCSP defaults and options
Default: Security Hub automation rules plus routing to the ITSM tool and team chat by owner tag. Option: an external CSPM or SIEM.

## Watch out for
Missing owner tags send findings nowhere; make the tag mandatory at account request time.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-secmon) · [Overview](../00-overview.md)
