# Tindy Architecture Overview

Tindy is a web application that allows users to discover projects, find teammates, manage profiles, submit applications, and communicate with other members. In this workshop, the AWS infrastructure for the frontend, backend, database, and PDF scanning services is deployed using six CloudFormation templates.

## Objectives

The workshop focuses on deploying the CloudFormation stacks in the correct dependency order and using stack Outputs to configure subsequent stacks. It also covers creating a fresh database, running bootstrap, migration, and seed tasks, and verifying the application through CloudFront.

The seeded demo data is used to demonstrate login, project discovery, applications, and reviews.

## System Architecture

The architecture diagram illustrates how the AWS services and application components are organized and connected.

[Additional Tindy architecture diagram](/my_aws_workshop/static/images/5-Workshop/5.1-Workshop-overview/tindy-architecture-supplement.png)

| **Component** | **Role** |
| :--- | :--- |
| CloudFront and frontend S3 | Serve the frontend over HTTPS, with Origin Access Control managing access to the S3 bucket. |
| CloudFront VPC Origin and internal ALB | Forward backend requests from CloudFront to the ECS API. |
| ECS Fargate | Host the API and execute one-off database preparation tasks. |
| RDS PostgreSQL | Store application data in private database subnets using TLS connections. |
| Media S3 | Store uploaded files under application-specific prefixes. |
| GuardDuty, EventBridge and SQS | Scan PDF files and send scanning results to the API worker, using a DLQ for failed processing. |
| Secrets Manager and SSM | Manage application secrets and Data Protection keys. |
| ECR and GitHub OIDC | Store backend container images and enable GitHub Actions to authenticate with AWS. |
| CloudWatch and SNS | Collect logs, monitor alarms, and send email notifications. |

## CloudFormation Templates

The deployment uses six CloudFormation templates, each responsible for a specific part of the infrastructure.

| **Template** | **Scope** |
| :--- | :--- |
| `foundation.yaml` | Creates S3, ECR, secrets, OIDC, and conditional deployment/operations roles. |
| `network.yaml` | Configures the VPC, subnets, routes, and security groups. |
| `database.yaml` | Provisions the RDS instance, subnet group, and parameter group. |
| `integrations.yaml` | Sets up the GuardDuty plan, EventBridge rule, SQS/DLQ, SNS, and alarms. |
| `edge.yaml` | Configures CloudFront, Origin Access Control, the frontend bucket policy, and maintenance routing. |
| `runtime.yaml` | Deploys ECS, the internal ALB, VPC Origin, IAM roles, and task definitions. |

## Practical Deployment Sequence

The deployment follows five main steps:

1. **Environment cleanup (Section 5.7):** Remove the existing infrastructure before starting the new deployment.
2. **Foundation and Network (Section 5.3):** Import retained Foundation resources, restore the main template, and create the Network stack.
3. **Database and Integrations (Section 5.4):** Provision a fresh database and deploy the Integrations stack.
4. **Edge and Runtime (Section 5.5):** Deploy Edge in maintenance mode to obtain the CloudFront hostname. Create Runtime with `DesiredCount` set to `0`, prepare and seed the database, enable the API, and switch Edge to live mode.
5. **Application verification (Section 5.6):** Test the deployed application and demonstrate its main features.

The workshop uses the existing AWS account in the `us-east-1` region, with a new CloudFront hostname and freshly seeded demo data. Each deployment section includes the verified results and screenshots collected during the process.
