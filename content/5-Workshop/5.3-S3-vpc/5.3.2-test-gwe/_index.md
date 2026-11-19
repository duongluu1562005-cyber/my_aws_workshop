
## 5.3.2 — Deploy Network

### Overview

After completing the Foundation deployment, the next step is to set up the network infrastructure for Tindy.

The `network.yaml` template creates the VPC, subnets, routing components, and security groups required by the Database and Runtime stacks.

The network configuration includes:

- **VPC:** Uses the CIDR block `10.20.0.0/16`.
- **Internet Gateway:** Provides internet connectivity for resources using public routes.
- **Six subnets:** Separate networking for ECS tasks, the internal Application Load Balancer (ALB), and the database.
- **Route tables:** Manage traffic between subnets and their configured routes.
- **Security groups:** Control network access for ALB, ECS, migration tasks, and the database.

The template defines **26 AWS resources** and **9 Outputs** in the `us-east-1` region.

### Step 1: Upload the Network Template

Open the AWS Management Console and select **US East (N. Virginia) — us-east-1**.

Navigate to:

**CloudFormation → Stacks → Create stack → With new resources (standard)**

Under **Specify template**:

1. Select **Choose an existing template**.
2. Choose **Upload a template file**.
3. Upload `network.yaml` from `tindy-infrastructure/cloudformation`.
4. Click **Next**.

![Upload Network template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-15-network-template.png)

### Step 2: Configure the Network Stack

On the **Specify stack details** page, enter the stack name:

`tindy-network`

The `network.yaml` template does not define any parameters, so no additional parameter values are required.

![Network stack name](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16-network-stack-name.png)

Continue to the stack configuration page and add the following tags:

| Key | Value |
|---|---|
| Project | Tindy |
| Environment | tindy |
| Owner | TindyTeam |

These tags help identify and organize the AWS resources associated with the Tindy project.

![Network stack tags](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16a-network-tags.png)

Keep **Express mode disabled** and **rollback enabled**.

Review the stack configuration and submit the CloudFormation stack creation.

### Step 3: Create and Verify the Network Stack

After submitting the stack, CloudFormation begins provisioning the network resources defined in `network.yaml`.

Open the **Events** tab to monitor the deployment.

The expected final status is:

`CREATE_COMPLETE`

If the deployment fails, check the affected resource's **Logical ID** and **Status reason** before proceeding.

The deployment was completed successfully. CloudFormation confirmed that `tindy-network` reached **CREATE_COMPLETE** and generated **9 Outputs**.

![Network deployment completed and Outputs](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-18-network-outputs.png)

### Step 4: Review Network Outputs

The Network stack provides the references needed to deploy the database and application infrastructure.

| Output | Purpose |
|---|---|
| `VpcId` | Identifies the VPC used by Tindy resources. |
| `PublicTaskSubnetIds` | Identifies subnets used by ECS tasks. |
| `PrivateAppSubnetIds` | Identifies subnets for the internal ALB. |
| `PrivateDbSubnetIds` | Identifies private subnets used by Amazon RDS. |
| `AlbSubnetCidrs` | Provides ALB subnet CIDR ranges for trusted proxy configuration. |
| `AlbSecurityGroupId` | Identifies the ALB security group. |
| `EcsSecurityGroupId` | Identifies the ECS security group. |
| `MigrationSecurityGroupId` | Identifies the security group used by migration tasks. |
| `DatabaseSecurityGroupId` | Identifies the database security group. |

These Outputs allow subsequent CloudFormation stacks to use the correct network resources without manually entering their identifiers.

### Deployment Result

The Network stack was created successfully.

| Verification | Result |
|---|---|
| Stack name | `tindy-network` |
| AWS Region | `us-east-1` |
| VPC CIDR | `10.20.0.0/16` |
| Resources defined | 26 |
| Stack parameters | None |
| Stack status | `CREATE_COMPLETE` |
| Outputs | 9 |
| Resource tags | Configured |

The Network infrastructure is now available for the remaining Tindy deployment stages.

**Network is ready for the next stage: Database deployment.**
