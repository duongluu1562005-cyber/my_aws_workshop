---
title: "DEPLOY FOUNDATION"
date: 2026-11-19
weight: 1
chapter: false
pre: "<b> 5.3.1 </b>"
---

Foundation supplies frontend/media S3 buckets, the backend ECR repository, application secrets and GitHub OIDC. The main `foundation.yaml` also defines deployment and operations IAM roles.

## 1. Prepare retained resource import

Cleanup retained nine Foundation resources. Restore CloudFormation ownership with `foundation-import.yaml`, prepared from the deployed template. It contains those nine resources, omits the four deleted IAM roles and the entire Outputs section, and defaults `GitHubOidcProviderMode` to `Create` so the provider is included in the import list. The identifiers were exported before cleanup.

After import, use `foundation.yaml` to add IAM roles through change sets as new CloudFront and Runtime inputs become available. Import targets must already exist.

## 2. Open the import workflow

1. Select **N. Virginia (`us-east-1`) → CloudFormation → Stacks**.
2. Open the **Create stack** dropdown and select **With existing resources (import resources)**.

**📸 Capture the import menu.**

![Import existing resources](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01-foundation-import-start.png)

3. Read the introduction and click Next.

![Import introduction](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01a-foundation-import-introduction.png)

4. Select **Upload a template file**, choose the prepared `foundation-import.yaml` from the backup directory.

**📸 Capture the selected filename.**

![Upload import template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-02-foundation-import-template.png)

5. Click Next and verify **Resources to import (9)** under Identify resources.

![Identifiers upper section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03-foundation-identifiers-empty-top.png)

![Identifiers lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03a-foundation-identifiers-empty-bottom.png)

## 3. Fill identifiers from the saved mapping

| **Logical ID**             | **Identifier property** | **Mapping value**                                       |
| :------------------------- | :---------------------- | :------------------------------------------------------ |
| `ApiRepository`            | `RepositoryName`        | `tindy-api` repository name.                            |
| `MediaBucketPolicy`        | `Bucket`                | Media bucket name.                                      |
| `GitHubOAuthSecret`        | `Id`                    | Complete GitHub OAuth secret ARN.                       |
| `DatabaseConnectionSecret` | `Id`                    | Complete DB connection secret ARN.                      |
| `GoogleOAuthSecret`        | `Id`                    | Complete Google OAuth secret ARN.                       |
| `JwtSigningKeySecret`      | `Id`                    | Complete JWT secret ARN.                                |
| `MediaBucket`              | `BucketName`            | Same media bucket name as MediaBucketPolicy.            |
| `FrontendBucket`           | `BucketName`            | Frontend bucket name.                                   |
| `GitHubOidcProvider`       | `Arn`                   | Provider ARN for `token.actions.githubusercontent.com`. |

Copy values from the exported `console-import-identifiers.csv`. Secrets Manager Id fields use resource ARNs. Secret values stay in Secrets Manager.

**📸 Capture all nine filled identifiers before clicking Next, using two screenshots if needed.**

All nine identifiers have been filled and checked against the pre-cleanup mapping.

![Filled identifiers upper section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04-foundation-identifiers-filled-top.png)

![Filled identifiers lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04a-foundation-identifiers-filled-bottom.png)

## 4. Import-stage parameters

Click **Next** to Specify stack details and enter `tindy-foundation`. Use `GitHubOidcProviderMode = Create` to include the existing provider in the import. Set `BackendDeployEnabled`, `InfraPlannerEnabled` and `LifecycleWorkflowsEnabled` to `false`. Leave `BackendApiExecutionRoleName`, `BackendApiTaskRoleName`, `FrontendDeployDistributionId` and `FrontendDeployOidcSubject` empty for this stage.

**📸 Capture the stack name and all parameters before Review.**

Verified values show stack name `tindy-foundation`, `GitHubOidcProviderMode = Create`, all three role-enable options set to `false`, and the four Runtime/Frontend reference fields empty.

![Foundation stack details](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05-foundation-import-stack-details.png)

![Foundation import parameters](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05a-foundation-import-parameters.png)

## 5. Review before importing

Click **Next** to Review. Check the stack name, parameters and all nine resources against the saved mapping. Capture the resource list before executing. If Resource changes are displayed, expect nine items with Action `Import`.

The Review page shows the nine resources and the selected stack parameters.

![Review Foundation resources](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06-foundation-import-review-resources.png)

![Review Foundation parameters](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06a-foundation-import-review-parameters.png)

### Resolve Outputs validation during import

The initial import template still contained Outputs. CloudFormation reported `As part of the import operation, you cannot modify or add [Outputs]`. The change set was not created successfully and import completion remains unverified.

