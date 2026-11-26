---
title: "FOUNDATION AND NETWORK"
date: 2026-11-19
weight: 3
chapter: false
pre: "<b> 5.3. </b> "
---

Build Tindy storage, application configuration and networking. Foundation supplies buckets, the image registry and secret references. Network provides the VPC, subnets and security groups used by Database and Runtime.

This session follows cleanup. Nine Foundation resources were retained, so import them into the new stack before restoring the main template.

## 5.3.1 — Deploy Foundation

`foundation.yaml` manages persistent resources and conditional GitHub Actions roles.

| **Resource group**              | **Purpose in Tindy**                                                            |
| :------------------------------ | :------------------------------------------------------------------------------ |
| Frontend bucket                 | Stores frontend build assets, served by CloudFront using Origin Access Control. |
| Media bucket and policy         | Store uploads and enforce access conditions used by PDF scanning.               |
| ECR repository                  | Stores API images, pinned by digest for Runtime.                                |
| Application secrets             | Provide DB, JWT and OAuth configuration references to ECS tasks.                |
| GitHub OIDC provider            | Supports GitHub Actions authentication through deployment roles.                |
| Deployment/operations IAM roles | Grant permissions to workflows when their parameters enable them.               |

First, import nine retained resources with `foundation-import.yaml`. After `IMPORT_COMPLETE`, update with `foundation.yaml` to restore Outputs and recreate `InfraPlannerRole`. Configure roles that depend on new CloudFront and Runtime inputs when those inputs are available.



**Expected result** is stack `UPDATE_COMPLETE`, with Outputs for bucket names, ECR URI, secret ARNs, the OIDC provider and enabled role.

## 5.3.2 — Deploy Network

`network.yaml` creates VPC `10.20.0.0/16`. Each subnet group spans two Availability Zones and serves a specific component.

| **Component**                     | **Use**                                                                    |
| :-------------------------------- | :------------------------------------------------------------------------- |
| Public task subnets               | Fargate API and one-off tasks with public IP assignment configured in ECS. |
| Private app subnets               | Internal ALB, connected to CloudFront through a VPC Origin.                |
| Private DB subnets                | RDS PostgreSQL subnet group.                                               |
| Internet Gateway and route tables | Routing for the subnet groups.                                             |
| Security groups and ingress rules | Control traffic between ALB, API, migration tasks and the database.        |

The template defines 26 resources and nine Outputs. Use the new networking IDs in sections 5.4 and 5.5. Subnets span two Availability Zones, while API task count and database Multi-AZ behavior follow Runtime and Database settings.

## Dependencies for subsequent stacks

| **Output from this section**                    | **Consumer**                                    |
| :---------------------------------------------- | :---------------------------------------------- |
| `MediaBucketName`                               | Integrations protection plan and Runtime media. |
| `FrontendBucketName`                            | Edge S3 origin and bucket policy.               |
| `ApiRepositoryUri`, secret ARNs                 | Runtime image and application configuration.    |
| `PrivateDbSubnetIds`, `DatabaseSecurityGroupId` | RDS in section 5.4.1.                           |
| VPC, API/ALB subnets and security groups        | Runtime in section 5.5.2.                       |

## Verified results

| **Component** | **State**                                                                                           |
| :------------ | :-------------------------------------------------------------------------------------------------- |
| Foundation    | Import completed and main template update reached `UPDATE_COMPLETE`, with 14 Outputs at this stage. |
| Network       | `CREATE_COMPLETE`, with nine Outputs.                                                               |

## Practical guides

| **Page** | **Procedure and evidence** |
| :--- | :--- |
| [5.3.1 Deploy Foundation](/my_aws_workshop/5-workshop/5.3-s3-vpc/5.3.1-create-gwe/) | Import retained resources, restore the main template, review the role and Outputs. |
| [5.3.2 Deploy Network](/my_aws_workshop/5-workshop/5.3-s3-vpc/5.3.2-test-gwe/) | Create networking, add tags and verify new Outputs. |

