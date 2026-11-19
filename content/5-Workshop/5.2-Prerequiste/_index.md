
# Deployment Prerequisites

Before deploying Tindy, the required AWS access, infrastructure templates, backend container image, and resource references must be prepared. The deployment takes place in **N. Virginia (`us-east-1`)**, following the requirements defined in the CloudFormation templates.

## 1. Tools and Access

The following tools and permissions are required:

- **AWS Console:** Used to upload CloudFormation templates, review change sets, monitor Events/Outputs, and capture screenshots.
- **AWS CLI:** Requires an authenticated profile. This workshop uses `tindy-admin`.
- **PowerShell and `curl.exe`:** Used to run CLI commands and perform HTTP checks.
- **AWS permissions:** Access to the services defined in the templates, including CloudFormation, IAM, EC2 Networking, S3, ECR, Secrets Manager, RDS, ECS, ELB, CloudFront, GuardDuty, EventBridge, SQS, SNS, and CloudWatch. IAM capabilities must be acknowledged when requested.
- **Email and OAuth access:** Access to the notification mailbox is needed to confirm SNS subscriptions. OAuth verification also requires access to Google/GitHub application callback settings to configure the new hostname.

Verify the AWS CLI session on the deployment machine:

```powershell
aws sts get-caller-identity --profile tindy-admin --region us-east-1 --no-cli-pager
```

**📸 Note:** Review and hide AWS account identifiers in identity screenshots before publishing them.

## 2. Source Code and Container Image

The infrastructure uses six YAML CloudFormation templates located in `tindy-infrastructure/cloudformation`, as introduced in Section 5.1.

Prepare the backend container image in the ECR repository `tindy-api` and pin its image digest to ensure the API and database preparation tasks run with the same image version.

The Runtime `ApiImage` parameter follows this format:

```text
<account-id>.dkr.ecr.us-east-1.amazonaws.com/tindy-api@sha256:<digest>
```

During this workshop, the image digest was checked against ECR and the Runtime parameter recorded before infrastructure cleanup.

The frontend is deployed using the release workflow from the frontend repository.

## 3. Redeployment Information

This workshop starts with an AWS environment that has already been deployed. Therefore, important infrastructure information must be backed up before deleting the Foundation stack.

The exported information includes:

- Logical-to-physical resource mappings.
- Stack configuration parameters.
- Stack Outputs.
- The currently deployed CloudFormation template.

These files are stored in the Worklog directory `backup/tindy-foundation-before-cleanup`.

A total of **nine resources are retained**, including:

- Two S3 buckets.
- The media bucket policy.
- The ECR repository.
- Four application secrets.
- GitHub OIDC.

The prepared `foundation-import.yaml` template is used to restore CloudFormation stack ownership of these resources. The stack is then updated using `foundation.yaml`.

The detailed procedure is covered in **Section 5.3.1**.

## 4. Deployment Configuration

The workshop uses the following deployment settings:

| **Setting** | **Configuration** |
| :--- | :--- |
| Database | Create a fresh database with an empty snapshot identifier, then run bootstrap, migrations, and seed tasks. |
| Region | `us-east-1`. |
| CloudFront | Create a new distribution and update hostname references. |
| Initial Runtime | Set `DesiredCount = 0` until the database and secrets are ready. |
| Traffic | Keep Edge in maintenance mode until the API and target group are healthy. |
| Tags | `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. |

Deployment parameters are filled using Outputs from the newly created stacks.

The retained application connection secret must also be updated to connect to the fresh database.

Sensitive information, including passwords, JWT keys, and OAuth client secrets, is managed within AWS.

## 5. Screenshots and Video

Screenshots captured during the workshop are organized into two directories:

- `static/images/5-Workshop/cleanup`: Screenshots of infrastructure cleanup.
- `static/images/5-Workshop/deploy`: Screenshots of infrastructure deployment.

The following sections identify when screenshots should be captured, including parameter configuration, change set review, stack completion, and Outputs.

The **demo video** is recorded after completing database seeding and application verification.

The planned demonstration includes:

1. Logging into the application.
2. Discovering and browsing projects.
3. Submitting a project application.
4. Reviewing applications as a Project Leader.

Each deployment section separately records verified results and pending steps to clearly document the actual deployment progress.