![Outputs validation error](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06b-foundation-import-outputs-error.png)

The corrected `foundation-import.yaml` omits the entire Outputs section. Return to **Specify template**, upload the corrected file, verify all nine identifiers and retain the documented parameters. After import, restore Outputs through an update using the main `foundation.yaml` template.

### Review after template correction

Re-upload the template without Outputs, retaining identifiers and parameters. Change set creation succeeded with **Changes (9)** and all nine actions set to `Import`.

![Corrected resource review](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07-foundation-import-review-resources.png)

![Corrected parameter review](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07a-foundation-import-review-parameters.png)

**📸 Capture all nine Import actions before execution.**

![Nine Import actions](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07b-foundation-nine-import-actions.png)

## 6. Execute import

Click **Import resources** and monitor **Events** for `tindy-foundation` until the stack reaches `IMPORT_COMPLETE`. For import rollback or other errors, inspect Logical ID and Status reason before continuing.

**📸 Capture the stack IMPORT_COMPLETE event and Resources showing nine imported resources.**

Import completed successfully. The Resources screenshot shows stack `tindy-foundation` at `IMPORT_COMPLETE`, **Resources (9)** and `UPDATE_COMPLETE` for displayed resources.

![Foundation imported resources](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09-foundation-imported-resources.png)

Next, update using the main `foundation.yaml` template to restore Outputs and configure IAM roles as deployment prerequisites become available.

## 7. Restore the main template and Outputs

Create change set `tindy-foundation-restore-template` using **Replace existing template → Upload a template file**, choosing `foundation.yaml` from the infrastructure repository. Keep `GitHubOidcProviderMode = Create`. Restore `InfraPlannerEnabled = true` from the pre-cleanup configuration to create the GitHub Actions change set planning role. Its OIDC input is available at this stage.

Keep `BackendDeployEnabled = false` and `LifecycleWorkflowsEnabled = false`. Leave both Runtime role names and both frontend parameters empty until new Runtime and Edge outputs are available.

Acknowledge IAM capabilities and create the change set. Expected Resource changes add only `InfraPlannerRole`; the main template restores Outputs. Review actual changes before execution.

**📸 Capture the main template filename, parameters and Resource changes.** This describes the next step. Update results will be added after verification.

Opened **Stack actions → Create a change set**, selected **Standard change set → Replace existing template → Upload a template file**, and uploaded `foundation.yaml`.

![Foundation after import](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09a-foundation-imported-resources.png)

![Foundation change set menu](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10a-foundation-create-changeset-menu.png)

![Main foundation.yaml template selected](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10-foundation-main-template.png)

The change set is named `tindy-foundation-restore-template`. Verified parameters are `GitHubOidcProviderMode = Create`, `InfraPlannerEnabled = true`, `BackendDeployEnabled = false` and `LifecycleWorkflowsEnabled = false`. Both Runtime role names and both Frontend fields remain empty for this stage.

**📸 Capture the change set name and all parameters.**

![Main Foundation parameters](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11-foundation-main-parameters.png)

![Foundation parameters lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11a-foundation-main-parameters-bottom.png)

Click Next, keep Express mode disabled and rollback enabled, acknowledge IAM capabilities and create the change set.

Change set `tindy-foundation-restore-template` reached `CREATE_COMPLETE` and `AVAILABLE`, with one change adding `InfraPlannerRole` of type `AWS::IAM::Role`.

**📸 Capture Resource changes before execution.**

![Main Foundation change set](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12-foundation-main-changeset.png)

Execute the change set, keeping **Use deletion policy** if displayed. Monitor Events until `tindy-foundation — UPDATE_COMPLETE`. Check Outputs for bucket, ECR, secret, OIDC and `InfraPlannerRoleArn` references needed in subsequent steps.

**📸 Capture the completed stack status and all Outputs.**

The change set was executed with **Use deletion policy**.

![Execute Foundation change set](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12a-foundation-main-execute.png)

The stack reached **UPDATE_COMPLETE**. The Outputs screenshot shows completed stack status and **Outputs (14)**.

![Foundation update completed](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-13-foundation-main-update-complete.png)

![Foundation Outputs upper section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14-foundation-main-outputs-top.png)

![Foundation Outputs lower section](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14a-foundation-main-outputs-bottom.png)

Frontend/media bucket, ECR, application secret, OIDC, Data Protection path and `InfraPlannerRoleArn` references are available again. Backend/frontend deployment and lifecycle roles will be configured when new Runtime and Edge inputs become available. Compare GitHub repository role variables with new Outputs before running their workflows.

Foundation is ready for Network deployment.
