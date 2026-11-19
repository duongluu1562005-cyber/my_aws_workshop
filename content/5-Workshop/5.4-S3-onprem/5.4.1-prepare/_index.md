
## 5.4.1 — Create a Fresh Database

### Overview

After completing the Network deployment, the next step is to create the PostgreSQL database for Tindy.

The `database.yaml` template provisions an Amazon RDS instance and configures the network and security settings required by the application.

The main components include:

- **Amazon RDS:** Runs PostgreSQL in a Single-AZ configuration.
- **DB Subnet Group:** Places the database in two private DB subnets from the Network stack.
- **PostgreSQL Parameter Group:** Enables `rds.force_ssl` to require encrypted connections.
- **RDS-managed Master Secret:** Stores the database administrator password securely in AWS Secrets Manager.
- **Deletion Protection:** Helps prevent accidental deletion of the database.

This deployment uses **Fresh provisioning**, creating a new database named `tindy` with the master user `tindy_admin`.

Before deployment, the AWS CLI confirmed that PostgreSQL version `17.11` was available in `us-east-1`.

Database migrations and demo data preparation will be completed during Runtime setup.

### Step 1: Upload the Database Template

Open the AWS Management Console and select **US East (N. Virginia) — us-east-1**.

Navigate to:

**CloudFormation → Stacks → Create stack → With new resources (standard)**

Under **Specify template**:

1. Select **Choose an existing template**.
2. Choose **Upload a template file**.
3. Upload `database.yaml` from the infrastructure repository.
4. Click **Next**.

![Upload Database template](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-19-database-template.png)

### Step 2: Configure Fresh Database Parameters

On the **Specify stack details** page, enter the stack name:

`tindy-database`

Configure the database parameters as follows:

| Parameter | Value |
|---|---|
| `DBProvisionMode` | `Fresh` |
| `DBSnapshotIdentifier` | Empty |
| `EnableDeletionProtection` | `true` |
| `DatabaseSecurityGroupId` | From Network Outputs |
| `PrivateDbSubnetIds` | Both private DB subnets from Network Outputs |

Using `Fresh` mode creates a new PostgreSQL database instead of restoring an existing snapshot.

The database security group and subnets must match the resources created by `tindy-network`.

Deletion protection is enabled to help prevent accidental removal of the RDS instance.

![Fresh Database parameters](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20-database-fresh-parameters.png)

### Step 3: Configure Stack Options

Continue to the stack configuration page and add the following tags:

| Key | Value |
|---|---|
| Project | Tindy |
| Environment | tindy |
| Owner | TindyTeam |

These tags help organize and identify the database resources associated with Tindy.

![Database stack tags](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20a-database-tags.png)

Keep the following options:

- **Express mode:** Disabled.
- **Rollback:** Enabled.
- **Deletion policy option:** Use deletion policy.

![Database stack options](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20b-database-options.png)

Review the stack name, template, parameters, and deployment settings before creating the stack.

![Review Database stack](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20c-database-review.png)

### Step 4: Create and Verify the Database

Submit the CloudFormation stack creation.

CloudFormation begins provisioning the RDS instance, database subnet group, and PostgreSQL parameter group.

Open the **Events** tab to monitor the deployment until the stack reaches:

`CREATE_COMPLETE`

If the deployment fails, check the affected resource's **Logical ID** and **Status reason**.

The deployment completed successfully, and CloudFormation displayed **5 Outputs**.

![Database deployment completed and Outputs](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-22-database-outputs.png)

### Step 5: Review Database Outputs

After the stack reaches `CREATE_COMPLETE`, open its **Outputs** tab.

The verified database configuration includes:

| Setting | Result |
|---|---|
| Stack name | `tindy-database` |
| Provision mode | `Fresh` |
| PostgreSQL version | `17.11` |
| Database name | `tindy` |
| Master username | `tindy_admin` |
| Database identifier | `tindy-database-postgres` |
| Database port | `5432` |
| Stack status | `CREATE_COMPLETE` |
| Outputs | 5 |

The Outputs provide the database endpoint, port, identifier, master secret ARN, and provision mode.

Two important references for the next deployment stage are:

- `DBEndpointAddress`: The PostgreSQL endpoint used for database connectivity.
- `DBMasterSecretArn`: The Secrets Manager ARN containing the RDS-managed master credentials.

These references will be used during database bootstrap and Runtime configuration.

### Next Steps

The database infrastructure is now available, but the application schema and demo data have not yet been initialized.

During Runtime setup, the following tasks will be completed:

1. Run the database bootstrap process.
2. Apply database migrations.
3. Seed the required demo data.
4. Verify and update the retained application database connection secret for the new RDS instance.
5. Prepare the backend API to connect using its separate database credentials.

The RDS-managed master secret is used for database administration and bootstrap, while the API uses its own application credential.

### Deployment Result

The Fresh Database deployment completed successfully.

| Verification | Result |
|---|---|
| Database stack | `tindy-database` |
| Provisioning | Fresh database |
| Database networking | Private DB subnets |
| Deletion protection | Enabled |
| Stack status | `CREATE_COMPLETE` |
| Outputs | 5 |
| Schema and demo data | Pending Runtime setup |

The PostgreSQL database has been provisioned successfully and is ready for application initialization.

**The next stage is Integrations deployment, followed by Runtime setup and database preparation.**
