# Three-Tier SaaS Architecture on AWS

A production-style, three-tier web application architecture on AWS, provisioned entirely with Terraform. Built as a portfolio project to demonstrate cloud architecture, infrastructure-as-code, and AWS networking/security fundamentals.

## Architecture

![Architecture diagram](docs/architecture-diagram.svg)

<details>
<summary>Text version</summary>

```
Users
  |
  v
Route 53 --> CloudFront (CDN) + WAF --> S3 (static assets)
  |
  v
+----------------------------- VPC (10.0.0.0/16) -----------------------------+
|                                                                              |
|  Public subnet (2 AZs)                                                      |
|    Application Load Balancer                                                |
|              |                                                              |
|              v                                                              |
|  Private subnet - app tier (2 AZs)                                          |
|    ECS Fargate service (auto-scaling, CPU target tracking)                  |
|              |                                                              |
|      +-------+-------+                                                     |
|      v               v                                                     |
|  Private subnet - data tier (2 AZs)                                        |
|    RDS PostgreSQL (Multi-AZ)    ElastiCache Redis (session/query cache)    |
|                                                                              |
+------------------------------------------------------------------------------+
```
</details>

**Edge layer** — Route 53 resolves DNS, CloudFront caches and serves content globally, WAF filters malicious traffic (AWS managed rule set + rate limiting) before it reaches the VPC. Static assets are served from S3 through CloudFront using Origin Access Control (no public bucket access).

**Public subnet** — Only the ALB is internet-facing. It terminates HTTP(S) and forwards to the app tier.

**Private app subnet** — ECS Fargate runs the containerized application across two AZs, with target-tracking autoscaling on CPU utilization. No public IPs; outbound internet access (for pulling images, calling APIs) goes through NAT gateways.

**Private data subnet** — RDS PostgreSQL (Multi-AZ, encrypted at rest) and ElastiCache Redis, reachable only from the app tier's security group. Database credentials are generated with `random_password` and stored in Secrets Manager — never hardcoded, never in state as plaintext output.

## Tech stack

| Layer | Service |
|---|---|
| DNS / CDN | Route 53, CloudFront |
| Security | AWS WAF, Security Groups, Secrets Manager |
| Compute | ECS Fargate |
| Load balancing | Application Load Balancer |
| Database | RDS PostgreSQL (Multi-AZ) |
| Cache | ElastiCache Redis |
| Storage | S3 |
| IaC | Terraform |

## Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/downloads) >= 1.5
- An AWS account and credentials configured (`aws configure` or environment variables)
- A container image pushed somewhere ECS can pull from (ECR, Docker Hub, etc.)

## Setup

```bash
git clone <your-repo-url>
cd aws-three-tier-saas

cp terraform.tfvars.example terraform.tfvars
# edit terraform.tfvars: set container_image to your app's image, adjust sizing

terraform init
terraform plan
terraform apply
```

On success, Terraform prints the ALB DNS name and CloudFront domain. Point Route 53 (or your DNS provider) at the CloudFront domain for production traffic.

To tear everything down:

```bash
terraform destroy
```

## Notes and next steps

- **HTTPS**: the ALB and CloudFront default to HTTP/the default CloudFront cert. For a real domain, request an ACM certificate (in `us-east-1` for CloudFront) and wire it into `alb.tf` / `s3_cloudfront.tf`.
- **WAF region**: `aws_wafv2_web_acl` with `scope = "CLOUDFRONT"` must be created in `us-east-1`. If your primary region is different, add a provider alias for this resource.
- **CI/CD**: not included here by design — see the "CI/CD" section of the project writeup / issues for a suggested GitHub Actions pipeline (build → push to ECR → `ecs update-service`).
- **Cost**: with `db.t4g.micro`, `cache.t4g.micro`, 2 NAT gateways, and 2 Fargate tasks, expect roughly $120–180/month depending on region and traffic — the two NAT gateways are the biggest line item. For a pure demo, you can drop to a single NAT gateway or a NAT instance.
- **State**: this repo uses local state by default. Uncomment the `backend "s3"` block in `providers.tf` for remote state if collaborating or running from CI.

## Proof of deployment

This stack creates real, billable AWS resources, so it isn't left running permanently. Evidence from the most recent deploy:

- [ ] Screenshot: ECS service running with healthy tasks
- [ ] Screenshot: RDS instance status (Multi-AZ, `available`)
- [ ] Screenshot: CloudFront distribution deployed
- [ ] Screen recording: `terraform apply` → hitting the ALB/CloudFront endpoint in a browser

<!--
Once you've deployed and captured evidence, replace the checklist above with:

![ECS service running](docs/screenshots/ecs-service.png)
![RDS Multi-AZ](docs/screenshots/rds-status.png)
![CloudFront distribution](docs/screenshots/cloudfront.png)

Or link a short demo video/GIF here.
-->

## Architecture decisions

See [`docs/DECISIONS.md`](docs/DECISIONS.md) for the reasoning behind the key choices — ECS Fargate vs. EC2/Lambda, Multi-AZ RDS, three-tier subnet separation, Secrets Manager, and what was deliberately left out of scope.

## License

MIT — use this as a starting point for your own projects.
