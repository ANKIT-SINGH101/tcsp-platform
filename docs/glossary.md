# Glossary

| Term | Meaning in TCSP |
| --- | --- |
| **Account per project** | Each project and environment gets its own AWS account, the strongest isolation boundary AWS offers. |
| **AFT** | Account Factory for Terraform. The AWS-provided Terraform module and pipeline that turns a Git request into a Control Tower account and applies customizations. Runs in `tcsp-aft-management`. |
| **AFT repositories** | `aft-account-request`, `aft-account-provisioning-customizations`, `aft-global-customizations` and `aft-account-customizations`. |
| **Audit account** | `tcsp-audit`. Delegated administrator for the organization-wide security services. |
| **Break-glass** | A tightly controlled emergency role, used by two named people, alarmed on every use. |
| **CD** | Continuous delivery. In Path A, a pull-based tool such as Argo CD inside each project's EKS cluster. |
| **CI** | Continuous integration. In TCSP, the shared CI runners on EKS in the Shared account. |
| **Control Tower** | The AWS service that sets up and runs the landing zone and creates accounts. |
| **CSPM** | Cloud security posture management. Security Hub CSPM, or an external tool. |
| **CT Baseline** | What Control Tower installs in every account: organization trail, Config recorder, execution roles, OU controls. |
| **CUR 2.0** | The AWS Cost and Usage Report, the raw data for cost management and charge-back. |
| **Declarative policy** | An organization policy that fixes a service setting (for example, block public EBS snapshots) across accounts. |
| **Delegated administrator** | A member account allowed to administer an organization-wide service, so the management account stays quiet. |
| **Design target** | A goal TCSP is designed to meet, not a measured result. |
| **Development space** | A pre-approved sandbox VPC and toolset launched from Service Catalog inside a project account. |
| **Egress VPC** | The central VPC in `tcsp-network` with NAT Gateways and Network Firewall for outbound traffic. |
| **Golden path** | The supported, templated way to do a common task (Development space, Path A, Path B). |
| **Guardrails, not gates** | Teams get admin inside their account; organization policies set hard limits outside it, instead of approvals for each change. |
| **HCP Terraform** | HashiCorp's managed Terraform service, used for AFT customizations with Sentinel or OPA policy checks. |
| **IAM Identity Center** | The single sign-in path for people, connected to the company IdP. |
| **IdP** | Identity provider: the company directory used for sign-in (SAML) and group sync (SCIM). |
| **IPAM** | VPC IP Address Manager. Hands out non-overlapping CIDRs. |
| **ITSM** | IT service management tool where teams request accounts. |
| **Landing zone** | A governed, multi-account AWS environment with logging, security and networking set up. |
| **Log Archive account** | `tcsp-log-archive`. Write-once storage for every account's security logs. |
| **Management account** | `tcsp-management`. Owns the organization, policies, sign-in and billing. Runs no workloads. |
| **Migration OU** | Temporary OU under Project OU where accounts joining from another organization land and are checked before promotion. |
| **OPA** | Open Policy Agent. Policy-as-code engine usable instead of Sentinel. |
| **OU** | Organizational unit. A group of accounts that share policies. |
| **Path A** | Container delivery: shared CI on EKS, CD on the project's EKS cluster. |
| **Path B** | AWS-native delivery: CodePipeline, CodeBuild and CodeDeploy to ECS inside one project account. |
| **Permission set** | An Identity Center template that becomes an IAM role in each assigned account (for example ProjectAdmin). |
| **Platform Engineering team** | The team that builds and runs TCSP's shared parts and changes them only through Git. |
| **Platform VCS** | The Git service that holds the platform repositories. |
| **Project account** | `tcsp-prj-<name>-<env>`. Where a team builds and runs its application. |
| **RAM** | AWS Resource Access Manager. Shares the Transit Gateway, IPAM pools and Resolver rules with the organization. |
| **RCP** | Resource control policy. Caps what can be done to resources such as S3 buckets and KMS keys, whoever the caller is. |
| **SBOM** | Software bill of materials. A list of everything inside an image, produced by CI. |
| **SCP** | Service control policy. Caps what principals in member accounts can do, even admins. |
| **Sentinel** | HashiCorp's policy-as-code language, used in HCP Terraform. |
| **Shared account** | `tcsp-shared-services`. Hosts the shared VCS, CI and ECR. |
| **SLI / SLO** | Service level indicator / objective. The starting point of TCSP dashboards. |
| **TF Baseline** | The Terraform baseline AFT applies to every account from `aft-global-customizations`. |
| **Transit Gateway (TGW)** | The routing hub in `tcsp-network` that every VPC attaches to. |
