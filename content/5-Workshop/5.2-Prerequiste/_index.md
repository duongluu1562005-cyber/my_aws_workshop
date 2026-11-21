---
title: "DEPLOYMENT PREREQUISITES"
date: 2026-11-19
weight: 2
chapter: false
pre: " <b> 5.2 </b> "
---

Prepare AWS access, infrastructure source, the backend image and resource references for Tindy deployment in N. Virginia (`us-east-1`), matching template constraints.

## 1. Tools and access

- AWS Console for template uploads, change set review, Events/Outputs and screenshots.
- An authenticated AWS CLI profile. This session uses `tindy-admin`.
- PowerShell and `curl.exe` for CLI and HTTP checks.
- Permissions for resources defined in the templates, including CloudFormation, IAM, EC2 networking, S3, ECR, Secrets Manager, RDS, ECS, ELB, CloudFront, GuardDuty, EventBridge, SQS, SNS and CloudWatch. Acknowledge IAM capabilities when requested.
- Access to the notification mailbox to confirm SNS. OAuth verification requires access to Google/GitHub application callback settings for the new hostname.

Verify the CLI session on the deployment machine:

```powershell
aws sts get-caller-identity --profile tindy-admin --region us-east-1 --no-cli-pager
```

📸 **Review account identifiers in identity screenshots before publication.**

## 2. Source and release image

Use the six YAML templates under `tindy-infrastructure/cloudformation`, described in section 5.1. Prepare the backend image in ECR repository `tindy-api` and pin its digest for consistent API and database task execution.

Runtime `ApiImage` uses `<account-id>.dkr.ecr.us-east-1.amazonaws.com/tindy-api@sha256:<digest>`. This session checked the digest against ECR and the pre-cleanup Runtime parameter. Frontend deployment uses the frontend repository release workflow.

## 3. Redeployment mappings

The practical session begins with an existing environment. Before Foundation deletion, logical-to-physical mappings, parameters, Outputs and the deployed template were exported to `backup/tindy-foundation-before-cleanup` in Worklog.

Nine retained resources cover two S3 buckets, the media bucket policy, ECR, four application secrets and GitHub OIDC. Use the prepared `foundation-import.yaml` to restore stack ownership, then update with `foundation.yaml` as documented in section 5.3.1.

## 4. Session choices

| **Setting** | **Choice** |
| :--- | :--- |
| Database | Fresh, empty snapshot identifier, bootstrap/migrations/seed. |
| Region | `us-east-1`. |
| CloudFront | New distribution and updated hostname references. |
| Initial Runtime | `DesiredCount = 0` until database and secrets are ready. |
| Traffic | Edge maintenance until the API and target group are healthy. |
| Tags | `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. |

Fill parameters from new stack Outputs. Update the retained application connection secret for the Fresh database. Manage passwords, JWT keys and OAuth client secrets in AWS.

## 5. Screenshots and video

Screenshots are saved under `static/images/5-Workshop/cleanup` and `static/images/5-Workshop/deploy`. Guided steps identify parameter, change set, completion and Output capture points.

Record the video after seeding and application verification. The planned flow covers login, project discovery, applying and leader review. Each deployment page distinguishes verified outcomes from pending steps.
