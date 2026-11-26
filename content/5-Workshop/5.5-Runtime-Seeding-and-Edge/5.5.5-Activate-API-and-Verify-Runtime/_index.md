---
title: "ACTIVATE API AND VERIFY RUNTIME"
date: 2026-11-19
weight: 5
chapter: false
pre: "<b> 5.5.5 </b>"
---
Bootstrap, migrations and seed completed with exit code 0 and completion logs. This stage configures the ALB traffic source using the CloudFront service security group and starts one API task. Edge remains in maintenance until Runtime health has been verified.

## 1. Identify the CloudFront service security group

In EC2 → Security Groups, the screenshot confirms `CloudFront-VPCOrigins-Service-SG` has ID **`sg-0ae54c77731609b33`** and belongs to VPC **`vpc-0209344341dce92fb`**. This is the current ID in the rebuilt environment.

![CloudFront service security group](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-53-cloudfront-service-security-group.png)

The screenshot shows zero inbound permission entries and one outbound permission entry. Runtime uses this group as the source for the ALB ingress rule. This step requires no direct edits to the CloudFront-managed group.

## 2. Create the API activation change set

Open **CloudFormation → tindy-runtime → Stack actions → Create a change set**. Select **Standard change set**, **Use existing template**, then Next. Name it `tindy-runtime-start-api`.

| **Parameter**                      | **Value**              | **Purpose**                                                   |
| :--------------------------------- | :--------------------- | :------------------------------------------------------------ |
| `CloudFrontServiceSecurityGroupId` | `sg-0ae54c77731609b33` | Restrict ALB traffic to the CloudFront VPC Origin service SG. |
| `DesiredCount`                     | `1`                    | Start one API task after database preparation.                |

Keep all other parameters, including `AiPdfUploadsEnabled = false` and the current CloudFront origin prefix list. Retain current options, acknowledge IAM capability if requested, then create the change set.

**📸 Capture both changed parameters and Resource changes once the change set reaches CREATE_COMPLETE / AVAILABLE.** With only these two inputs changed, expect replacement of `AlbIngressFromCloudFront` and an in-place modification of `ApiService`. Review the actual changes before execution. Change-set creation and API execution remain unverified at this milestone.

## 3. Recorded change-set creation and review

Runtime is UPDATE_COMPLETE before API activation. This records the previous update and does not yet confirm API launch.

![Runtime before API activation](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54a-runtime-before-activation.png)

Standard change set and Use existing template were selected to retain the template and update parameters.

![Use existing template](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54b-start-api-existing-template.png)

Both changed inputs match the guide: `CloudFrontServiceSecurityGroupId = sg-0ae54c77731609b33` and `DesiredCount = 1`. The group supplies the ALB ingress source, while DesiredCount requests one API task maintained by ECS.

![CloudFront service SG parameter](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54c-start-api-cloudfront-sg.png)

![DesiredCount one](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54d-start-api-desired-one.png)

IAM capability was acknowledged in the wizard. The actual review below lists two changed resources.

![IAM capability acknowledgement](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54e-start-api-iam.png)

Change set `tindy-runtime-start-api` reached **CREATE_COMPLETE**, Execution status **AVAILABLE**, with Express mode Disabled and Roll back all stack resources configured.

| **Logical ID**             | **Action** | **Replacement** | **Meaning**                                                                                                        |
| :------------------------- | :--------- | :-------------- | :----------------------------------------------------------------------------------------------------------------- |
| `AlbIngressFromCloudFront` | Modify     | True            | Replace the ingress rule resource to use the CloudFront service SG. ReplaceAndDelete applies to the previous rule. |
| `ApiService`               | Modify     | False           | Update the ECS service in place with the new task count.                                                           |

![API activation change set review](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-55-start-api-changeset.png)

The changes match the two updated parameters. Execution and API health remain unverified at this milestone.

## 4. Execute and verify

Select **Execute change set**, retain the configured rollback behavior and select **Use deletion policy** if prompted, then confirm execution. Follow Events until the stack reaches **UPDATE_COMPLETE**. If the update fails, inspect stack Events and ECS service events before continuing.

**📸 Capture Runtime UPDATE_COMPLETE after this update.** Open **ECS → Clusters → tindy-runtime → Services → tindy-api** and verify Desired 1, Running 1, Pending 0 and completed deployment. **📸 Capture the service state**. Then review target group health and API logs. Keep Edge in maintenance during verification.

## 5. Verified API activation results

The Execute dialog retained Roll back all stack resources and Use deletion policy. The change set was executed.

![Execute API activation](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56a-start-api-execute.png)

Events confirm Runtime **UPDATE_COMPLETE** at 14:19:50 UTC+07 on November 18, 2026. The DELETE_COMPLETE event for `AlbIngressFromCloudFront` during rule replacement matches the reviewed Replacement True change.

![Runtime API update complete](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56b-runtime-api-update-complete.png)

The cluster reports **1 running / 0 pending**. Service `tindy-api` is Active with **1/1 Tasks running** and Last deployment Completed.

![Cluster API running](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56c-cluster-api-running.png)

![API service deployment completed](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56d-api-service-completed.png)

Service overview confirms Desired **1**, Running **1**, Pending **0**, task definition `tindy-runtime-api:13`, and Deployment status **Success**. Load balancer target health for container `api:8080` reports **1 Healthy / 0 Unhealthy**. These establish Runtime readiness and passing ALB health checks. Live API requests through CloudFront remain unverified.

![API service and ALB target healthy](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-57-api-target-healthy.png)

## Next step

