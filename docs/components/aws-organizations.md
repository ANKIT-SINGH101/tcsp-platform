# AWS Organizations

**Layer:** Governance
**Lives in:** tcsp-management · Organization root
**Owned by:** Platform Engineering team

## What it is
The AWS service that groups every TCSP account into one organization and a tree of OUs, and attaches policies to that tree.

## Why it exists
Without an organization each account is configured by hand and drifts. With one, a policy attached once to an OU applies to every account under it, including accounts created later.

## What it is built to accommodate
Hundreds of accounts, nested OUs up to five levels deep, and policy types that cover principals (SCPs), resources (RCPs) and service settings (declarative policies). Use it as the single place where account membership and inheritance are decided. It is not a place for per-application configuration.

## How it works
Inputs: OU definitions and policy documents from the tcsp-org repo. What it does: holds accounts in OUs, evaluates the policy chain from root to account on every request, enables trusted access and delegated administrators for organization-wide services. Outputs: inherited policies, an organization ID used in resource policies (aws:PrincipalOrgID), and consolidated billing.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 11: Sign-in](../02-service-flows.md#11-sign-in): Company IdP → IAM Identity Center → permission set role in the account.
- [Flow 12: Guardrail check](../02-service-flows.md#12-guardrail-check): SCPs on the OU path cap every API call, even an admin's.

## Guardrails that apply
Root SCPs deny leaving the organization (organizations:LeaveOrganization) and protect the baseline roles. Changes to the OU tree or to policies only come through the tcsp-org pipeline.

## TCSP defaults and options
Default: all features enabled, trusted access for Control Tower, IAM Identity Center, Config, GuardDuty, Detective, IAM Access Analyzer and Security Hub, with tcsp-audit as delegated administrator for the security services.

## Watch out for
Policy size and count limits per target (for example five SCPs per OU and 5,120 characters each) push you to merge statements, so keep policies in version control with tests. Moving an account between OUs changes its guardrails instantly.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-org) · [Overview](../00-overview.md)
