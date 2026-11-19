
# Database and Integrations

## Overview

After deploying Foundation and Network, the next step is to set up the database and supporting services for Tindy.

This section covers two main components:

- **Database:** Deploys Amazon RDS for PostgreSQL to store application data, using the network resources created in Section 5.3.
- **Integrations:** Configures PDF malware scanning, delivers scan results to the API worker, and provides operational notifications.

This deployment uses a **fresh database**. Database schema initialization, migrations, and demo data seeding will be completed during Runtime setup in Section 5.5, after RDS is ready.

## 5.4.1 — Create a Fresh Database

The `database.yaml` template defines the AWS resources and settings required to run PostgreSQL on Amazon RDS.

### Main Resources and Configuration

| Resource or Setting | Purpose |
|---|---|
| DB Subnet Group | Places RDS in private database subnets from the Network stack. |
| PostgreSQL Parameter Group | Enables `rds.force_ssl` to require TLS connections. |
| RDS Instance | Stores Tindy application data using PostgreSQL 17, `db.t4g.micro`, and Single-AZ configuration. |
| RDS-managed Master Secret | Stores administrative credentials used by the database bootstrap task. |
| Deletion Protection and Snapshot Policy | Protects against database deletion and requests a snapshot according to the template policy. |

### Deployment Configuration

The Database stack uses the following settings:

| Parameter or Setting | Value |
|---|---|
| `DBProvisionMode` | `Fresh` |
| Snapshot Identifier | Empty |
| Deletion Protection | Enabled |
| Database Name | `tindy` |
| Master Username | `tindy_admin` |

With `Fresh` mode, Amazon RDS creates a new database instead of restoring an existing snapshot.

The `tindy_admin` account is used for database administration and initialization. The backend API will use separate database credentials prepared in a later step.

### Deployment Result

The Database stack was created successfully with the status:

`CREATE_COMPLETE`

CloudFormation generated **5 Outputs**, providing:

- `DBEndpointAddress`: Database endpoint address.
- Database port: PostgreSQL connection port.
- Database identifier: RDS instance identifier.
- `DBMasterSecretArn`: ARN of the administrative credentials stored in Secrets Manager.
- `DBProvisionMode`: Database provisioning mode.

These Outputs will be used during Runtime configuration and database bootstrap.

Schema creation, migrations, and demo data seeding are covered in **Section 5.5 — Runtime**.

## 5.4.2 — Deploy Integrations

After creating the database, the next step is to deploy supporting services using `integrations.yaml`.

This stack handles PDF malware scanning, scan result delivery, and operational alerts.

### Main Components

| Component | Purpose |
|---|---|
| GuardDuty Malware Protection | Scans new objects under `certificates/pdf/` and `ai-input/pdf/` in the media bucket. |
| Object Tagging | Records scan results as S3 object tags to support access controls. |
| Amazon EventBridge | Routes malware scan result events to the queue. |
| Amazon SQS and Queue Policy | Receives scan result messages for the API worker. |
| Dead-letter Queue and CloudWatch Alarm | Retains failed messages according to the redrive policy and detects message backlog. |
| Amazon SNS | Sends operational notifications to a confirmed email recipient. |
| ALB/API Alarms | Monitors ALB and API metrics after Runtime deployment. |

### Deployment Configuration

Integrations uses the media bucket created in the Foundation stack.

GuardDuty Malware Protection is configured for two S3 prefixes:

- `certificates/pdf/`
- `ai-input/pdf/`

When new objects are uploaded within these prefixes, GuardDuty scans them and records the results.

Scan result events are delivered through EventBridge to Amazon SQS, where the API worker can process them.

During the initial deployment, both ALB metric dimensions are left empty because Runtime has not yet been deployed.

After Runtime creates the new ALB and target group, Integrations will be updated with the corresponding metric dimensions to enable the required alarms.

### Deployment Verification

After creating the Integrations stack, the following checks were performed:

1. Confirm the CloudFormation stack reaches `CREATE_COMPLETE`.
2. Verify the GuardDuty Malware Protection plan is **Active**.
3. Confirm object tagging for scan results is enabled.
4. Verify both PDF prefixes are configured correctly.
5. Confirm the Amazon SNS email subscription.

### Deployment Result

The Integrations stack was created successfully with the status:

`CREATE_COMPLETE`

CloudFormation generated **7 Outputs**.

The verified results include:

- The SNS email subscription was confirmed.
- The GuardDuty Malware Protection plan is Active.
- Object tagging is enabled.
- Both PDF prefixes were verified.

Application-level PDF upload testing will be performed after the API and database schema are ready.

## Dependencies Between Stacks

Each stack uses Outputs from previously deployed components to configure its resources.

| Dependency | Required References |
|---|---|
| Network → Database | `PrivateDbSubnetIds`, `DatabaseSecurityGroupId` |
| Foundation → Integrations | `MediaBucketName` |
| Database → Runtime | `DBEndpointAddress`, `DBMasterSecretArn`, `DBProvisionMode` |
| Integrations → Runtime | `GuardDutyProtectionPlanArn`, `GuardDutyQueueUrl` |
| Runtime → Integrations | New ALB and target group metric dimensions for alarms |

Using CloudFormation Outputs allows the stacks to share the correct resource references without manually entering AWS resource identifiers.

## Deployment Summary

Both Database and Integrations were deployed successfully.

| Component | Result |
|---|---|
| Database | `CREATE_COMPLETE` |
| Database Provision Mode | `Fresh` |
| Database Outputs | 5 |
| Integrations | `CREATE_COMPLETE` |
| Integrations Outputs | 7 |
| SNS Subscription | Confirmed |
| GuardDuty Protection Plan | Active |
| Object Tagging | Enabled |
| PDF Prefixes | Both verified |

The database infrastructure is ready for schema initialization and application data preparation.

Integrations also provides the services required for PDF malware scan result processing and operational alerts.

**The next stage is Runtime deployment, including database migrations and demo data preparation.**

## Detailed Deployment Guides

The complete procedures and supporting screenshots are available in the following sections:

| Guide | Description |
|---|---|
| [5.4.1 — Create a Fresh Database](/my_aws_workshop/5-workshop/5.4-database-integrations/5.4.1-database/) | Fresh database parameters, networking configuration, options, and RDS Outputs. |
| [5.4.2 — Deploy Integrations](/my_aws_workshop/5-workshop/5.4-database-integrations/5.4.2-integrations/) | PDF malware scanning configuration, SNS confirmation, and GuardDuty protection plan verification. |
