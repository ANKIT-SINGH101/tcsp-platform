# Service flows

Twelve flows connect every part of TCSP. The numbers and names match the numbered badges and the flow list in the [interactive architecture](../site/index.html), where each flow can be played step by step (for example [`#flow-2`](../site/index.html#flow-2)).

Flows 1 to 4 build accounts, 5 to 7 ship software, 8 to 10 watch it, and 11 and 12 control who can do what.

| # | Flow | Path through the services | Owner |
| --- | --- | --- | --- |
| 1 | [Platform change](#1-platform-change) | Platform Engineering team → push .tf to platform VCS → merge request reviewed and merged → webhook triggers AFT CodePipeline / CodeBuild | Platform Engineering team |
| 2 | [Account vending](#2-account-vending) | AFT (DynamoDB → SQS → Lambda) → Control Tower Account Factory → account created in target OU with CT Baseline → "created" back to AFT → Step Functions runs provisioning and global customizations (TF Baseline) | Platform Engineering team |
| 3 | [Additional customizations](#3-additional-customizations) | AFT → HCP Terraform workspace → Sentinel / OPA policy check → apply into the member account (VPC from IPAM, TGW attachment, tool hooks) | Platform Engineering team |
| 4 | [Template deploy](#4-template-deploy) | Service Catalog portfolio in the AFT account → shared to Project OU → team launches the best-practice template → Development space | Project team |
| 5 | [Engineer access](#5-engineer-access) | Engineer → IdP-authenticated Client VPN → Transit Gateway (Access route table) → shared VCS or private project resources | Platform Engineering team (network) |
| 6 | [Container delivery (Path A)](#6-container-delivery-path-a) | Push to shared VCS → CI runners on shared EKS (build, test, scan, SBOM) → image to ECR → manifest update → CD on project EKS pulls and deploys | Shared CI: Platform Engineering team. CD and app: project team |
| 7 | [AWS-native delivery (Path B)](#7-aws-native-delivery-path-b) | CodeCommit → CodePipeline → CodeBuild → ECR → CodeDeploy blue/green → ECS service | Project team |
| 8 | [Logging](#8-logging) | Organization CloudTrail + Config recorders + VPC Flow Logs in every account → S3 in Log Archive (Object Lock) | Security (operated by the Platform Engineering team) |
| 9 | [Security findings](#9-security-findings) | GuardDuty, Detective, Access Analyzer, Config rules in every account → delegated admin in Audit → Security Hub / CSPM → alert to the owning team | Security detects; the project team fixes application issues |
| 10 | [Telemetry and cost](#10-telemetry-and-cost) | Agents and metric streams → observability platform (SLI / SLO dashboards); CUR 2.0 → cost tool; budgets per account | Shared: Platform Engineering team for the platform, project teams for their apps |
| 11 | [Sign-in](#11-sign-in) | Company IdP → IAM Identity Center → permission set → role in the target account | Platform Engineering team |
| 12 | [Guardrail check](#12-guardrail-check) | Every API call in a member account → IAM allow? → SCP / RCP on its OU path allow? → only then runs | Automatic |

## 1. Platform change

Every change to the platform, from a new account to a new SCP, starts as a merge request. Review and merge are the approval.

1. A request arrives through ITSM. The Platform Engineering team writes it as a short Terraform file.
2. They push the change to the platform VCS and open a merge request.
3. Review and merge are the approval; there is no separate ticket chain.
4. The merge webhook starts the AFT pipeline in the AFT management account.
5. CodeBuild runs the Terraform plan and apply for the repository that changed.

**Owner:** Platform Engineering team  
**Components:** [AFT management account](components/aft-management-account.md), [AFT pipeline](components/aft-pipeline.md), [Platform VCS and the four AFT repositories](components/aft-repositories.md), [Platform Engineering team](components/platform-engineering-team.md)  
**Play it:** [site/index.html#flow-1](../site/index.html#flow-1)

## 2. Account vending

This is the flow that turns an approved request into a governed account without anyone clicking in the console.

1. The merged account request is stored in DynamoDB and waits its turn in an SQS FIFO queue.
2. AFT asks Control Tower Account Factory to create the account.
3. Control Tower creates the account in its target OU, here Project / Non-Prod, and applies the CT Baseline.
4. Control Tower reports the account as created back to AFT.
5. Step Functions runs the provisioning framework: execution role, tags and provisioning customizations.
6. AFT applies the TF Baseline to the new account with Terraform. The account is ready.

**Owner:** Platform Engineering team  
**Components:** [Management account](components/management-account.md), [AWS Organizations](components/aws-organizations.md), [OU structure](components/ou-structure.md), [SCPs, RCPs and declarative policies](components/service-control-policies.md), [AWS Control Tower](components/control-tower.md), [CT Baseline](components/ct-baseline.md), [TF Baseline](components/tf-baseline.md), [AFT management account](components/aft-management-account.md), [AFT pipeline](components/aft-pipeline.md), [Platform VCS and the four AFT repositories](components/aft-repositories.md), [Project account](components/project-account.md), [Platform Engineering team](components/platform-engineering-team.md)  
**Play it:** [site/index.html#flow-2](../site/index.html#flow-2)

## 3. Additional customizations

After the baseline, each account gets its named customization set, checked by policy as code before anything is applied.

1. AFT sends the account's customization set, for example project-standard, to an HCP Terraform workspace.
2. Terraform plans the change and Sentinel or OPA policies check it. A failed check stops the run.
3. On a pass, HCP Terraform applies the customizations into the project account.
4. The project VPC gets a non-overlapping address range from VPC IPAM.
5. The VPC attaches to the Transit Gateway and joins the right route table.

**Owner:** Platform Engineering team  
**Components:** [AFT management account](components/aft-management-account.md), [AFT pipeline](components/aft-pipeline.md), [Platform VCS and the four AFT repositories](components/aft-repositories.md), [HCP Terraform and policy as code](components/hcp-terraform.md), [Network account](components/network-account.md), [Transit Gateway and route tables](components/transit-gateway.md), [VPC IPAM](components/vpc-ipam.md), [Project account](components/project-account.md), [Platform Engineering team](components/platform-engineering-team.md)  
**Play it:** [site/index.html#flow-3](../site/index.html#flow-3)

## 4. Template deploy

Teams start from approved templates instead of designing a network and toolset from scratch.

1. The best-practice template portfolio is shared from the AFT account to the Project OU.
2. The project team launches the Development space product from its own account.
3. A pre-approved VPC and toolset appears, inside the account guardrails and ready to use.

**Owner:** Project team  
**Components:** [AFT management account](components/aft-management-account.md), [Service Catalog best-practice templates](components/service-catalog.md), [Development space](components/development-space.md), [Project account](components/project-account.md)  
**Play it:** [site/index.html#flow-4](../site/index.html#flow-4)

## 5. Engineer access

Workloads are private by default. Engineers reach them through one authenticated, logged path.

1. An engineer connects with Client VPN, signing in through the company identity provider.
2. Client VPN hands the traffic to the Transit Gateway.
3. The Access route table allows only the networks that the engineer's group is authorized for.
4. Route 53 Resolver answers private names, such as the VCS host and corporate domains.
5. The engineer reaches the shared VCS and other private resources.

**Owner:** Platform Engineering team (network)  
**Components:** [IAM Identity Center and permission sets](components/iam-identity-center.md), [Network account](components/network-account.md), [Transit Gateway and route tables](components/transit-gateway.md), [AWS Client VPN](components/client-vpn.md), [Direct Connect and Site-to-Site VPN](components/direct-connect.md), [Route 53 Resolver](components/route53-resolver.md), [Shared account](components/shared-account.md), [Shared VCS](components/shared-vcs.md)  
**Play it:** [site/index.html#flow-5](../site/index.html#flow-5)

## 6. Container delivery (Path A)

The container path: one shared, hardened CI, and a pull-based CD inside each project account.

1. An engineer pushes code to the project's repository in the shared VCS.
2. The push starts a CI job on the shared EKS cluster.
3. Runner pods build the image, run tests, scan source and image, and create an SBOM.
4. The scanned image is pushed to ECR, readable only inside the organization.
5. CI updates the image tag in the manifest repository. It never deploys into the project account itself.
6. Argo CD in the project's EKS cluster pulls the change and deploys the new version.

**Owner:** Shared CI: Platform Engineering team. CD and app: project team  
**Components:** [Transit Gateway and route tables](components/transit-gateway.md), [Egress VPC (NAT + Network Firewall)](components/egress-vpc.md), [Shared account](components/shared-account.md), [Shared VCS](components/shared-vcs.md), [Shared CI on EKS](components/shared-ci-eks.md), [Amazon ECR (shared registry)](components/ecr.md), [Project account](components/project-account.md), [CD on project EKS (Path A)](components/cd-project-eks.md)  
**Play it:** [site/index.html#flow-6](../site/index.html#flow-6)

## 7. AWS-native delivery (Path B)

The managed path for teams that do not want to run Kubernetes. Everything stays inside one project account.

1. Code is pushed to CodeCommit in the project account.
2. CodePipeline starts CodeBuild to build, test and scan, and to push the image to the project's ECR.
3. CodeDeploy receives the new task definition.
4. CodeDeploy shifts traffic to the new ECS task set, blue/green.
5. If health checks fail, traffic shifts back automatically.

**Owner:** Project team  
**Components:** [Project account](components/project-account.md), [CodePipeline to ECS (Path B)](components/codepipeline-ecs.md)  
**Play it:** [site/index.html#flow-7](../site/index.html#flow-7)

## 8. Logging

Audit evidence is collected from the first minute of every account's life, into storage nobody can quietly change.

1. The organization trail records every API call in every account and Region.
2. Config recorders in every account record each resource change.
3. Both deliver to S3 buckets in the Log Archive account.
4. VPC Flow Logs and other security logs from project, network and shared accounts arrive there too.
5. Object Lock keeps logs write-once: accounts can write, but nobody can read, change or delete them without break-glass.

**Owner:** Security (operated by the Platform Engineering team)  
**Components:** [AWS Control Tower](components/control-tower.md), [CT Baseline](components/ct-baseline.md), [Log Archive account](components/log-archive-account.md), [AWS Config and Config Aggregator](components/aws-config.md), [CloudTrail organization trail](components/cloudtrail-org-trail.md)  
**Play it:** [site/index.html#flow-8](../site/index.html#flow-8)

## 9. Security findings

Detection is switched on for every account automatically, and every finding has somewhere to go.

1. GuardDuty, Access Analyzer and Config rules evaluate every account continuously.
2. Findings from every account go to the delegated administrators in the Audit account.
3. Security Hub collects, scores and deduplicates them. Detective helps investigate.
4. Security monitoring receives the findings, together with Terraform policy results.
5. Each finding is routed to the owning team by account tag, and Security tracks it to closure.

**Owner:** Security detects; the project team fixes application issues  
**Components:** [HCP Terraform and policy as code](components/hcp-terraform.md), [Audit account](components/audit-account.md), [Amazon GuardDuty](components/guardduty.md), [Amazon Detective](components/detective.md), [AWS Config and Config Aggregator](components/aws-config.md), [IAM Access Analyzer](components/iam-access-analyzer.md), [Security Hub CSPM](components/security-hub.md), [Security monitoring](components/security-monitoring.md)  
**Play it:** [site/index.html#flow-9](../site/index.html#flow-9)

## 10. Telemetry and cost

Observability and cost tracking are wired in by the baseline, so they work on day one.

1. The TF Baseline installs read-only observability roles, metric streams and a budget in every account.
2. Metrics, logs and traces stream to the observability platform.
3. Dashboards start from SLI and SLO scores and drill down to services and resources.
4. Cost and usage data, tagged per account, flows to cost management.
5. Budgets and anomaly alerts warn the project team; shared costs are charged back.

**Owner:** Shared: Platform Engineering team for the platform, project teams for their apps  
**Components:** [Management account](components/management-account.md), [TF Baseline](components/tf-baseline.md), [Observability platform](components/observability-platform.md), [Cost management](components/cost-management.md)  
**Play it:** [site/index.html#flow-10](../site/index.html#flow-10)

## 11. Sign-in

One sign-in path for every person and every account, tied to the company directory.

1. An engineer signs in with the company identity provider and MFA. Groups sync to Identity Center over SCIM.
2. They pick an account and a permission set such as ProjectAdmin. Identity Center issues short-lived credentials for that role.
3. The session runs as a role in that account only, and every action is logged under the engineer's own name.

**Owner:** Platform Engineering team  
**Components:** [Management account](components/management-account.md), [AWS Organizations](components/aws-organizations.md), [IAM Identity Center and permission sets](components/iam-identity-center.md), [Project account](components/project-account.md)  
**Play it:** [site/index.html#flow-11](../site/index.html#flow-11)

## 12. Guardrail check

Admin inside your account, hard limits outside it. This is how the two fit together on every single call.

1. A ProjectAdmin calls an AWS API, for example to create an Internet Gateway. IAM allows it: the role is admin.
2. AWS then evaluates the SCPs and RCPs on the account's OU path: Non-Prod, Project, then Root.
3. The Project OU SCP denies Internet Gateways outside the platform role, so the call is denied.
4. Root policies, such as the Region allow-list and the protected baseline, apply to every account the same way.
5. The denied call is recorded in CloudTrail. Only calls that every layer allows actually run.

**Owner:** Automatic  
**Components:** [Management account](components/management-account.md), [AWS Organizations](components/aws-organizations.md), [OU structure](components/ou-structure.md), [SCPs, RCPs and declarative policies](components/service-control-policies.md), [CloudTrail organization trail](components/cloudtrail-org-trail.md), [Project account](components/project-account.md)  
**Play it:** [site/index.html#flow-12](../site/index.html#flow-12)

## A worked example

See "A day in the life" in the [overview](00-overview.md#5-a-day-in-the-life) for how these flows chain together for a new project, from request to first production deploy.
