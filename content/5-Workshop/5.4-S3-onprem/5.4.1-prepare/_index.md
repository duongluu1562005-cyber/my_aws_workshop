---
title: "CREATE A FRESH DATABASE"
date: 2026-11-19
weight: 1
chapter: false
pre: "<b> 5.4.1 </b>"
---

`database.yaml` creates an RDS PostgreSQL instance, a subnet group using two private DB subnets and a parameter group requiring TLS. Fresh provisioning creates database `tindy` with master user `tindy_admin`, with RDS managing its master password in Secrets Manager. AWS CLI confirmed PostgreSQL `17.11` is available in `us-east-1` before this step.

## Create the stack

1. Select **CloudFormation → Create stack → With new resources (standard)** in N. Virginia and upload `database.yaml` from the infrastructure repository.

**📸 Capture the selected filename.**



![Upload database.yaml](/my_aws_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-19-database-template.png)

2. Name the stack `tindy-database`. Set `DBProvisionMode = Fresh`, leave `DBSnapshotIdentifier` empty and keep `EnableDeletionProtection = true`.
3. Select `DatabaseSecurityGroupId` and both `PrivateDbSubnetIds` using Outputs from the newly created Network stack.

**📸 Capture the stack name and all parameters.**



![Fresh Database parameters](/my_aws_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20-database-fresh-parameters.png)

4. Add tags `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Keep Express mode disabled and rollback enabled, review and create the stack.
5. Wait for `CREATE_COMPLETE`, then record Outputs including the endpoint, port, provision mode and master secret ARN where exported for Fresh.

**📸 Capture completed stack status and Outputs.**



Bootstrap, migrations and demo seeding take place during Runtime setup before enabling the API. Verify and update the retained API connection secret for the new DB. Verified parameters show stack `tindy-database`, Fresh provisioning, empty snapshot identifier and enabled deletion protection. Security group and both selected DB subnets match the new Network Outputs. Database creation is complete, with CREATE_COMPLETE and Outputs evidence below.

## Verified deployment results

Tags, disabled Express mode, rollback and **Use deletion policy** were configured before reviewing the stack name and template.



![Database tags](/my_aws_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20a-database-tags.png)



![Database options](/my_aws_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20b-database-options.png)



![Database review](/my_aws_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20c-database-review.png)



Stack `tindy-database` reached **CREATE_COMPLETE** with **Outputs (5)**, provisioning mode `Fresh`, port `5432` and identifier `tindy-database-postgres`. `DBEndpointAddress` and `DBMasterSecretArn` are available for subsequent bootstrap.



**📸 The Outputs image also records completed stack status.**



![Fresh Database completed and Outputs](/my_aws_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-22-database-outputs.png)



The RDS-managed master secret supports bootstrap. The API uses a separate credential through its application connection secret, prepared during Runtime setup. Integrations deployment is next.
