# IAM Identity Center and permission sets

**Layer:** Governance
**Lives in:** tcsp-management · Organization root (administration can be delegated)
**Owned by:** Platform Engineering team

## What it is
The only way people sign in to TCSP accounts. Users and groups come from the company identity provider, and permission sets turn group membership into roles in each account.

## Why it exists
Per-account IAM users and shared passwords are hard to audit and impossible to revoke quickly. One sign-in path tied to the company directory means joiners, movers and leavers are handled in one place.

## What it is built to accommodate
Thousands of users across hundreds of accounts, short-lived credentials for console and CLI, and group-based access that follows people as they change teams. Use it for all human access. Workloads and pipelines use IAM roles, not Identity Center.

## How it works
Inputs: users and groups synced from the company IdP over SCIM, SAML sign-in with MFA, and permission-set assignments from the tcsp-org repo. What it does: issues short-lived credentials for the role that matches the permission set in the chosen account. Outputs: a session in the target account, with every action logged under the user's identity.

## Connected flows
- [Flow 5: Engineer access](../02-service-flows.md#5-engineer-access): Client VPN through the Transit Gateway to the shared VCS and private resources.
- [Flow 11: Sign-in](../02-service-flows.md#11-sign-in): Company IdP → IAM Identity Center → permission set role in the account.

## Guardrails that apply
Root SCP denies creating IAM users and access keys in Prod. BreakGlass use raises an alert. Permission-set changes go only through the tcsp-org pipeline.

## TCSP defaults and options
Default permission sets: PlatformAdmin (Platform Engineering team, all accounts), SecurityAudit (Security, read-only everywhere), ProjectAdmin (team, own account), ProjectDeveloper (team, own account, no IAM), ReadOnly (approved readers), BreakGlass (two named people, alarmed on use). Option: attribute-based access control using IdP attributes for very large organizations.

## Watch out for
Assignments made by hand drift from the repo. Session length that is too long weakens revocation. If the IdP is down nobody can sign in, so keep a tested break-glass path.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-idc) · [Overview](../00-overview.md)
