---
title: "RUN BOOTSTRAP, MIGRATIONS AND SEED"
date: 2026-11-19
weight: 4
chapter: false
pre: "<b> 5.5.4 </b>"
---

Runtime reached UPDATE_COMPLETE with all three database task definitions. The following steps are instructions awaiting execution evidence. Keep API `DesiredCount = 0` and Edge in maintenance while preparing the database.

## Fresh database sequence

| **Task**                       | **Purpose**                                                                                                               | **Completion gate**                                               |
| :----------------------------- | :------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------- |
| `tindy-runtime-db-bootstrap:2` | Use the master credential to create API/migration roles, configure schema permissions and default grants for new objects. | Container exit code 0 and `Database bootstrap complete.` in logs. |
| `tindy-runtime-migration:5`    | Run `migrate-db` with migration credentials to create/update application schema.                                          | Exit code 0 and reviewed migration logs.                          |
| `tindy-runtime-seed:5`         | Run `seed-demo --fresh` to create demonstration data after migrations.                                                    | Exit code 0 and reviewed seed logs.                               |

Run each task sequentially and verify its result before continuing.

## 1. Run DB bootstrap

Open **Amazon ECS → Clusters → tindy-runtime → Tasks → Run new task**. Console field names may vary by version.

| **Field**              | **Value**                                              | **Purpose**                                              |
| :--------------------- | :----------------------------------------------------- | :------------------------------------------------------- |
| Compute / Launch type  | FARGATE                                                | Run the container defined by the task definition.        |
| Task definition family | `tindy-runtime-db-bootstrap`                           | Prepare PostgreSQL roles and grants.                     |
| Revision               | `2`                                                    | Current revision recorded in Runtime Outputs.            |
| Desired tasks          | `1`                                                    | Launch one bootstrap task.                               |
| Platform version       | LATEST                                                 | Use the current Fargate platform.                        |
| VPC                    | `vpc-0209344341dce92fb`                                | VPC created by Network.                                  |
| Subnets                | `subnet-01748b133fa7297ac`, `subnet-0a7df83a7449da864` | Network public task subnets.                             |
| Security group         | `sg-03032e44e0d9f2bf7`                                 | MigrationSecurityGroupId with database access.           |
| Public IP              | ENABLED                                                | Allow image pulls and RDS CA download over the Internet. |

Select the existing security group by its ID. Keep the `db-bootstrap` container command and environment unchanged. Its task definition references Secrets Manager credentials and uses TLS for RDS connections.

**📸 Capture task configuration and Networking before selecting Run/Create.**

## 2. Verify bootstrap

Open the new task, wait for **Stopped**, then check that container `db-bootstrap` has **Exit code 0**. Stopped alone does not establish success.

Open **Logs** and find `Database bootstrap complete.`. The log group is `/ecs/tindy-runtime`, with the `db-bootstrap` stream prefix.

**📸 Capture the container status/exit code and completion log.** Investigate a nonzero exit code or missing completion message before running migrations.

## 3. Migrations and seed

Once bootstrap is verified, use the same cluster and Networking configuration to run migration revision 5. Check the exit code and logs before running seed revision 5. Further details and result screenshots will be added as each step is performed.

## Recorded bootstrap configuration screens

### Select the cluster and verify zero API tasks

The ECS list shows `tindy-runtime` with one service and no running tasks. Its `tindy-api` service is Active with `0/0 Tasks running`. This is the expected preparation state for the Fresh database while the API remains stopped.

[ECS cluster list](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44a-ecs-clusters.png)

[API service at zero tasks](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44b-runtime-service-zero.png)

Open **Tasks → Run new task**. The captured list filters desired status **Stopped** and contains no tasks. After launch, adjust the filter to find the running or stopped bootstrap task.

[Tasks tab before bootstrap](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44c-runtime-tasks.png)

### Task details

Select family `tindy-runtime-db-bootstrap`, revision `2` and Desired tasks `1`. The family identifies bootstrap, the revision selects the definition created by CloudFormation, and Desired tasks controls how many tasks this run launches. Task group may remain empty.

[Bootstrap task details](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44d-bootstrap-task-details.png)

### Environment and compute

Existing cluster is `tindy-runtime`. Under Compute configuration, select **Launch type → Fargate** and platform version **LATEST**. Troubleshooting and Monitoring are collapsed. Keep their default configuration for this run.

