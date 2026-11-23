# Log Archive account

**Layer:** Security
**Lives in:** tcsp-log-archive · Security OU
**Owned by:** Security

## What it is
The account that stores every account's security logs in write-once S3 buckets: CloudTrail, Config history, VPC Flow Logs and other security logs.

## Why it exists
Audit evidence is only trustworthy if the people being audited cannot change it. A separate account with locked buckets keeps logs safe even if a project account is fully compromised.

## What it is built to accommodate
Logs from hundreds of accounts and every governed Region, kept for the retention your auditors require, with read access granted to investigators and tools only. It is not a log analytics platform; search and dashboards happen in the observability or SIEM tool that reads from it.

## How it works
Inputs: the organization trail, Config delivery channels and log forwarders in every account. What it does: stores objects in S3 with bucket policies that allow writes from the organization and deny reads, changes and deletes; Object Lock keeps them immutable. Outputs: a tamper-evident record for audits and investigations.

## Connected flows
- [Flow 8: Logging](../02-service-flows.md#8-logging): CloudTrail, Config and flow logs from every account land in Log Archive.

## Guardrails that apply
Security OU SCP denies deleting or changing log buckets, Object Lock and lifecycle settings except from a named break-glass role. RCPs limit bucket access to the organization.

## TCSP defaults and options
Default: S3 Object Lock in compliance mode, KMS encryption with a dedicated key, lifecycle to cheaper storage classes after 90 days. Option: replicate to a second Region for disaster recovery.

## Watch out for
Storage grows with every account and every log source, so set lifecycle rules early. Compliance-mode Object Lock cannot be shortened, so agree retention before turning it on.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-logarch) · [Overview](../00-overview.md)
