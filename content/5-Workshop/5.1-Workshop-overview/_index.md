---
title: "TINDY ARCHITECTURE OVERVIEW"
date: 2026-11-19
weight: 1
chapter: false
pre: " <b> 5.1 </b> "
---
Tindy helps users find projects and teammates, manage profiles, apply to projects and communicate with other members. This workshop deploys its AWS frontend, backend, database and PDF scanning infrastructure through six CloudFormation templates.

## Objectives

Deploy stacks in dependency order, use stack Outputs as subsequent inputs, provision a Fresh database, run bootstrap/migration/seed tasks and verify the application through CloudFront. Seeded data supports the login, project discovery, application and review demonstration.

## System architecture

The diagram shows the arrangement of service groups and application components.



[Additional Tindy architecture diagram](/aws_my_workshop/images/5-Workshop/5.1-Workshop-overview/tindy-architecture-supplement.png)

| **Component**                          | **Role**                                                                           |
| :------------------------------------- | :--------------------------------------------------------------------------------- |
| CloudFront and frontend S3             | Deliver the frontend over HTTPS using Origin Access Control for bucket access.     |
| CloudFront VPC Origin and internal ALB | Route backend requests from CloudFront to the ECS API.                             |
| ECS Fargate                            | Run the API and one-off database preparation tasks.                                |
| RDS PostgreSQL                         | Store application data in private DB subnets with TLS connections.                 |
| Media S3                               | Store uploads under application prefixes.                                          |
| GuardDuty, EventBridge and SQS         | Scan PDFs and deliver results to the API worker, with a DLQ for failed processing. |
| Secrets Manager and SSM                | Store application secrets and Data Protection keys.                                |
| ECR and GitHub OIDC                    | Store backend images and support GitHub Actions authentication to AWS.             |
| CloudWatch and SNS                     | Provide logs, alarms and email notifications.                                      |

## CloudFormation templates

| **Template**        | **Scope**                                                                          |
| :------------------ | :--------------------------------------------------------------------------------- |
| `foundation.yaml`   | S3, ECR, secrets, OIDC and conditional deployment/operations roles.                |
| `network.yaml`      | VPC, subnets, routes and security groups.                                          |
| `database.yaml`     | RDS instance, subnet group and parameter group.                                    |
| `integrations.yaml` | GuardDuty plan, EventBridge rule, SQS/DLQ, SNS and alarms.                         |
| `edge.yaml`         | CloudFront, Origin Access Control, frontend bucket policy and maintenance routing. |
| `runtime.yaml`      | ECS, internal ALB, VPC Origin, IAM roles and task definitions.                     |

## Practical deployment sequence

1. Clean up the existing environment, documented in section 5.7.
2. Import retained Foundation resources, restore the main template and create Network in section 5.3.
3. Create the Fresh Database and Integrations in section 5.4.
4. Create Edge maintenance to obtain its hostname, provision Runtime with DesiredCount 0, prepare and seed the database, enable the API and switch Edge live in section 5.5.
5. Verify and demonstrate the application in section 5.6.

This session uses the existing account in `us-east-1`, a new CloudFront hostname and newly seeded demo data. Deployment pages record verified results and screenshots as execution proceeds.