[Fargate and platform version](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44e-bootstrap-fargate.png)

### Networking

The screenshot confirms VPC `vpc-0209344341dce92fb`, subnets `tindy-ecs-public-a` and `tindy-ecs-public-b`, security group `sg-03032e44e0d9f2bf7` (`tindy-migration-sg`), and Public IP **Turned on**. Public subnets with a public IP provide outbound connectivity for image pulls and CA downloads. The migration security group supplies the configured source for database preparation access.

[Bootstrap networking](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44f-bootstrap-network.png)

**Verified progress:** the pre-launch configuration matches the instructions. Task launch and completion have not yet been evidenced. Next, select Run/Create, open the task and verify its exit code and logs using section 2 above.

### Bootstrap launched

Task `e5c9c5841c004c6eb10289e9a40be2d9` appeared in the task list. The screenshot records Last status **Deprovisioning**, Desired status **Stopped**, and Health status **Unknown**. ECS is releasing task resources. Open the container details to verify its exit code and Logs before confirming bootstrap success or running migrations.

[Bootstrap deprovisioning](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-45a-bootstrap-deprovisioning.png)

### Bootstrap exited with code 0

Task `e5c9c5841c004c6eb10289e9a40be2d9`, using `tindy-runtime-db-bootstrap:2`, reached **Stopped**. Container `db-bootstrap` details confirm **Exit code 0**. The message `Essential container in task exited` describes the essential container ending. Together with exit code 0, this records successful process completion for the one-off task.

[Bootstrap stopped with exit code 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-45b-bootstrap-exit-zero.png)

Exit-code evidence is recorded. Open Logs and capture `Database bootstrap complete.` before proceeding to migrations. Log evidence has not yet been provided at this milestone.

### Logs confirm bootstrap completion

The `db-bootstrap` container recorded all four messages covering role creation over verified TLS, grants for objects created by the migration role, API role verification over verified TLS, and `Database bootstrap complete.`. Together with the earlier exit code 0, these confirm successful bootstrap completion.

[Bootstrap completion logs](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-46-bootstrap-complete-logs.png)

## 4. Run migrations

Return to **ECS → Clusters → tindy-runtime → Tasks → Run new task**. Select family `tindy-runtime-migration`, revision `5`, Desired tasks `1`, launch type **Fargate**, and platform **LATEST**. Keep the task definition command `migrate-db` and environment unchanged.

Use VPC `vpc-0209344341dce92fb`, public task subnets `subnet-01748b133fa7297ac` and `subnet-0a7df83a7449da864`, security group `sg-03032e44e0d9f2bf7`, and Public IP **Turned on**. This task uses the prepared migration connection secret to update the schema.

**📸 Capture Task details and Networking before selecting Run/Create.** After launch, open the task, wait for Stopped and verify container `migration` has exit code 0. **📸 Capture the exit code and migration completion logs.** Review failures before running seed. Keep API DesiredCount 0 and Edge in maintenance.

Migration and seed execution remain unverified at this milestone.

### Recorded migration configuration screens

Before creating the migration task, the bootstrap task is Stopped and the cluster reports zero running/pending tasks. The Tasks launched banner still references bootstrap task `e5c9c5841c004c6eb10289e9a40be2d9`. This screenshot records the migration starting point and does not yet establish migration launch.

[Cluster before migration](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47a-before-migration.png)

**Task details:** family `tindy-runtime-migration`, revision `5`, Desired tasks `1`, with Task group empty. This definition runs `migrate-db` to create/update the schema using the prepared migration credential. Desired tasks here controls the one-off launch count. The API service remains at DesiredCount 0.

[Migration task details](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47b-migration-task-details.png)

**Environment:** existing cluster `tindy-runtime`, Compute options set to Launch type, launch type Fargate, and platform version LATEST. These settings select compute for the migration task.

[Migration Fargate configuration](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47c-migration-fargate.png)

**Networking:** VPC `vpc-0209344341dce92fb`, public subnets `subnet-0a7df83a7449da864` and `subnet-01748b133fa7297ac`, existing security group `sg-03032e44e0d9f2bf7` (`tindy-migration-sg`), and Public IP Turned on. These settings reuse the network configuration that successfully ran bootstrap for image pulls and RDS connectivity under the security group rules.

[Migration networking](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47d-migration-network.png)

