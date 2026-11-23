# Guardrails

TCSP gives project teams admin rights inside their own accounts. That is safe because five guardrail layers, each acting at a different moment, catch different kinds of failure. A gap in one layer is covered by the next.

| Layer | When it acts | TCSP mechanism | Example it catches | Owner |
| --- | --- | --- | --- | --- |
| 1. Preventive | Before an API call succeeds | SCPs, RCPs and declarative policies on the OU tree; Control Tower preventive controls | Launching in an unapproved Region; disabling CloudTrail | Platform Engineering team with Security |
| 2. Provision-time baseline | When an account is created or enrolled | [CT Baseline](components/ct-baseline.md) + [TF Baseline](components/tf-baseline.md) applied by AFT | An account born with public S3 allowed or no default encryption | Platform Engineering team |
| 3. Code-time policy | Before Terraform applies | Sentinel or OPA policies in [HCP Terraform](components/hcp-terraform.md) on every plan | A security group opening 0.0.0.0/0 on port 22 or 3389 | Platform Engineering team with Security |
| 4. Delivery-time | Before a container ships | Scanners and SBOM in [shared CI](components/shared-ci-eks.md); [ECR](components/ecr.md) image scanning; admission policies in project clusters | A vulnerable base image or a hard-coded secret | Platform Engineering team; project teams fix findings |
| 5. Detective and runtime | After something is live | [GuardDuty](components/guardduty.md), [Detective](components/detective.md), [Config](components/aws-config.md), [IAM Access Analyzer](components/iam-access-analyzer.md), [Security Hub](components/security-hub.md) in the Audit account; logs in [Log Archive](components/log-archive-account.md) | Crypto-mining, a bucket shared outside the organization, configuration drift | Security; project teams fix application issues |

## Layer 1: preventive policies

**How it works.** IAM grants permissions; organization policies cap them. On every API call in a member account, AWS evaluates IAM and then every SCP and RCP attached on the path from the root to the account's OU. Any explicit deny wins, so a project admin with AdministratorAccess is still blocked. See [flow 12](02-service-flows.md#12-guardrail-check).

SCPs never apply to the management account, so that account is protected by access instead: only the PlatformAdmin and BreakGlass permission sets reach it, and every sign-in raises an alert.

### SCP starter set

| Attached to | Policy | Effect |
| --- | --- | --- |
| Root (all OUs) | Region allow-list | Deny actions outside the approved Regions (home Region plus one DR Region), with global services exempted |
| Root | Protect the baseline | Deny changes to CloudTrail, Config, GuardDuty, Control Tower roles, AFT roles and the observability integration roles |
| Root | Root-user deny | Deny all actions by the member-account root user |
| Root | Leave-org deny | Deny `organizations:LeaveOrganization` |
| Security OU | Lock down log and audit | Deny deleting or changing log buckets, Object Lock and detector settings, except from a named break-glass role |
| Infra OU | Network owner only | Deny Transit Gateway, Direct Connect, Client VPN and route-table changes except from the network pipeline role |
| Project OU | No network bypass | Deny creating Internet Gateways, VPC peering and Transit Gateway attachments outside the platform role |
| Project OU | No public data | Deny turning off S3 Block Public Access; deny public EBS and RDS snapshots |
| Project OU / Prod | Stricter production | Deny IAM user and access-key creation; require IMDSv2; deny unencrypted volumes |
| AFT OU | Factory only | Deny everything except the AFT service roles and break-glass |

### Beyond SCPs

- **Resource control policies (RCPs)** cap what can be done *to* resources, whoever the caller is. TCSP starts with RCPs that limit S3, KMS, Secrets Manager and SQS access to principals inside the organization, which closes the gap SCPs leave for callers from other organizations.
- **Declarative policies** fix service settings across the organization and keep them fixed, for example blocking public access for EBS snapshots and AMIs, and enforcing IMDSv2 defaults.

### Rules for writing policies

- Keep policies in the `tcsp-org` repository with unit tests that assert which actions are allowed and denied.
- Attach a new policy to a test OU with canary accounts first; widen it after a week without unexpected denies.
- Prefer a few broad, simple rules. Fine-grained permissions belong in IAM inside the account.
- Every exception is time-limited, recorded in Git and reviewed when it expires.

## Layer 2: provision-time baseline

Every account carries two baselines from the moment it exists:

- **CT Baseline**, installed by Control Tower: organization trail, Config recorder, execution roles and the controls enabled on the OU.
- **TF Baseline**, applied by AFT from `aft-global-customizations`: S3 Block Public Access, EBS default encryption, IAM password policy, default-VPC removal, IMDSv2 defaults, security alarms, observability integration roles, metric streams and a budget.

Baseline changes roll out in stages: plan-only on canary accounts, then by OU, with pinned module versions.

## Layer 3: code-time policy

Every Terraform plan for account customizations is checked before apply. Starter policies:

- No security group rule open to `0.0.0.0/0` or `::/0` on administrative ports.
- No S3 bucket without encryption and Block Public Access.
- No VPC created outside an IPAM pool.
- Required tags present: owner, cost centre, environment, data classification.
- No IAM policy with `"Action": "*"` and `"Resource": "*"` outside the platform roles.

Hard-mandatory policies stop the run. Soft-mandatory overrides are logged with a reason. Results feed security monitoring ([flow 9](02-service-flows.md#9-security-findings)).

## Layer 4: delivery-time

The shared CI runs the same checks for every project:

- source and dependency scanning, secret scanning, IaC scanning;
- container image scanning, with builds failing on critical findings;
- an SBOM for every image, stored with the image;
- image signing, verified by admission policies in project EKS clusters.

## Layer 5: detective and runtime

GuardDuty, Detective, IAM Access Analyzer, Security Hub and the Config aggregator are enabled organization-wide with `tcsp-audit` as delegated administrator. New accounts are enrolled automatically. Findings are routed to the owning team by account tag; Security tracks them to closure. Logs from every account land in `tcsp-log-archive` with S3 Object Lock, so even an admin cannot erase evidence.

## Who owns what

The Platform Engineering team owns layers 1 and 2 and the shared parts of layers 3 to 5. Security owns the content of the detective layer and the policy rules. Each project team owns its application security: authorization, data classification, secrets, and responding to its own alerts. This split is part of the onboarding terms every project accepts.
