---
title: "PREPARE FRESH DATABASE SECRETS"
date: 2026-11-22
weight: 3
chapter: false
pre: "<b> 5.3.3 </b>"
---

AWS CLI confirmed ECS service ACTIVE with Desired/Running/Pending all zero and no failures. The VPC Origin ID read directly from Outputs reports Deployed.

## 1. Initialize secret references

The existing `scripts/prepare-runtime-secrets.ps1` reads Foundation and Database Outputs, verifies zero task counts and prepares application/migration/seed secrets. Existing AWSCURRENT versions are preserved by the script. Verify the endpoint and connection configuration before DB tasks.



Run in PowerShell 7 from the infrastructure repository.

```powershell
Set-Location 'D:\FPT\Semester 6\OJT\aws\tindy-platform\tindy-infrastructure'
.\scripts\prepare-runtime-secrets.ps1 -AwsProfile tindy-admin | Format-List *
```

## 2. Record results

The script outputs `DBEndpointAddress`, `DBMasterSecretArn`, `MigrationConnectionSecretArn` and `SeedPasswordSecretArn`. These references enable the bootstrap/migration/seed definitions through a Runtime update.



**📸 Capture the command and four output fields, milestone deploy-39.** Record errors for diagnosis before proceeding.



This script prepares configuration. Database bootstrap, migration and seed run sequentially as one-off ECS tasks later. The script completed successfully and returned all four fields shown below.



[Runtime secret preparation results](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-39-runtime-secrets-prepared.png)



The screenshot records the current endpoint and three ARNs for task configuration. Next, verify API/migration connection settings against the Fresh DB, then update the four Runtime parameters through a change set while retaining DesiredCount 0.

## 3. Verify connection settings before bootstrap

Secret JSON was read and parsed in memory through AWS CLI, reporting only configuration fields and comparison results. API uses `tindy_api`, migrations use `tindy_migrator`. Both reference the Fresh DB endpoint, database `tindy`, port `5432`, TLS `VerifyFull` and CA bundle `/app/certs/rds-us-east-1-bundle.crt`. JSON password and connection string password match for each secret.

## 4. Enable Runtime DB task definitions

Create change set `tindy-runtime-enable-db-tasks` using the existing Runtime template. Keep DesiredCount 0 and other parameters. Fill `DBEndpointAddress`, `DBMasterSecretArn`, `MigrationConnectionSecretArn` and `SeedPasswordSecretArn` from the script output.



Template conditions enable three execution role/task definition pairs for bootstrap, migration and seed, expecting six Add actions. Review the actual change set before execution.



**📸 Capture the four parameters with DesiredCount 0, followed by Resource changes.** Parameter entry is recorded below. Change set creation/execution and task results remain pending verification.

### DB task parameter entry screenshots

Selected **Create a change set**, retained the existing template and named it `tindy-runtime-enable-db-tasks`.



[DB task change set menu](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40a-runtime-db-tasks-menu.png)



[Existing Runtime template](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40b-runtime-db-tasks-existing-template.png)



[DB task change set name](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40c-runtime-db-tasks-changeset-name.png)



The four new values match the script output. `DBProvisionMode = Fresh`, `DesiredCount = 0` and `AiPdfUploadsEnabled = false`. Image, Edge, networking and Integrations references match the initial Runtime configuration.



[Image and Edge inputs](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40d-runtime-db-tasks-image-edge.png)



[Database inputs and DesiredCount](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40-runtime-db-task-parameters.png)



[Migration secret and Integrations inputs](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40e-runtime-db-tasks-migration.png)



[Seed secret and networking](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40f-runtime-db-tasks-seed-network.png)



Click Next, retain options and acknowledge IAM capabilities, then create the change set. **📸 Capture Resource changes at CREATE_COMPLETE and AVAILABLE**, expecting six Add actions. Change set creation succeeded; see the results below.

### Review DB task change set

IAM capabilities were acknowledged. Change set `tindy-runtime-enable-db-tasks` reached **CREATE_COMPLETE** and **AVAILABLE**.



[DB task IAM acknowledgement](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40g-runtime-db-tasks-iam.png)



The six Add actions create `DbBootstrapExecutionRole`/`DbBootstrapTaskDefinition`, `MigrationExecutionRole`/`MigrationTaskDefinition`, and `SeedExecutionRole`/`SeedTaskDefinition`.



[Six DB task Add actions](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-41-runtime-db-tasks-changeset.png)



Execute the change set, retaining Use deletion policy if requested, and wait for Runtime UPDATE_COMPLETE. Open Outputs and record `DbBootstrapTaskDefinitionArn`, `MigrationTaskDefinitionArn` and `SeedTaskDefinitionArn`.



**📸 Capture completed stack update and DB task Outputs.** This update creates task definitions. Bootstrap, migrations and seeding run sequentially after the update completes. Completed execution evidence is recorded below.



The execution dialog selects Roll back all stack resources and Use deletion policy. Execute the change set and monitor Events until UPDATE_COMPLETE.



[DB task change set execution confirmation](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-41a-runtime-db-tasks-execute-confirm.png)

## 5. Runtime update results

The stack reached **UPDATE_COMPLETE** with **Outputs (9)**. New Outputs identify `tindy-runtime-db-bootstrap:2`, `tindy-runtime-migration:5` and `tindy-runtime-seed:5`.



[Completed Runtime update and DB task Outputs](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-43-runtime-db-task-outputs.png)



Definitions are ready. Run bootstrap, migrations and seed sequentially, verifying exit code 0 and completion logs before the next task.
