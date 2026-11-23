# Service Catalog best-practice templates

**Layer:** Provisioning
**Lives in:** tcsp-aft-management · AFT OU (portfolio shared to the Project OU)
**Owned by:** Platform Engineering team; project teams launch products

## What it is
A portfolio of approved infrastructure templates that project teams can launch themselves, such as the Development space, a Path A starter and a Path B starter.

## Why it exists
Teams should not each design a VPC, pipeline and logging setup from scratch. Approved templates give a fast, safe start and keep common patterns consistent.

## What it is built to accommodate
Self-service launches by any project account in the Project OU, versioned products with upgrade paths, and launch constraints so a team can deploy a product without holding the permissions it uses. Use it for shared starting points. One-off application stacks belong in the team's own IaC.

## How it works
Inputs: product templates from the tcsp-templates repo. What it does: publishes products to a portfolio, shares it with the Project OU, and launches products with a constrained launch role. Outputs: resources in the project account, tracked as provisioned products.

## Connected flows
- [Flow 4: Template deploy](../02-service-flows.md#4-template-deploy): Service Catalog deploys a best-practice template as the project's Development space.

## Guardrails that apply
Templates pass the same policy-as-code checks as platform Terraform. Launch roles are limited to the resources each product needs.

## TCSP defaults and options
Default products: Development space, Path A starter (EKS with Argo CD), Path B starter (CodePipeline to ECS). Option: publish templates through an internal developer portal that calls Service Catalog.

## Watch out for
Old product versions linger in accounts, so publish deprecation dates. Products launched in an account stay tied to this portfolio; plan for that during migrations.

---
[All components](README.md) · [Interactive architecture](../../site/index.html#c-svccat) · [Overview](../00-overview.md)
