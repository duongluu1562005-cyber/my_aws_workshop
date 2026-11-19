
## 5.3.1 — Deploy Foundation

### Overview

The Foundation stack manages the AWS resources shared across the Tindy application, including:

- **Amazon S3:** Stores frontend files and uploaded media.
- **Amazon ECR:** Stores backend container images.
- **AWS Secrets Manager:** Stores database credentials, JWT signing keys, and OAuth configuration.
- **GitHub OIDC:** Enables GitHub Actions to authenticate with AWS.
- **IAM roles:** Provides permissions for infrastructure planning, application deployment, and operational workflows.

Because Tindy had previously been deployed, nine Foundation resources were retained during cleanup. These resources must be imported back into CloudFormation before restoring the main `foundation.yaml` template.

The deployment consists of two stages:

1. Import the nine existing resources using `foundation-import.yaml`.
2. Restore the main `foundation.yaml` template, its Outputs, and the IAM role that can be configured at this stage.

### Step 1: Prepare the Foundation Import

The `foundation-import.yaml` template was prepared from the original Foundation template to restore CloudFormation management of the retained resources.

For the import operation, the template:

- Includes the nine existing resources.
- Excludes the four deleted IAM roles.
- Removes the entire `Outputs` section.
- Sets `GitHubOidcProviderMode` to `Create` so the existing OIDC provider is included in the import.

Resource identifiers were saved before cleanup in `console-import-identifiers.csv`.

**Important:** Resource import does not create new resources. All nine resources must already exist in AWS.

### Step 2: Start the Import Process

Open the AWS Management Console and select **US East (N. Virginia) — us-east-1**.

Navigate to:

**CloudFormation → Stacks → Create stack → With existing resources (import resources)**

![Foundation import menu](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01-foundation-import-start.png)

The import introduction page explains how existing AWS resources can be brought under CloudFormation management.

Click **Next** to continue.

![Foundation import introduction](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01a-foundation-import-introduction.png)

On the **Specify template** page:

1. Select **Upload a template file**.
2. Choose `foundation-import.yaml`.
3. Click **Next**.

![Upload Foundation import template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-02-foundation-import-template.png)

### Step 3: Identify the Existing Resources

CloudFormation displays **Resources to import (9)**.

Each resource requires an identifier that matches the resource already deployed in AWS.

The following table summarizes the required mapping.

| Logical ID | Identifier | Value |
|---|---|---|
| ApiRepository | RepositoryName | Existing `tindy-api` repository name |
| MediaBucketPolicy | Bucket | Existing media bucket name |
| GitHubOAuthSecret | Id | GitHub OAuth secret ARN |
| DatabaseConnectionSecret | Id | Database connection secret ARN |
| GoogleOAuthSecret | Id | Google OAuth secret ARN |
| JwtSigningKeySecret | Id | JWT signing secret ARN |
| MediaBucket | BucketName | Existing media bucket name |
| FrontendBucket | BucketName | Existing frontend bucket name |
| GitHubOidcProvider | Arn | Existing GitHub OIDC provider ARN |

The initial form shows the identifier fields before they are filled.

![Empty resource identifiers - upper section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03-foundation-identifiers-empty-top.png)

![Empty resource identifiers - lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03a-foundation-identifiers-empty-bottom.png)

Enter the values from the saved `console-import-identifiers.csv` file.

For Secrets Manager resources, use the complete secret ARNs rather than secret names or secret values.

The media bucket policy and media bucket must reference the same existing bucket.

After entering all nine identifiers, verify them against the saved mapping.

![Completed resource identifiers - upper section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04-foundation-identifiers-filled-top.png)

![Completed resource identifiers - lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04a-foundation-identifiers-filled-bottom.png)

### Step 4: Configure Import Parameters

Click **Next** to open **Specify stack details**.

Set the stack name to:

` tindy-foundation `

Use the following parameters for the import stage.

| Parameter | Value |
|---|---|
| GitHubOidcProviderMode | Create |
| BackendDeployEnabled | false |
| InfraPlannerEnabled | false |
| LifecycleWorkflowsEnabled | false |
| BackendApiExecutionRoleName | Empty |
| BackendApiTaskRoleName | Empty |
| FrontendDeployDistributionId | Empty |
| FrontendDeployOidcSubject | Empty |

At this stage, the IAM deployment roles are disabled because the required Runtime and Frontend references are not yet available.

The existing GitHub OIDC provider is included in the import operation.

![Foundation stack details](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05-foundation-import-stack-details.png)

![Foundation import parameters](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05a-foundation-import-parameters.png)

### Step 5: Review and Correct the Import Template

Before starting the import, review the stack parameters and the list of nine resources.

![Review Foundation resources](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06-foundation-import-review-resources.png)

![Review Foundation parameters](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06a-foundation-import-review-parameters.png)

#### Issue: Outputs Validation Error

During the first import attempt, CloudFormation returned the following error:

```text
As part of the import operation, you cannot modify or add [Outputs].
```

