# Open decisions and risks

## Open decisions

These choices shape the build and must be made in [Phase 0](03-build-plan.md#phase-0-decide).

| Decision | Options | Default if nobody decides | Decided by |
| --- | --- | --- | --- |
| Home and DR Region | Any two Regions close to users and data | The Region with most existing workloads, plus its nearest pair | Platform Engineering team, Security, Finance |
| Company IdP for IAM Identity Center | Entra ID, Okta, Google Workspace, others with SAML + SCIM | The current corporate directory | Identity team |
| VCS product | GitLab, GitHub Enterprise, CodeCommit | The VCS teams already use | Platform Engineering team |
| Terraform engine for AFT | HCP Terraform (managed, Sentinel/OPA) or open-source Terraform with OPA in CodeBuild | HCP Terraform, for the built-in policy gate | Platform Engineering team, Finance |
| Observability vendor | New Relic, Datadog, Grafana Cloud, CloudWatch + Managed Grafana | The vendor already licensed | Platform Engineering team, project leads |
| CI and CD tools for Path A | GitLab Runner or GitHub Actions Runner Controller; Argo CD or Flux | Match the VCS; Argo CD | Platform Engineering team |
| Direct Connect timing | Day one or later; VPN-only start | Site-to-Site VPN first, Direct Connect when traffic justifies it | Network owner |
| Log retention periods | Per log type and regulation | 1 year hot, 7 years archived, Object Lock in compliance mode | Security, Legal |
| Shared-cost split | By account, by usage, flat fee | By measured usage where available, flat fee otherwise | Finance |
| What TCSP stands for | | | Platform owner |

## Risks

| Risk | Effect | Likelihood | Mitigation |
| --- | --- | --- | --- |
| Shared parts fail (AFT, Transit Gateway, shared CI) | Many projects stop at once | Medium | Two AZs everywhere, on-call ownership, staged AFT and baseline rollouts, runbooks |
| An SCP blocks a real need | Teams wait; the platform team turns into a gatekeeper | High early on | Policy tests in CI, canary OU, time-limited exception process, Migration OU before promotion |
| A baseline change hits hundreds of accounts | One bad commit spreads widely | Medium | AFT plan-only runs, canary accounts, pinned module versions, staged rollout by OU |
| A production account breaks during migration | Outage | Medium | TGW peering bridge, waves, rollback by transfer back, change windows |
| The source organization's administrators are not available for the migration | Migration cannot start | Medium | Agree participation and a schedule before Phase 5 starts |
| SaaS costs grow (HCP Terraform, observability) | Budget overrun | Medium | Charge back per project, watch ingest volume, retention per environment |
| Platform knowledge sits with one or two people | Slow fixes, burnout | Medium | Pairing on AFT, network and policy changes; this documentation; on-call rotation |
| Teams bypass golden paths | Inconsistent security returns | Low to medium | Guardrails make bypass impossible for the dangerous cases; templates make the right way the fast way |

## Design gaps TCSP fills with standard AWS patterns

Some parts of the design have no single obvious answer and are filled here with common AWS patterns. Review each during Phase 0:

- full SCP and RCP JSON, and the exception process;
- Transit Gateway route tables and CIDR plan;
- egress and inspection design;
- DNS resolution between TCSP and on-premises;
- CI and CD tool choices;
- alert routing and on-call;
- log retention periods;
- the break-glass procedure.
