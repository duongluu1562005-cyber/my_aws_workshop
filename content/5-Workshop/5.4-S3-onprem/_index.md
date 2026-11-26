---
title: "DATABASE AND INTEGRATIONS"
date: 2026-11-19
weight: 4
chapter: false
pre: "<b> 5.4. </b>"
---

Prepare application storage and PDF scanning before running the backend. Database consumes the networking from section 5.3. Integrations uses the Foundation media bucket and delivers scan results to the API worker.

This session provisions a Fresh database. Bootstrap, migrations and demo seeding run during Runtime setup after RDS creation completes.

## 5.4.1 — Create a Fresh Database

`database.yaml` defines three main resources and database access settings.

| **Resource or setting**                 | **Purpose**                                                                        |
| :-------------------------------------- | :--------------------------------------------------------------------------------- |
| DB subnet group                         | Places RDS in private DB subnets from Network.                                     |
| PostgreSQL parameter group              | Enables `rds.force_ssl` to require TLS.                                            |
| RDS instance                            | Stores Tindy data using PostgreSQL 17, `db.t4g.micro` and Single-AZ configuration. |
| RDS-managed master secret               | Supplies the administrative credential to the bootstrap task.                      |
| Deletion protection and snapshot policy | Protect database deletion and request a snapshot under the template policy.        |

Use `DBProvisionMode = Fresh`, leave the snapshot identifier empty and enable deletion protection. RDS creates database `tindy` and master user `tindy_admin`. The API uses a separate DB credential prepared later.



**Expected result** is `CREATE_COMPLETE` and five Outputs covering endpoint, port, identifier, master secret ARN and provision mode. Schema preparation and demo data follow in section 5.5.

## 5.4.2 — Deploy Integrations

`integrations.yaml` configures PDF scan result delivery and operational notifications.

| **Component**                     | **Purpose**                                                                          |
| :-------------------------------- | :----------------------------------------------------------------------------------- |
| GuardDuty Malware Protection plan | Scans new objects under `certificates/pdf/` and `ai-input/pdf/` in the media bucket. |
| Tag scanned objects               | Records scan results on objects to support access controls.                          |
| EventBridge rule                  | Delivers scan result events to the queue.                                            |
| SQS queue and policy              | Receive messages for the API worker.                                                 |
| Dead-letter queue and alarm       | Retain failed messages under the redrive policy and detect backlog.                  |
| SNS topic and email subscription  | Send alerts to the confirmed recipient.                                              |
| Conditional ALB/API alarms        | Use new ALB/target group dimensions after Runtime deployment.                        |

Leave both ALB metric dimensions empty initially. After creation, confirm the SNS email subscription and verify the Active protection plan, tagging and both PDF prefixes.

## Inputs and outputs

| **Dependency**                 | **References**                                               |
| :----------------------------- | :----------------------------------------------------------- |
| Network → Database             | `PrivateDbSubnetIds`, `DatabaseSecurityGroupId`.             |
| Foundation → Integrations      | `MediaBucketName`.                                           |
| Database → Runtime preparation | `DBEndpointAddress`, `DBMasterSecretArn`, `DBProvisionMode`. |
| Integrations → Runtime         | `GuardDutyProtectionPlanArn`, `GuardDutyQueueUrl`.           |
| Runtime → Integrations update  | New ALB/target group metric dimensions for alarms.           |

## Verified results

| **Component** | **State**                                                |
| :------------ | :------------------------------------------------------- |
| Database      | `CREATE_COMPLETE`, Fresh mode and five Outputs.          |
| Integrations  | `CREATE_COMPLETE`, seven Outputs.                        |
| SNS           | Subscription confirmed.                                  |
| GuardDuty     | Active plan, tagging enabled and both prefixes verified. |

Application PDF upload testing follows API and schema readiness.

## Practical guides

| **Page** | **Procedure and evidence** |
| :--- | :--- |
| [5.4.1 Create a Fresh Database](/my_aws_workshop/5-workshop/5.4-s3-onprem/5.4.1-prepare/) | Fresh parameters, networking, Options and RDS Outputs. |
| [5.4.2 Deploy Integrations](/my_aws_workshop/5-workshop/5.4-s3-onprem/5.4.2-create-interface-enpoint/) | PDF scan stack, SNS confirmation and protection plan verification. |
