# Components

Every part of TCSP, grouped by layer. Each page explains what the component is for, what it is built to accommodate and where it is used. The same text appears in the detail panel of the [interactive architecture](../../site/index.html).

## Governance

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [Management account](management-account.md) | tcsp-management · Organization root | Platform Engineering team | 2, 10, 11, 12 |
| [AWS Organizations](aws-organizations.md) | tcsp-management · Organization root | Platform Engineering team | 2, 11, 12 |
| [OU structure](ou-structure.md) | tcsp-management · Root, Security, Infra, Project (Prod, Non-Prod, Migration) and AFT OUs | Platform Engineering team | 2, 12 |
| [SCPs, RCPs and declarative policies](service-control-policies.md) | tcsp-management · attached to Root and to each OU | Platform Engineering team, with Security | 2, 12 |
| [AWS Control Tower](control-tower.md) | tcsp-management · Organization root | Platform Engineering team | 2, 8 |
| [IAM Identity Center and permission sets](iam-identity-center.md) | tcsp-management · Organization root (administration can be delegated) | Platform Engineering team | 5, 11 |
| [CT Baseline](ct-baseline.md) | every member account · all OUs | Platform Engineering team | 2, 8 |
| [TF Baseline](tf-baseline.md) | every member account · all OUs (applied from aft-global-customizations) | Platform Engineering team | 2, 10 |

## Provisioning

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [AFT management account](aft-management-account.md) | tcsp-aft-management · AFT OU | Platform Engineering team | 1, 2, 3, 4 |
| [AFT pipeline](aft-pipeline.md) | tcsp-aft-management · AFT OU | Platform Engineering team | 1, 2, 3 |
| [Platform VCS and the four AFT repositories](aft-repositories.md) | Platform VCS (outside AWS, or in tcsp-shared-services) | Platform Engineering team | 1, 2, 3 |
| [HCP Terraform and policy as code](hcp-terraform.md) | SaaS, outside AWS (one workspace per account customization) | Platform Engineering team, with Security for policies | 3, 9 |
| [Service Catalog best-practice templates](service-catalog.md) | tcsp-aft-management · AFT OU (portfolio shared to the Project OU) | Platform Engineering team; project teams launch products | 4 |
| [Development space](development-space.md) | each project account · Project OU | Project team | 4 |

## Security

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [Log Archive account](log-archive-account.md) | tcsp-log-archive · Security OU | Security | 8 |
| [Audit account](audit-account.md) | tcsp-audit · Security OU | Security | 9 |
| [Amazon GuardDuty](guardduty.md) | detectors in every account · delegated admin in tcsp-audit | Security | 9 |
| [Amazon Detective](detective.md) | tcsp-audit · Security OU (delegated admin) | Security | 9 |
| [AWS Config and Config Aggregator](aws-config.md) | recorders in every account · aggregator in tcsp-audit | Security | 8, 9 |
| [IAM Access Analyzer](iam-access-analyzer.md) | tcsp-audit · Security OU (delegated admin) | Security | 9 |
| [Security Hub CSPM](security-hub.md) | tcsp-audit · Security OU (delegated admin) | Security | 9 |
| [CloudTrail organization trail](cloudtrail-org-trail.md) | trail in tcsp-management · bucket in tcsp-log-archive | Security (operated by the Platform Engineering team) | 8, 12 |

## Network

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [Network account](network-account.md) | tcsp-network · Infra OU | Platform Engineering team | 3, 5 |
| [Transit Gateway and route tables](transit-gateway.md) | tcsp-network · Infra OU (shared to the organization with AWS RAM) | Platform Engineering team | 3, 5, 6 |
| [AWS Client VPN](client-vpn.md) | tcsp-network · Infra OU | Platform Engineering team | 5 |
| [Direct Connect and Site-to-Site VPN](direct-connect.md) | tcsp-network · Infra OU | Platform Engineering team | 5 |
| [Egress VPC (NAT + Network Firewall)](egress-vpc.md) | tcsp-network · Infra OU | Platform Engineering team | 6 |
| [VPC IPAM](vpc-ipam.md) | tcsp-network · Infra OU (pools shared with AWS RAM) | Platform Engineering team | 3 |
| [Route 53 Resolver](route53-resolver.md) | tcsp-network · Infra OU (rules shared with AWS RAM) | Platform Engineering team | 5 |

## Delivery

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [Shared account](shared-account.md) | tcsp-shared-services · Infra OU | Platform Engineering team | 5, 6 |
| [Shared VCS](shared-vcs.md) | tcsp-shared-services · Infra OU | Platform Engineering team operates it; project teams own their repositories | 5, 6 |
| [Shared CI on EKS](shared-ci-eks.md) | tcsp-shared-services · Infra OU | Platform Engineering team | 6 |
| [Amazon ECR (shared registry)](ecr.md) | tcsp-shared-services · Infra OU | Platform Engineering team | 6 |
| [Project account](project-account.md) | tcsp-prj-<name>-<env> · Project OU / Prod or Non-Prod | Project team, within the guardrails | 2, 3, 4, 6, 7, 11, 12 |
| [CD on project EKS (Path A)](cd-project-eks.md) | each project account · Project OU | Project team (the Platform Engineering team provides the template) | 6 |
| [CodePipeline to ECS (Path B)](codepipeline-ecs.md) | each project account · Project OU | Project team | 7 |

## Observability

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [Observability platform](observability-platform.md) | SaaS (for example New Relic, Datadog or Grafana Cloud) · integration roles in every account | Platform Engineering team for the platform; project teams for their apps | 10 |
| [Security monitoring](security-monitoring.md) | tcsp-audit (Security Hub) and an optional external CSPM or SIEM | Security | 9 |
| [Cost management](cost-management.md) | tcsp-management (CUR 2.0, Cost Explorer) · budgets in every account | Platform Engineering team, with Finance | 10 |

## People

| Component | Lives in | Owned by | Flows |
| --- | --- | --- | --- |
| [Platform Engineering team](platform-engineering-team.md) | outside AWS · works through the platform VCS | Platform Engineering team | 1, 2, 3 |

