---
title: "RUNTIME, SEEDING AND EDGE"
date: 2026-11-22
weight: 5
chapter: false
pre: "<b> 5.5 </b>"
---

Connect the deployed stacks into a running Tindy environment. Edge provides the HTTPS hostname. Runtime runs the API behind CloudFront VPC Origin and an internal ALB. Database tasks prepare DB roles, schema and demo data before enabling the API.

Keep Edge in maintenance and Runtime at DesiredCount 0 while preparing secrets, bootstrap, migrations and seed data. Open traffic after successful DB tasks and API health checks.

## 5.5.1 — Create Edge in maintenance mode

Create `edge.yaml` first to obtain `DistributionDomainName`, used as Runtime `CloudFrontPublicHost` and for OAuth callback updates during authentication verification.

| **Component**                | **Purpose**                                                                               |
| :--------------------------- | :---------------------------------------------------------------------------------------- |
| CloudFront distribution      | Provides the HTTPS entry point for frontend and API.                                      |
| Origin Access Control        | Enables CloudFront access to the frontend S3 origin through its bucket policy.            |
| Frontend bucket policy       | Restricts origin access to the configured distribution.                                   |
| Maintenance routing function | Returns HTTP 503 and maintenance JSON for API routes while the backend is being prepared. |

Initially use `EdgeMode = maintenance` and leave `AlbDnsName` and `VpcOriginId` empty. Switch to live with the new Runtime values after the API becomes healthy.



**Expected result** is CREATE_COMPLETE, three Outputs and HTTPS `/api/health/ready` returning HTTP 503 with maintenance JSON.

## 5.5.2 — Create Runtime with DesiredCount zero

`runtime.yaml` consumes inputs from Foundation, Network, Database, Integrations and Edge.

| **Resource group**                       | **Purpose**                                                                    |
| :--------------------------------------- | :----------------------------------------------------------------------------- |
| ECS cluster, task definition and service | Define the image, configuration, secret references, CPU/memory and task count. |
| Execution and task IAM roles             | Support container startup and application AWS access.                          |
| Internal ALB, listener and target group  | Receive VPC Origin traffic, route requests to the container and check health.  |
| CloudFront VPC Origin                    | Connects the distribution to the internal ALB.                                 |
| Security group ingress                   | Restricts ALB access according to CloudFront configuration.                    |
| CloudWatch log group                     | Records API and database task logs.                                            |
| Conditional one-off task definitions     | Run bootstrap, migrations and seeding once required parameters are supplied.   |

Create Runtime with a digest-pinned image, `DBProvisionMode = Fresh` and `DesiredCount = 0`. Retain the verified OAuth, media and Bedrock text settings. Keep AI PDF false during schema preparation. Once the zero-count service exists, prepare secrets and update parameters to enable DB tasks.

## Stages after Runtime creation

| **Order** | **Work**                                                                | **Verification**                                             |
| :-------- | :---------------------------------------------------------------------- | :----------------------------------------------------------- |
| 1         | Prepare application/migration/seed secrets and verify the new endpoint. | Secret references match the Fresh DB configuration.          |
| 2         | Add one-off task inputs through a change set.                           | Bootstrap, migration and seed definitions appear in Outputs. |
| 3         | Run bootstrap → migrations → seed sequentially.                         | Each task exits with code 0 and completion logs.             |
| 4         | Configure CloudFront service SG ingress and set DesiredCount to 1.      | Stable ECS service and Healthy target group.                 |
| 5         | Update alarm dimensions and switch Edge live.                           | API readiness returns 200 through the new hostname.          |
| 6         | Update OAuth, release frontend and verify demonstration flows.          | Frontend and API work through CloudFront.                    |

Each stage will receive detailed instructions and screenshots as it is performed. API and migration credentials serve separate DB roles, while the master secret supports bootstrap.

## Session progress

| **Component**                  | **Verified state**                                                                                                                                                                                                |
| :----------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Edge                           | UPDATE_COMPLETE, EdgeMode live, CloudFront readiness returned HTTP 200 Healthy.                                                                                                                                   |
| Runtime                        | UPDATE_COMPLETE with nine Outputs. Secrets and all three DB task definitions are ready.                                                                                                                           |
| Bootstrap, migrations and seed | All three tasks completed with exit code 0 and completion logs. API is running. Web verification remains pending.                                                                                                 |
| Live API and web verification  | Runtime Desired 1 / Running 1 / Pending 0, ALB 1 Healthy. Edge is live, with HTTP 200 Healthy readiness through CloudFront. Demo login and Discover Projects data rendering verified. Other flows remain pending. |

## Practical guides

| **Page** | **Procedure and evidence** |
| :--- | :--- |
| [5.5.1 Create Edge in maintenance mode](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.1-edge-maintenance/) | Create the distribution, record its hostname and verify HTTP 503. |
| [5.5.2 Create Runtime with DesiredCount zero](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.2-runtime/) | Explain 28 parameters, create ECS/ALB/VPC Origin and collect Outputs. |
| [5.5.3 Prepare Fresh database secrets](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.3-secrets/) | Verify zero ECS counts, run the script and collect DB task references. |
| [5.5.4 Run bootstrap, migrations and seed](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.4-database-tasks/) | Configure Fargate and verify exit codes and logs sequentially. |
| [5.5.5 Activate API and verify Runtime](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.5-activate-runtime/) | Identify the CloudFront service SG, update ingress and start the API. |
| [5.5.6 Switch Edge to live](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.6-edge-live/) | Collect Runtime Outputs and review the CloudFront API routing change set. |
| [5.5.7 Reconnect CI/CD permissions](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.7-cicd/) | Restore deployment roles and reconcile repository variables. |
