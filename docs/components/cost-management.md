# Cost management

**Layer:** Observability
**Lives in:** tcsp-management (CUR 2.0, Cost Explorer) · budgets in every account
**Owned by:** Platform Engineering team, with Finance

## What it is
Cost reporting and control for TCSP: the Cost and Usage Report, Cost Explorer, a budget per account created by AFT, and a charge-back model for shared costs.

## Why it exists
Without per-project cost visibility, cloud spend surprises everyone at month end. Accounts per project make cost attribution simple; budgets make it visible early.

## What it is built to accommodate
Per-account and per-tag reporting, budgets and anomaly alerts per project, and fair splitting of shared costs such as Transit Gateway processing, NAT, observability ingest, Terraform runs and the shared CI cluster.

## How it works
Inputs: CUR 2.0 data, cost-allocation tags set at request time, budget definitions in the TF Baseline. What it does: reports spend by account, team and service; alerts on budget and anomalies. Outputs: monthly reports and charge-back figures.

## Connected flows
- [Flow 10: Telemetry and cost](../02-service-flows.md#10-telemetry-and-cost): Every account reports to observability and cost management.

## Guardrails that apply
Budgets and anomaly detection are created by the baseline; Region allow-list SCPs prevent spend in unexpected Regions.

## TCSP defaults and options
Default: CUR 2.0 to an S3 bucket, Cost Explorer, budgets at 80 and 100 percent, Cost Anomaly Detection per account. Option: an external FinOps tool reading the CUR.

## Watch out for
Shared costs need an agreed split before go-live or every month becomes a debate. Untagged shared resources make charge-back guesswork.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-cost) · [Overview](../00-overview.md)