![Foundation Outputs validation error](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06b-foundation-import-outputs-error.png)

The error occurred because the import template still contained an `Outputs` section.

CloudFormation did not create the import change set successfully.

**Resolution:**

1. Remove the entire `Outputs` section from `foundation-import.yaml`.
2. Return to the template selection page.
3. Upload the corrected template.
4. Verify the nine resource identifiers again.
5. Keep the previously configured stack parameters.

The corrected template was accepted by CloudFormation.

![Corrected Foundation resource review](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07-foundation-import-review-resources.png)

![Corrected Foundation parameter review](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07a-foundation-import-review-parameters.png)

After correction, CloudFormation successfully generated a change set containing **nine Import actions**.

![Nine Foundation import actions](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07b-foundation-nine-import-actions.png)

### Step 6: Execute the Resource Import

Click **Import resources** to execute the change set.

Monitor the `tindy-foundation` stack until its status becomes:

`IMPORT_COMPLETE`

The import completed successfully, and the Resources tab showed all nine resources managed by CloudFormation.

![Foundation imported resources](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09-foundation-imported-resources.png)

![Foundation imported resources verification](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09a-foundation-imported-resources.png)

At this point, the retained resources have been imported successfully. However, the Foundation stack still needs to be updated with the main template to restore its Outputs and required IAM configuration.

### Step 7: Restore the Main Foundation Template

After the import, open the `tindy-foundation` stack.

Select:

**Stack actions → Create a change set**

![Foundation change set menu](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10a-foundation-create-changeset-menu.png)

Create a **Standard change set** and select:

**Replace existing template → Upload a template file**

Upload the original `foundation.yaml` from the infrastructure repository.

![Select main Foundation template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10-foundation-main-template.png)

Name the change set:

`tindy-foundation-restore-template`

Configure the parameters as follows:

| Parameter | Value |
|---|---|
| GitHubOidcProviderMode | Create |
| InfraPlannerEnabled | true |
| BackendDeployEnabled | false |
| LifecycleWorkflowsEnabled | false |
| BackendApiExecutionRoleName | Empty |
| BackendApiTaskRoleName | Empty |
| FrontendDeployDistributionId | Empty |
| FrontendDeployOidcSubject | Empty |

The main difference from the import stage is that `InfraPlannerEnabled` is now set to `true`.

This enables the IAM role used by GitHub Actions for CloudFormation change set planning.

The backend deployment and lifecycle roles remain disabled because their required inputs will become available after the Runtime and Edge stacks are deployed.

![Main Foundation parameters](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11-foundation-main-parameters.png)

![Main Foundation parameters - lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11a-foundation-main-parameters-bottom.png)

Continue through the remaining configuration:

1. Keep Express mode disabled.
2. Keep rollback enabled.
3. Acknowledge the required IAM capabilities.
4. Create the change set.

### Step 8: Review and Execute the Change Set

CloudFormation created the change set successfully.

Its status reached:

`CREATE_COMPLETE — AVAILABLE`

The resource changes showed one addition:

| Resource | Type | Action |
|---|---|---|
| InfraPlannerRole | AWS::IAM::Role | Add |

The update also restores the `Outputs` section from the main Foundation template.

![Main Foundation change set](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12-foundation-main-changeset.png)

After reviewing the changes, execute the change set.

The **Use deletion policy** option was retained during execution.

![Execute Foundation change set](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12a-foundation-main-execute.png)

Monitor the stack Events until the update finishes.

The stack successfully reached:

`UPDATE_COMPLETE`

![Foundation update completed](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-13-foundation-main-update-complete.png)

### Step 9: Verify Foundation Outputs

After the update, open the **Outputs** tab of `tindy-foundation`.

CloudFormation displayed **14 Outputs**, including references needed by the remaining infrastructure stacks and deployment workflows.

These include:

- Frontend and media S3 bucket references.
- Backend ECR repository references.
- Application secrets stored in Secrets Manager.
- GitHub OIDC provider reference.
- Data Protection resource path.
- `InfraPlannerRoleArn` for GitHub Actions infrastructure planning.

![Foundation Outputs - upper section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14-foundation-main-outputs-top.png)

![Foundation Outputs - lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14a-foundation-main-outputs-bottom.png)

The remaining backend deployment, frontend deployment, and lifecycle IAM roles will be configured after the necessary Runtime and Edge references are available.

GitHub repository role variables should also be checked against the updated Foundation Outputs before running the associated workflows.

### Result

The Foundation deployment completed successfully.

| Verification | Result |
|---|---|
| Resource import | 9 resources imported |
| Import stack status | `IMPORT_COMPLETE` |
| Main template restoration | Completed |
| InfraPlannerRole | Added successfully |
| Final stack status | `UPDATE_COMPLETE` |
| Stack Outputs | 14 Outputs available |

The retained resources are now managed by CloudFormation, the main Foundation template has been restored, and the required shared resource references are available.

**Foundation is ready for the next stage: Network deployment.**