Copy `AlbDnsName` and `VpcOriginId` directly from **CloudFormation → tindy-runtime → Outputs** to prepare the `tindy-edge` change set. Switching `EdgeMode` to `live` follows in the next stage. Edge remains in maintenance. Integrations alarm dimensions and AI PDF activation remain pending separate updates.

### Edge before live activation

The `tindy-edge` Outputs screenshot confirms CREATE_COMPLETE, distribution `E39MIF3N8DR3D6`, hostname `d1vcn4g2ifl3k8.cloudfront.net`, and `EdgeMode = maintenance`. Healthy Runtime API targets do not establish live Edge routing. To prepare live inputs, select stack `tindy-runtime` and open Outputs to copy `AlbDnsName` and `VpcOriginId`.

![Edge remains in maintenance before live activation](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-58-edge-before-live.png)

## Enable AI PDF after schema and monitoring preparation

Migration recorded `20261112124706_Media_AllowAiPdfQuarantine`, the GuardDuty protection plan is Active, its queue is configured, and all four monitoring alarms are OK. Next, create a Standard change set on `tindy-runtime`, Use existing template, named `tindy-runtime-enable-ai-pdf`. Change only `AiPdfUploadsEnabled` from false to true. Retain DesiredCount 1 and all other parameters.

This parameter maps to the API task definition environment variable `GuardDuty__AiPdfUploadsEnabled`. The template is expected to produce a new task definition revision and update the ECS service to deploy it. **📸 Capture the parameter and Resource changes at CREATE_COMPLETE / AVAILABLE** before execution. AI PDF activation and successful upload/scan remain unverified at this milestone.

### AI PDF change-set review

The wizard selected Standard change set and Use existing template on `tindy-runtime`. Parameter `AiPdfUploadsEnabled` is set to **true**.

![AI PDF uses existing template](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-72a-ai-pdf-existing-template.png)

![AI PDF enabled parameter](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-72b-ai-pdf-enabled-parameter.png)

Change set `tindy-runtime-enable-ai-pdf` reached **CREATE_COMPLETE / AVAILABLE** with:

| **Logical ID**      | **Action** | **Replacement** | **Meaning**                                                                                                                                          |
| :------------------ | :--------- | :-------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ApiService`        | Modify     | False           | Update the existing service to deploy the new task definition.                                                                                       |
| `ApiTaskDefinition` | Modify     | True            | Register a new task definition revision with the AI PDF environment flag enabled. ReplaceAndDelete applies to the previous task definition resource. |

![AI PDF change set review](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-73-ai-pdf-changeset.png)

The scope matches the parameter change. Execution and successful PDF upload/scan remain unverified at this milestone.

Execute the change set, retaining rollback and Use deletion policy if prompted. Wait for Runtime **UPDATE_COMPLETE**, then verify ECS `tindy-api` Desired 1 / Running 1 / Pending 0, deployment Success and Healthy ALB target. A rolling update may temporarily run more than one task. **📸 Capture the stable stack and service states**, recording the actual new revision. Recheck CloudFront readiness for HTTP 200 Healthy before testing PDF upload.

### AI PDF revision deployment results

Runtime reached **UPDATE_COMPLETE** after the AI PDF change set. ECS service `tindy-api` is Active with Desired 1 / Running 1 / Pending 0, Deployment status **Success**, and task definition **`tindy-runtime-api:14`**.

![AI PDF Runtime update complete](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-74-ai-pdf-runtime-update-complete.png)

![AI PDF service revision 14](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-75-ai-pdf-service-revision14.png)

Target health is still loading in the screenshot, so direct Healthy evidence for the new revision remains pending. Recheck CloudFront readiness before testing PDF upload.

### Verify upload → scan → extract

After readiness returns HTTP 200 Healthy, open **AI Studio** in the frontend. Under **CV (optional)** choose a non-empty text-based demo PDF, up to **2 MiB and 10 pages**. File selection initiates upload and the UI displays scan progress. **📸 Capture Scanning and the scan result**. Once clean, select **Extract suggestions**, then **📸 capture Review suggestions** or the actual error. Successful upload, scan and extraction remain unverified at this milestone.

### Readiness and PDF scan/extraction verified

After deploying revision 14, HTTPS requests through CloudFront to `/api/health/ready` still return **HTTP 200 OK** with `{"status":"Healthy","service":"Tindy API"}`.

![Readiness after AI PDF activation](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-76-ai-pdf-readiness-200.png)

AI Studio shows `CV_Backend.pdf` with **Scan complete. Ready to extract.**. Profile text is empty (0/20,000 characters), while Review suggestions contains skills including ASP.NET Core, C#, Docker, Entity Framework Core, PostgreSQL and REST API. Experience and certificates and Extraction notes (3) remain collapsed.

![PDF scan complete and extracted suggestions](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-77-ai-pdf-scan-extract-success.png)

UI evidence confirms the PDF upload → completed scan → extracted suggestions flow. Individual fields in collapsed panels and saving suggestions to the profile remain unverified. Separate image and certificate PDF uploads have not yet been evidenced.

Next, select one suggested skill absent from the profile, click **Save selected suggestions**, verify the success notification, then open the profile and reload to confirm persistence. **📸 Capture the save notification and persisted profile skill.** This changes demo data and should be performed with the demo account within acceptance testing.

### Suggestion saved to the profile

Following instructions to select a skill, save and reload the profile, the operator confirmed completion. The profile screenshot shows **Docker** among Technical skills. This captures the resulting profile and does not include the save notification. Completion of the save and reload steps was confirmed by the operator.

![AI suggestion saved in profile](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-78-ai-suggestion-profile-saved.png)

The recorded PDF acceptance flow now covers upload → completed scan → extracted suggestions → saved skill → skill displayed in the profile. Image and certificate PDF uploads are separate media flows and are not established by these screenshots.