The screenshots confirm the pre-launch configuration matches the guide. Select Run/Create, open the new migration task, then inspect the `migration` container exit code and Logs. Migration results remain unverified at this milestone.

### Migration completed

Task `d6839873357f48e2a4cc830dbc480677` used `tindy-runtime-migration:5` with command `migrate-db` and reached Stopped with container exit code **0**.

[Migration exit code 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-48-migration-exit-zero.png)

Logs confirm `Database migration complete. Applied history: 26. Latest migration: 20261112124706_Media_AllowAiPdfQuarantine.`. The migration command emits completion after successfully applying migrations and reading migration history.

Logs also report that `libgssapi_krb5.so.2` could not be loaded. This diagnostic did not prevent this migration run from completing, as evidenced by exit code 0 and the subsequent completion message. Its underlying cause has not been verified at this milestone.

[Migration completion and library diagnostic](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-49-migration-complete-logs.png)

## 5. Run demo seed for the Fresh database

Open **ECS → Clusters → tindy-runtime → Tasks → Run new task**. Select family `tindy-runtime-seed`, revision `5`, Desired tasks `1`, launch type **Fargate**, platform **LATEST**.

Reuse VPC `vpc-0209344341dce92fb`, public subnets `subnet-01748b133fa7297ac` and `subnet-0a7df83a7449da864`, security group `sg-03032e44e0d9f2bf7`, and Public IP **Turned on**. Keep command `seed-demo --fresh` and environment unchanged. The definition obtains its connection string from the migration secret and demo password from the seed password secret.

**📸 Capture Task details and Networking before Run/Create.** After launch, verify container `seed` reaches Stopped with exit code **0**, then review its seed logs. A newly populated dataset is expected to produce `Demo seed completed.`. **📸 Capture exit code and Logs.** Seed execution remains unverified. Keep API DesiredCount 0 and Edge in maintenance.

### Recorded seed configuration screens

The cluster shows the bootstrap and migration tasks as Stopped, with zero running/pending tasks. Their successful exit codes and logs are recorded above. This is the state before launching seed.

[Cluster before seed](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50a-before-seed.png)

**Task details:** family `tindy-runtime-seed`, revision `5`, Desired tasks `1`, and an empty Task group. The definition executes `seed-demo --fresh` to populate the Fresh database after schema preparation. This count applies to the one-off seed launch. The API service remains at DesiredCount 0.

[Seed task details](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50b-seed-task-details.png)

**Environment:** cluster `tindy-runtime`, Compute options set to Launch type, launch type Fargate, platform version LATEST. Keep the task definition command, environment and secret references unchanged.

[Seed Fargate configuration](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50c-seed-fargate.png)

**Networking:** VPC `vpc-0209344341dce92fb`, public subnets `subnet-0a7df83a7449da864` and `subnet-01748b133fa7297ac`, existing security group `sg-03032e44e0d9f2bf7` (`tindy-migration-sg`), and Public IP Turned on. This reuses the network settings that successfully ran bootstrap and migrations.

[Seed networking](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50d-seed-network.png)

The screenshots confirm the pre-launch configuration matches the guide. Select Run/Create, open the new seed task and verify container `seed` exits with code 0 and completion logs. Seed launch and completion remain unverified at this milestone.

### Seed completed

Task `1e49f952ecc14f6eaed0dfe72a6144b5` used `tindy-runtime-seed:5` with command `seed-demo --fresh`. It reached Stopped with container `seed` reporting **Exit code 0**.

[Seed exit code 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-51-seed-exit-zero.png)

Logs confirm `Demo seed completed.`. SQL INSERT lines shown in the screenshot record data creation. Exit code 0 and the completion message confirm successful seed process execution. Independent record counts and web flows have not yet been verified at this milestone.

[Demo seed completed](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-52-seed-complete-logs.png)

| **Task**             | **Completion evidence**                                       |
| :------------------- | :------------------------------------------------------------ |
| Bootstrap revision 2 | Exit code 0 and `Database bootstrap complete.`                |
| Migration revision 5 | Exit code 0, 26 migrations and `Database migration complete.` |
| Seed revision 5      | Exit code 0 and `Demo seed completed.`                        |

Next, locate the CloudFront VPC Origin service-managed security group in the correct VPC, then update Runtime through a change set using its current `CloudFrontServiceSecurityGroupId` and `DesiredCount = 1`. API execution and health remain unverified. Edge stays in maintenance.
