---
title: "SWITCH EDGE TO LIVE"
date: 2026-11-19
weight: 6
chapter: false
pre: "<b> 5.5.6 </b>"
---

Runtime is UPDATE_COMPLETE with API Desired 1 / Running 1 / Pending 0 and ALB 1 Healthy / 0 Unhealthy. Edge remains in maintenance. This stage uses Runtime Outputs to configure the CloudFront API origin.

## 1. Collect Runtime Outputs

Open CloudFormation → tindy-runtime → Outputs and copy `AlbDnsName` and `VpcOriginId`. `AlbDnsName` identifies the internal ALB hostname. `VpcOriginId` identifies the CloudFront VPC Origin created by Runtime. Use the Console copy control, preserve letter case and avoid pasted whitespace. AlbDnsName must contain only the hostname, without a protocol or path.



[Runtime ALB DNS output](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-59a-runtime-alb-output.png)



[Runtime VPC Origin ID output](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-59b-runtime-vpc-origin-output.png)

## 2. Create the Edge live change set

Select **tindy-edge → Stack actions → Create a change set**. Choose **Standard change set → Use existing template → Next** and name it `tindy-edge-go-live`.

| **Parameter**        | **Value**                               | **Purpose**                                            |
| :------------------- | :-------------------------------------- | :----------------------------------------------------- |
| `EdgeMode`           | `live`                                  | Enable live API routing configuration in the template. |
| `AlbDnsName`         | Copy the matching tindy-runtime Output. | Internal ALB hostname for the API origin.              |
| `VpcOriginId`        | Copy the matching tindy-runtime Output. | Connect the origin to the current VPC Origin.          |
| `FrontendBucketName` | Keep the existing value.                | Continue using the existing frontend bucket.           |

Retain current options and create the change set. **📸 Capture filled Parameters and Resource changes at CREATE_COMPLETE / AVAILABLE.** Review changes before execution. This milestone confirms only that Outputs have been collected. Live Edge activation remains unverified.



After successful execution in the next stage, verify readiness through HTTPS on the CloudFront hostname. Alarm dimensions, AI PDF, OAuth and frontend deployment remain separate follow-up stages.

## 3. Recorded change-set preparation screens

**Standard change set** and **Use existing template** were selected for `tindy-edge`. The banner mentioning `tindy-runtime-start-api` refers to the earlier Runtime execution.



[Edge live uses existing template](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-60a-edge-live-existing-template.png)



The change set name is `tindy-edge-go-live`. The description retains the existing Edge template description.



[Edge live change set name](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-60b-edge-live-changeset-name.png)



The Parameters screenshot records `EdgeMode = live`, populated `AlbDnsName` and `VpcOriginId`, and the retained `FrontendBucketName` value `tindy-foundation-frontendbucket-dxzgxfyg75gu`. The ALB hostname has no protocol prefix. Copy both origin inputs verbatim from Runtime Outputs to avoid character transcription errors.



[Edge live parameters](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-60c-edge-live-parameters.png)



Continue with Next, retain current options, review, then create the change set. **📸 Capture Resource changes at CREATE_COMPLETE and execution status AVAILABLE.** Change-set completion and live Edge activation remain unverified at this milestone.

## 4. Review the live change set

Change set `tindy-edge-go-live` reached **CREATE_COMPLETE**, Execution status **AVAILABLE**. It uses a Standard change set, Express mode Disabled and Roll back all stack resources.

| **Logical ID**     | **Action** | **Resource type**               | **Replacement** |
| :----------------- | :--------- | :------------------------------ | :-------------- |
| `EdgeDistribution` | Modify     | `AWS::CloudFront::Distribution` | False           |

Exactly one resource changes in place, matching the origin and routing configuration update for live mode. Execution and readiness through CloudFront remain unverified at this milestone.



[Edge live change set review](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-61-edge-live-changeset.png)

## 5. Execute and verify readiness

Select **Execute change set**, retain the configured rollback behavior and **Use deletion policy** if prompted, then confirm execution. Follow Events to **UPDATE_COMPLETE**. Check Outputs for `EdgeMode = live`.



