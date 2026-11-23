# OU structure

**Layer:** Governance
**Lives in:** tcsp-management · Root, Security, Infra, Project (Prod, Non-Prod, Migration) and AFT OUs
**Owned by:** Platform Engineering team

## What it is
The four top-level organizational units, Security, Infra, Project and AFT, plus Prod, Non-Prod and a temporary Migration OU under Project.

## Why it exists
Different kinds of accounts need different limits. Grouping them by purpose lets one policy set cover a whole group and keeps shared infrastructure apart from project workloads.

## What it is built to accommodate
Any number of project accounts without new policy work: a new account inherits everything by landing in the right OU. Prod and Non-Prod let production carry stricter rules. The Migration OU gives accounts arriving from another organization a checked landing spot before promotion. Add a new OU only when a group of accounts truly needs different rules, not for each team or department.

## How it works
Inputs: the target OU named in each account request. What it does: Control Tower places the account in that OU, and AWS Organizations applies the policies attached on the path from root to that OU. Outputs: an account whose guardrails match its purpose. Security OU holds tcsp-log-archive and tcsp-audit, Infra OU holds tcsp-network and tcsp-shared-services, AFT OU holds tcsp-aft-management, and Project OU holds every tcsp-prj account.

## Connected flows
- [Flow 2: Account vending](../02-service-flows.md#2-account-vending): AFT asks Control Tower to create the account; it lands in its OU with the CT Baseline, then AFT adds the TF Baseline.
- [Flow 12: Guardrail check](../02-service-flows.md#12-guardrail-check): SCPs on the OU path cap every API call, even an admin's.

## Guardrails that apply
Root SCPs apply to every OU. Security OU locks log and detector settings. Infra OU limits network and CI changes to pipeline roles. Project OU blocks network bypass and public data. Project / Prod adds stricter production rules. AFT OU allows only the factory roles.

## TCSP defaults and options
Default: Security, Infra, Project (with Prod, Non-Prod, Migration) and AFT. Option: a Sandbox OU with spending limits for short-lived experiments, and a Suspended OU with a deny-all SCP for accounts being closed.

## Watch out for
Deep or team-shaped OU trees multiply policy combinations that nobody can test. Moving an account changes its guardrails immediately, so treat OU moves as changes that go through review.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-ous) · [Overview](../00-overview.md)
