# Observability platform

**Layer:** Observability
**Lives in:** SaaS (for example New Relic, Datadog or Grafana Cloud) · integration roles in every account
**Owned by:** Platform Engineering team for the platform; project teams for their apps

## What it is
The tool where metrics, logs, traces and user-experience data from every account come together, with SLI and SLO dashboards per project.

## Why it exists
Teams cannot run what they cannot see, and the platform team cannot run shared parts blind. Wiring observability in at account creation means it works from day one.

## What it is built to accommodate
Platform monitoring (Transit Gateway, AFT, shared CI) and application monitoring per project, with each project in its own sub-account or workspace and admin rights delegated to the team. New Relic is one option among others.

## How it works
Inputs: CloudWatch metric streams, log forwarding and APM agents, set up by the TF Baseline. What it does: stores and correlates telemetry, alerts on SLOs, and links alerts to incident tooling. Outputs: dashboards that start from SLO scores and drill down to services and resources.

## Connected flows
- [Flow 10: Telemetry and cost](../02-service-flows.md#10-telemetry-and-cost): Every account reports to observability and cost management.

## Guardrails that apply
Integration roles are read-only and created by the TF Baseline. The 'protect the baseline' SCP keeps them in place.

## TCSP defaults and options
Default: integration in aft-global-customizations, dashboard templates from the Platform Engineering team, a licence model priced by users and data rather than per feature. Option: Amazon CloudWatch and Managed Grafana for an all-AWS setup.

## Watch out for
Ingest volume drives cost; set log sampling and retention per environment. Alert fatigue if every team copies default alerts.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-obs) · [Overview](../00-overview.md)
