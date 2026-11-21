---
title: "CREATE RUNTIME WITH DESIREDCOUNT ZERO"
date: 2026-11-22
weight: 2
chapter: false
pre: "<b> 5.3.2 </b>"
---

`runtime.yaml` defines the ECS cluster, API task definition and service, IAM roles, log group, internal ALB, listener, target group, ingress and CloudFront VPC Origin. Optional bootstrap, migration and seed task definitions are enabled through parameters.

## 1. Prepare inputs

AWS CLI reads verified Outputs from Foundation, Network, Database, Integrations and Edge. The new hostname is `d1vcn4g2ifl3k8.cloudfront.net`; maintenance returns HTTP 503.

ECR still contains release image `tindy-api@sha256:1505b06199c2965fd800deab9956cbf47601ce76bc42848b8085dfae5b7f69ea`, matching ApiImage recorded before cleanup. Pin the digest consistently for API and DB tasks.

Create Runtime with `DesiredCount = 0` while preparing secrets, DB bootstrap, migrations and seeding. Keep `AiPdfUploadsEnabled = false` during schema preparation.

## 2. Upload the template

1. In N. Virginia, select **CloudFormation → Create stack → With new resources (standard)**.
2. Upload `runtime.yaml` from the infrastructure repository.

**📸 Capture the selected filename.**

3. Click Next and name the stack `tindy-runtime`. Fill parameters from the new stack Outputs in the next guided steps.

**📸 Capture parameter sections before Review.**

Template upload and parameter entry are recorded below. New Runtime creation is complete; see the evidence below.

## 3. Initial Runtime parameters

AWS CLI verified current Outputs, the ECR image digest and CloudFront prefix list. The previous environment enabled OAuth, media, Bedrock text and AI PDF. Initial creation retains OAuth, media and Bedrock text, while AI PDF stays false until schema readiness. Leave the final four one-off task inputs empty for initial creation. After the zero-count service exists, prepare secrets and update the stack to enable bootstrap/migration/seed definitions.

The complete values are also stored in `backup/tindy-runtime-fresh/runtime-parameters.csv` and its JSON counterpart in Worklog.

| **Parameter** | **Session value** |
| :--- | :--- |
| `ApiImage` | `572454213668.dkr.ecr.us-east-1.amazonaws.com/tindy-api@sha256:1505b06199c2965fd800deab9956cbf47601ce76bc42848b8085dfae5b7f69ea` |
| `DesiredCount` | `0` |
| `VpcId` | `vpc-0209344341dce92fb` |
| `PublicTaskSubnetIds` | `subnet-01748b133fa7297ac,subnet-0a7df83a7449da864` |
| `PrivateAppSubnetIds` | `subnet-0faa17a03a1392748,subnet-050d30a2b0a6274a3` |
| `AlbSubnetCidrs` | `10.20.20.0/24,10.20.21.0/24` |
| `AlbSecurityGroupId` | `sg-08b240832188ed241` |
| `EcsSecurityGroupId` | `sg-0b9a2bfcc5ff842f5` |
| `CloudFrontOriginPrefixListId` | `pl-3b927c52` |
| `CloudFrontServiceSecurityGroupId` | Leave empty |
| `CloudFrontPublicHost` | `d1vcn4g2ifl3k8.cloudfront.net` |
| `DatabaseConnectionSecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/database/api-connection-UleOrh` |
| `JwtSigningKeySecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/auth/jwt-signing-key-b4L7JF` |
| `OAuthEnabled` | `true` |
| `GoogleOAuthSecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/auth/google-oauth-OHiwct` |
| `GitHubOAuthSecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/auth/github-oauth-QqFH0k` |
| `AiPdfUploadsEnabled` | `false` |
| `BedrockTextEnabled` | `true` |
| `MediaEnabled` | `true` |
| `MediaBucketName` | `tindy-foundation-mediabucket-5kz0y8tsm0qo` |
| `GuardDutyProtectionPlanArn` | `arn:aws:guardduty:us-east-1:572454213668:malware-protection-plan/9cd0f69716d5ca261abc` |
| `GuardDutyQueueUrl` | `https://sqs.us-east-1.amazonaws.com/572454213668/tindy-integrations-pdf-scan` |
| `DataProtectionParameterPath` | `/tindy/data-protection` |
| `DBProvisionMode` | `Fresh` |
| `MigrationConnectionSecretArn` | Leave empty |
| `SeedPasswordSecretArn` | Leave empty |
| `DBEndpointAddress` | Leave empty |
| `DBMasterSecretArn` | Leave empty |

These values have been checked against the parameter screenshots. New Runtime creation is verified below. **📸 Capture the stack name and parameter sections before Review.**

## 4. Verify entered parameters

Uploaded `runtime.yaml` and named the stack `tindy-runtime`.

[Runtime template](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-34-runtime-template.png)

[Runtime stack name](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35-runtime-stack-name.png)

The image digest, Edge hostname and prefix list, secret/GuardDuty references, feature flags and new networking IDs match the prepared values. `DesiredCount = 0`, `DBProvisionMode = Fresh` and `AiPdfUploadsEnabled = false`. The four one-off task inputs remain empty for initial creation.

[Image and Edge parameters](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35a-runtime-parameters-image-edge.png)

[Database and task count parameters](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35b-runtime-parameters-database.png)

[Integration and secret references](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35c-runtime-parameters-integrations.png)

[Subnet and VPC parameters](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35d-runtime-parameters-subnets.png)

## 5. Create the stack and collect Outputs

Click Next, add `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam` tags. Keep Express mode disabled, rollback enabled and Use deletion policy. Acknowledge IAM capabilities, review and submit.

Wait for `tindy-runtime — CREATE_COMPLETE`, then record `AlbDnsName`, `VpcOriginId`, `ApiTargetGroupArn` and ECS references from Outputs. With DesiredCount zero, the service has no running API task at this stage.

**📸 Capture Options/IAM, CREATE_COMPLETE and all Outputs.**

Verified tags, disabled Express mode, Roll back all resources, Use deletion policy and IAM capability acknowledgement.

[Runtime tags](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36-runtime-tags.png)

[Runtime options](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36a-runtime-options.png)

[Runtime IAM acknowledgement](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36b-runtime-iam-capabilities.png)

Review shows the Submit button to start stack creation.

[Runtime Review before Submit](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36c-runtime-review-submit.png)

Runtime reached **CREATE_COMPLETE** with **Outputs (6)**.

[Runtime completed](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-37-runtime-complete.png)

[Six Runtime Outputs](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-38-runtime-outputs.png)

`AlbDnsName` and `VpcOriginId` configure Edge live mode. `ApiTargetGroupArn` supports health verification and alarm dimensions. `ApiTaskDefinitionArn`, `ApiServiceName` and `ClusterName` identify the API configuration and ECS resources.

Bootstrap/migration/seed definitions appear after supplying their parameters. Next, verify Desired/Running/Pending counts are zero before preparing Fresh secrets.