**📸 Capture stack UPDATE_COMPLETE and EdgeMode live in Outputs.** Once the update completes, run in PowerShell:

```powershell
curl.exe -i https://d1vcn4g2ifl3k8.cloudfront.net/api/health/ready
```

Expect HTTP 200. **📸 Capture the full HTTP status and response body.** If an error is returned, preserve the response and inspect origin configuration and logs before declaring live success. Frontend and OAuth verification follow separately.

## 6. Verified live Edge and readiness

Stack `tindy-edge` reached **UPDATE_COMPLETE**. Outputs confirm `EdgeMode = live`, distribution ID `E39MIF3N8DR3D6`, and hostname `d1vcn4g2ifl3k8.cloudfront.net`.



[Edge live update complete](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-62-edge-live-update-complete.png)



The HTTPS request to `/api/health/ready` returned **HTTP/1.1 200 OK** with body:

```json
{"status":"Healthy","service":"Tindy API"}
```

The response records Server Kestrel, Via CloudFront, X-Cache Miss from cloudfront, and Cache-Control no-store, no-cache. This establishes API readiness through the CloudFront hostname after live activation. Login, media, AI PDF and frontend flows remain unverified by this check.



[CloudFront API readiness HTTP 200](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-63-cloudfront-readiness-200.png)



Next, inspect the frontend on the same hostname and reconcile OAuth callbacks and frontend release configuration with the new URL. Integrations alarm dimensions and AI PDF remain separate follow-up stages.

## 7. Frontend login page rendered

The browser screenshot records the Tindy login page with its logo, introduction, Email or Username/Password fields, Sign in button, and Google/GitHub buttons. This establishes login page rendering. Password login and OAuth success remain unverified.



[Tindy frontend login page](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-64-frontend-login-page.png)

### Verify demo account login

The backend dataset configuration provides `demo20` or `demo20@tindy.test`. Use the password supplied to seed through secret `tindy/database/seed-password`. View it in Secrets Manager → select the secret → Retrieve secret value and enter it directly into the application. Keep the password out of workshop screenshots and documentation files.



Select Sign in, verify the resulting page and whether project data loads. **📸 Capture the signed-in page**, or any error message. Actual project counts and application interactions will be verified as performed. The login-page screenshot does not establish them. Google/GitHub OAuth requires separate verification using callbacks for the new hostname.

## 8. Verified login and Discover Projects rendering

Following demo account login, the frontend displays Discover Projects and the `Nguyễn Minh An (Demo)` profile. The screenshot shows `Student Marketplace [Demo]`, a Backend Developer position, two open slots, ASP.NET Core/C# skills, a **93%** Rule-based match and a scoring factor explanation.



Your overview shows **Loaded 20**. This records the number loaded in the interface at capture time and does not establish the database project total. The screenshot confirms a signed-in page and displayed Discovery data. Apply/Save actions, session persistence after reload, Find Teammates and OAuth remain unverified.



[Demo login and Discover Projects](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-65-demo-login-discover-projects.png)



Next, reload to check session persistence, open **Full specs** on the project card to inspect project details, then switch to **Find Teammates** to inspect member data. **📸 Capture project details and Find Teammates after data loads.**

## 9. Project details and Find Teammates data rendered

Project Details loads `Student Marketplace [Demo]`, recruiting status, leader `Trần Thu Hà (Demo)`, 1/7 members, description, goals and technology stack. Apply to Position, Save and Message leader controls are visible. The screenshot establishes details rendering. These write actions have not been verified.



[Project details loaded](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-66-project-details.png)



Find Teammates displays `Campus Room Booking [Demo]`, a Backend Developer position with two open slots, candidate `Phan Nhật Minh (Demo)` and **55% Position Match**. The match inspector shows required skills 0%, preferred role 100%, availability 100% and optional skills 50%. Your overview records Loaded 19 at capture time.



[Find Teammates and position match](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-67-find-teammates.png)



Both pages rendered data in the signed-in interface. These screenshots do not independently establish an F5 reload or retrieval of all 50 projects. Apply, Save, Shortlist, Invite, OAuth and media/AI require separate checks within the demonstration scope.
