# CloudTrail organization trail

**Layer:** Security
**Lives in:** trail in tcsp-management · bucket in tcsp-log-archive
**Owned by:** Security (operated by the Platform Engineering team)

## What it is
One trail that records every API call in every account and Region of the organization and writes it to the Log Archive bucket.

## Why it exists
Every investigation, audit and access review starts with who did what and when. An organization trail gives that record for every account without any team setting it up.

## What it is built to accommodate
All accounts, including those created in the future, management and data events where needed, and lake or SIEM queries on top. It also records the denied calls that show guardrails at work.

## How it works
Inputs: API activity across the organization. What it does: Control Tower creates the trail; it delivers log files to tcsp-log-archive with integrity validation. Outputs: an immutable event history.

## Connected flows
- [Flow 8: Logging](../02-service-flows.md#8-logging): CloudTrail, Config and flow logs from every account land in Log Archive.
- [Flow 12: Guardrail check](../02-service-flows.md#12-guardrail-check): SCPs on the OU path cap every API call, even an admin's.

## Guardrails that apply
The 'protect the baseline' SCP denies stopping or deleting the trail. Log Archive SCPs and Object Lock protect the files.

## TCSP defaults and options
Default: organization trail from Control Tower, log file validation on, KMS encryption. Option: CloudTrail Lake for SQL queries over events, and data events for sensitive S3 buckets.

## Watch out for
Data events can be expensive at high volume; turn them on selectively.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-trail) · [Overview](../00-overview.md)
