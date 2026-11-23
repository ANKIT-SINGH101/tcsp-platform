# Migration: bringing existing OUs and production accounts onto TCSP

Existing AWS accounts, including production ones, can join TCSP from another AWS organization (the **source organization**). The account move itself is now simple. The network, shared CI and organization-scoped permissions the accounts depend on do not move with them, so plan the migration around those dependencies rather than around the move.

## What AWS supports

- **Direct account transfers between organizations.** An account can move from one organization to another without a standalone period in between, so governance and consolidated billing stay in place throughout.
- **Control Tower-managed accounts** follow a documented order: unenroll from Control Tower in the source organization, transfer, then enroll in the destination organization. Preventive controls can keep applying while the account moves.
- **Access to both management accounts is required.** The source organization's administrators must take part. Agree this before anything else.
- Accounts created through Account Factory must be **at least four days old** before they can be transferred.
- Products that the source organization's Account Factory provisioned **stay in its Service Catalog** after the move; its administrators terminate them there.
- Moving many accounts at once can hit the **AWS Config limit of 1,000 accounts added or removed per week** across aggregators. Control Tower landing zone 4.0 or later avoids it with a service-linked aggregator.
- **AFT can re-apply customizations automatically when an account moves between OUs**, which makes promotion out of the Migration OU cheap.

Check the current AWS documentation before each wave (see [References](#references)); these capabilities change.

## What breaks when an account leaves the source organization

| Dependency in the source organization | Why it breaks | TCSP fix, ready before the move |
| --- | --- | --- |
| VPC attached to the source Transit Gateway | The TGW is shared through RAM to the source organization; the share stops when the account leaves | TCSP Transit Gateway live; temporary TGW peering between the two hubs during the wave; re-attach to the TCSP TGW, then drop the peering |
| Shared VCS and CI in the source organization | Pipelines run in the source organization's shared account | Repositories mirrored to the TCSP VCS and pipelines proven on TCSP CI before the wave |
| IAM Identity Center access | Assignments belong to the source organization | TCSP permission sets and group mappings created first; sign-in tested on day one |
| Policies using `aws:PrincipalOrgID` or OU paths | They name the source organization ID | Search every S3, KMS, ECR, SNS, SQS and Lambda policy; add the TCSP organization ID before, remove the old one after |
| Delegated administrator roles | An account that is a delegated administrator cannot leave as is | Deregister it in the source organization first |
| Logs and findings history | Stays in the source Log Archive and Audit accounts | Agree retention or export with the source organization for audit evidence |
| Savings Plans, Reserved Instances, Marketplace subscriptions, support plan | Billing moves to the TCSP payer | Finance plans the payer switch and commitment coverage |
| Overlapping CIDRs | Existing ranges may overlap other TCSP VPCs or on-premises networks | Check in IPAM before the wave; re-IP or NAT where they clash |

## OU mapping

Bring existing OUs in as child OUs under the TCSP Project OU, split into Prod and Non-Prod. Every account lands first in the temporary **Migration OU** under Project OU, which carries the root SCPs plus detective-only controls. There it is checked against the full TCSP guardrails, then promoted to Project / Prod or Project / Non-Prod once clean.

## Per-account runbook

1. **Inventory.** Owners, VPCs and CIDRs, TGW attachments, RAM shares, organization-ID conditions, delegated administrator roles, pipelines, monitoring.
2. **Prepare TCSP.** Permission sets, TGW peering, CI pipelines, IPAM allocation, and an `aft-account-request` file for the account so that AFT manages it after import.
3. **Dry-run the guardrails.** Compare the account's effective policies in the source organization with TCSP's. Use CloudTrail history to find any recent action a TCSP SCP would deny.
4. **Source side.** Deregister delegated administrator roles, then unenroll from Control Tower (disable directly applied baselines and controls, or move to an unmanaged OU).
5. **Transfer.** The TCSP management account sends the invitation, the account accepts it, and TCSP places it in the Migration OU.
6. **Enroll.** Enroll the account in TCSP Control Tower. `AWSControlTowerExecution` is recreated to trust the TCSP management account. AFT applies the TF Baseline and the account's customization set.
7. **Cut over.** Attach the VPC to the TCSP Transit Gateway, switch pipelines to TCSP CI, re-point observability and cost tags.
8. **Verify.** Application health in observability, logs arriving in `tcsp-log-archive`, findings in `tcsp-audit`, team sign-in working.
9. **Promote.** Move the account from the Migration OU to Project / Prod or Non-Prod. When the last account in the wave is done, remove the old organization-ID conditions and the TGW peering.

## Rollback

Direct transfers work in both directions, so a failed account can move back to the source organization with its administrators' agreement. Keep the source TGW attachment path and pipelines available until each wave passes verification.

## Waves

| Wave | Scope | Purpose |
| --- | --- | --- |
| 0 | One non-production account with few dependencies | Prove the runbook |
| 1 | Remaining non-production accounts | Build confidence and speed |
| 2 | Low-risk production accounts | First production moves, in change windows |
| 3 | Critical production accounts | In a change window, with the source organization's administrators on the call |

Rule for every wave: move the account first and the pipeline later. Teams keep the delivery path they use today until the account is stable on TCSP.

## References

- [AWS Organizations: direct account transfers between organizations](https://aws.amazon.com/about-aws/whats-new/2025/11/aws-organizations-direct-account-transfers)
- [AWS Control Tower: transfer an account to a different organization](https://docs.aws.amazon.com/controltower/latest/userguide/account-transfer.html)
- [AWS Control Tower: prerequisites for enrollment](https://docs.aws.amazon.com/controltower/latest/userguide/enrollment-prerequisites.html)
- [AWS Control Tower: enroll an existing AWS account](https://docs.aws.amazon.com/controltower/latest/userguide/enroll-account.html)
- [AFT account customizations](https://docs.aws.amazon.com/controltower/latest/userguide/aft-account-customization-options.html)
