# Build plan

Build TCSP from the top of the architecture down, and do not move a single production account until a pilot project has gone through the platform end to end. Each phase ends at a gate that can be tested.

| Phase | Name | Outcome | Exit gate |
| --- | --- | --- | --- |
| 0 | Decide | Foundational choices signed off | Decisions signed off by security, network and finance owners |
| 1 | Control plane | Organization, landing zone, sign-in, root policies | A test account is blocked by each SCP, and its logs and findings appear centrally |
| 2 | Account factory | AFT, baselines, policy as code, first template | A merge request alone creates a fully baselined account in hours, with no console clicks |
| 3 | Infra OU | Network and shared developer tools | An engineer on Client VPN reaches the VCS; a test project VPC reaches Shared but not another project |
| 4 | Golden path | Project customization set, Path A and Path B, external platforms | A pilot goes from ITSM request to a same-day production-style deploy |
| 5 | Migrate | Existing OUs and accounts on TCSP | Every wave verified; see [04-migration.md](04-migration.md) |
| 6 | Operate | Platform run as a product | Platform SLOs met for a quarter |

## Phase 0: Decide

- Home Region and DR Region.
- Company IdP for IAM Identity Center (SAML and SCIM).
- VCS product for the platform and for the Shared account (GitLab, GitHub Enterprise or CodeCommit).
- Terraform engine: HCP Terraform or open-source Terraform with OPA.
- IP address plan for IPAM, checked against on-premises ranges.
- OU tree and SCP starter set ([01-guardrails.md](01-guardrails.md)).
- Account naming (`tcsp-<role>`, `tcsp-prj-<name>-<env>`) and required tags.
- The ITSM request form.
- Order Direct Connect now if it is needed on day one: lead times are long.

**Gate:** decisions signed off by the security, network and finance owners.

## Phase 1: Control plane

1. Create a fresh management account; it becomes the payer.
2. Enable AWS Organizations with all features.
3. Deploy the Control Tower landing zone in the home Region. This creates the Security OU with `tcsp-log-archive` and `tcsp-audit`.
4. Connect IAM Identity Center to the company IdP and create the starter permission sets.
5. Create the Infra OU, the Project OU (with Prod, Non-Prod and Migration) and the AFT OU.
6. Attach the root SCPs and the first RCPs.
7. Make `tcsp-audit` the delegated administrator for GuardDuty, Detective, IAM Access Analyzer, Security Hub and Config.

**Gate:** a test account in a test OU is blocked by each SCP, and its logs and findings appear centrally.

## Phase 2: Account factory

1. Vend `tcsp-aft-management` through Control Tower into the AFT OU.
2. Deploy the AFT Terraform module there, version pinned.
3. Create the four AFT repositories and the `tcsp-org` and `tcsp-policies` repositories.
4. Connect HCP Terraform (or set up OPA in CodeBuild) and write the first policies with tests.
5. Write TF Baseline v1.
6. Build the Service Catalog portfolio with the first template: the Development space.

**Gate:** a merge request alone creates a fully baselined account in hours, with no console clicks.

## Phase 3: Infra OU

1. Vend `tcsp-network`: Transit Gateway and its four route tables, RAM share to the organization, IPAM pools, the egress VPC with NAT and Network Firewall, Route 53 Resolver endpoints and rules, Client VPN with native Transit Gateway attachment, and the corporate link.
2. Vend `tcsp-shared-services`: the shared VCS, the EKS CI cluster across two AZs, ECR with an organization-only pull policy.

**Gate:** an engineer on Client VPN reaches the VCS; a test project VPC reaches Shared but not another project.

## Phase 4: Golden path

1. Write the `project-standard` customization set: VPC from IPAM, TGW attachment, observability integration, budget.
2. Publish the Path A starter (EKS with Argo CD) and the Path B starter (CodePipeline to ECS) as Service Catalog products and template repositories.
3. Wire security monitoring, cost management and observability.
4. Run one or two **new** pilot projects through the whole path.

**Gate:** a pilot goes from ITSM request to a same-day production-style deploy. This is the platform's core design target.

## Phase 5: Migrate

Move existing OUs and production accounts onto TCSP in waves. See [04-migration.md](04-migration.md).

## Phase 6: Operate

- Platform SLOs for account vending time, CI availability and network availability.
- On-call for the Transit Gateway, AFT and shared CI.
- An SCP exception process with a time limit.
- Policy tests in CI for every policy repository.
- A quarterly baseline review with Security.
- A charge-back report for shared costs every month.

## Platform repositories

| Repository | Contents |
| --- | --- |
| `tcsp-org` | Terraform for OUs, SCPs, RCPs, declarative policies, delegated administrators, Identity Center permission sets (applied from the management account with a narrow role) |
| `aft-account-request` | One file per account |
| `aft-global-customizations` | TF Baseline |
| `aft-account-customizations` | `project-standard`, `network`, `shared-ci` sets |
| `aft-account-provisioning-customizations` | ITSM and CMDB hooks |
| `tcsp-network` | Transit Gateway, route tables, IPAM, egress, Client VPN, corporate link, DNS |
| `tcsp-shared-ci` | EKS cluster, runners, scanners, ECR |
| `tcsp-policies` | Sentinel or OPA policies with tests |
| `tcsp-templates` | Service Catalog products, Path A and Path B starter repositories |
