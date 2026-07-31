# AWS Three-Tier SaaS Architecture

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws)
![Terraform](https://img.shields.io/badge/Terraform-IaC-623CE4?logo=terraform)
![ECS](https://img.shields.io/badge/Amazon-ECS-FF9900)
![RDS](https://img.shields.io/badge/Amazon-RDS-527FFF)
![CloudFront](https://img.shields.io/badge/Amazon-CloudFront-8C4FFF)
![License](https://img.shields.io/badge/License-MIT-blue)

A production-style **Three-Tier SaaS Architecture on AWS** designed following AWS Well-Architected Framework principles. This project demonstrates secure networking, containerized applications, high availability, scalability, caching, CDN integration, and cloud security best practices.

This repository is intended for **learning, portfolio demonstration, and cloud architecture showcase purposes**.

---

# Architecture Diagram

![Architecture Diagram](docs/architecture-diagram.svg)

---

# Architecture Overview

The architecture consists of three logical layers:

### Presentation Layer
Handles user requests, DNS resolution, content delivery, and edge security.

**Services Used:**
- Amazon Route 53
- Amazon CloudFront
- AWS WAF
- Amazon S3

---

### Application Layer
Processes application requests using containerized workloads.

**Services Used:**
- Application Load Balancer
- Amazon ECS Fargate
- Auto Scaling

---

### Data Layer
Stores application data and improves performance through caching.

**Services Used:**
- Amazon RDS PostgreSQL (Multi-AZ)
- Amazon ElastiCache Redis

---

# Architecture (Text Version)

```text
Users
   │
   ▼
Route 53
   │
   ▼
CloudFront + AWS WAF
   │
   ▼
S3 (Static Assets)
   │
   ▼
Application Load Balancer
   │
   ▼
ECS Fargate
   │
   ▼
+-----------------------------------+
|                                   |
| PostgreSQL (RDS Multi-AZ)         |
| Redis (ElastiCache)               |
|                                   |
+-----------------------------------+
```

---

# Key Features

- Three-Tier AWS Architecture
- Multi-AZ High Availability
- Secure Public and Private Subnets
- Containerized Applications using ECS Fargate
- Auto Scaling
- Load Balancing
- Database Layer with PostgreSQL
- Redis Caching Layer
- CloudFront CDN
- Route 53 DNS
- AWS WAF Protection
- Secrets Management
- Infrastructure as Code Design using Terraform

---

# AWS Services Used

| Category | Service |
|-----------|----------|
| Networking | VPC, Subnets, Route Tables |
| Compute | ECS Fargate |
| Load Balancing | Application Load Balancer |
| Database | Amazon RDS PostgreSQL |
| Cache | ElastiCache Redis |
| Storage | Amazon S3 |
| CDN | CloudFront |
| DNS | Route 53 |
| Security | WAF, IAM, Security Groups |
| Secrets | AWS Secrets Manager |
| Monitoring | CloudWatch |
| Infrastructure | Terraform |

---

# Architecture Highlights

- High Availability across multiple Availability Zones
- Stateless Application Design
- Private Database Tier
- Containerized Application Deployment
- Secure Networking
- Auto Scaling
- CDN Integration
- Edge Security with AWS WAF
- Secrets Management
- Cloud-Native Design

---

# Security Best Practices

The architecture follows AWS security best practices:

- Public access only through the Application Load Balancer
- Application servers in private subnets
- Database in private subnets
- Security Groups between each tier
- Least Privilege IAM Roles
- Database encryption enabled
- Secrets stored in AWS Secrets Manager
- Private S3 bucket
- AWS WAF managed rules
- Rate limiting for malicious traffic

---

# High Availability

The architecture is designed for fault tolerance:

- Multi-AZ Deployment
- ECS Service Auto Scaling
- Load Balancer Health Checks
- RDS Multi-AZ Database
- CloudFront Global Edge Locations

---

# Scalability

The infrastructure supports horizontal scaling:

- ECS Auto Scaling
- CloudFront Edge Caching
- Redis Caching
- Stateless Containers
- Load Balanced Application Tier

---

# Repository Structure

```text
.
├── docs/
│   └── architecture-diagram.svg
├── README.md
└── diagrams/
```

---

# Skills Demonstrated

- AWS Cloud Architecture
- Terraform Concepts
- Infrastructure as Code
- Amazon ECS
- Amazon RDS
- ElastiCache
- CloudFront
- Route 53
- AWS WAF
- IAM
- Secrets Manager
- Auto Scaling
- Load Balancing
- Networking
- High Availability
- Cloud Security

---

# Future Improvements

- CI/CD using GitHub Actions
- HTTPS using ACM Certificates
- Blue-Green Deployments
- CloudWatch Alarms
- SNS Notifications
- Monitoring Dashboard
- Remote Terraform State
- Automated Backup Strategy

---

# Project Status

**Status:** Completed

This project demonstrates a production-style AWS architecture intended for learning, portfolio showcase, and cloud design best practices.

---

# Author

**Gorakh Dorle**

Cloud Engineer | AWS | Terraform | DevOps

GitHub: https://github.com/gorakhd2609

LinkedIn: (https://www.linkedin.com/in/gorakh-dorle-2609v?)

---

# License

This project is licensed under the MIT License.
