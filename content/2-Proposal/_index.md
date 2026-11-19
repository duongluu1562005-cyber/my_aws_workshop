---
title: "Proposal"
date: 2026-19-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
# Proposal

# Tindy — An AWS Platform for Finding Projects and Teammates

## 1. Executive Summary

Tindy is a web-based platform designed to connect students and professionals at the beginning of their careers with projects suited to their abilities and available time. Project leaders can advertise openings for particular roles, monitor applications, and invite potential collaborators. When a candidate is approved, Tindy records the new project membership and adjusts the number of occupied seats for that position, keeping the information consistent for both sides.

The demonstration environment is planned for **US East (N. Virginia), `us-east-1`**. The React frontend is delivered by Amazon CloudFront using content stored in Amazon S3, while requests requiring server-side processing are forwarded to an ASP.NET Core API deployed with Amazon ECS on AWS Fargate. Amazon RDS for PostgreSQL holds the application's business records. A separate private S3 bucket houses documents; GuardDuty is used to scan PDF files, and Amazon Bedrock supports the preparation of suggested profile content.

## 2. Problem and Scope

### Problem

Information about available projects and recruitment is commonly fragmented across group chats, social media posts, and online forms. This makes it difficult for applicants to determine which roles remain available, the skills expected for each position, and the estimated weekly time commitment. In turn, project leaders often repeat the same questions and manage applications, invitations, and vacant seats in multiple places. Tindy centralizes this information at the project and position levels.

### Demo Scope

| **Workflow** | **User action** | **Result in Tindy** |
| :-- | :-- | :-- |
| Build a profile | During onboarding, a member provides their skills, intended role, experience, and available hours. Where appropriate, submitted text or a CV in PDF format may be used to prepare suggestions. | The profile changes only after the member examines the draft and saves the information. |
| Apply to a position | A project lead advertises an available role. A member browses the project, chooses a position, confirms an application, and waits for the lead to review it. | The application starts with **Pending** status. Acceptance adds the applicant to the project and raises the filled-seat total for the position. |
| Invite a teammate | A project lead searches for a candidate who has chosen to be discoverable and sends an invitation tied to a project and role. The recipient checks the notification and replies. | The invitation remains **Pending** until a decision is made. If the recipient accepts and the API confirms eligibility, the system adds the membership and revises the occupied-seat count. |
| Communicate | Users send private messages or take part in project conversations to which they have access. | New messages are displayed in the appropriate conversation only to authorized participants. |
| Operate the demo | The operator publishes updated frontend and API versions, checks service availability, creates backups, and restores the environment when required. | Release information, test evidence, and backup identifiers provide a basis for verifying and recovering the deployment. |

### Interpreting Match Scores

For each available position, the API calculates a match score based on four criteria: **required skills (40%), preferred role (30%), availability (20%), and optional skills (10%)**. When the position does not specify a particular skill category, or availability cannot be evaluated, the applicable weights are redistributed proportionally. When the candidate has not entered a preferred role, that criterion receives zero points.

As an illustration, consider an applicant who satisfies every required and optional skill, matches the preferred role, but can provide only 5 hours of the 10 hours requested each week. The resulting match score is **90/100**. This score is explanatory rather than an automatic hiring decision. The users choose how to proceed, and the API verifies authorization, project status, and available capacity when an acceptance is submitted.

### Benefits and How to Evaluate Them

For **members**, Tindy makes open roles, weekly commitment expectations, and skill gaps visible during project discovery. A completed profile can also be used for several applications. For **project leads**, recruitment activity is grouped by project and position, making it easier to follow both applications and invitations. After a candidate joins, the related application state, membership record, and seat count can all be checked against the same underlying data.

The demonstration will evaluate these benefits through observable tasks. Recruitment tests will log the number of steps and time between selecting an opening and confirmation of the final outcome by both parties. Profile-completion tests will use identical sample data to compare entry by hand with an AI-assisted process that includes drafting, reviewing, and correcting suggestions. The analysis will also account for AWS expenditure, the time needed to prepare the demo environment, and the human effort required to verify suggested profile details. Findings from these measurements will inform the final assessment of the platform's usefulness.

## 3. Solution Architecture

![Tindy architecture diagram](/my_aws_workshop/images/proposal/3.jpg)
*Tindy architecture diagram for the proposal. The following numbered explanation corresponds to the repository settings and implemented application behavior.*

| **Step** | **Component**                                       | **Request or data flow**                                                                                                                                                                                                                                                                                     |
| :------- | :-------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1        | Amazon CloudFront and frontend S3 bucket            | Visitors connect to the application using HTTPS. The private S3 bucket supplies the React/Vite frontend through CloudFront. Origin Access Control (OAC) allows the CloudFront distribution, rather than the public, to retrieve frontend objects.                                                                                                                                   |
| 2        | CloudFront VPC Origin and Application Load Balancer | Requests for dynamic routes, including `/api/*`, `/hubs/*`, and sign-in callbacks are forwarded to an internal ALB. The frontend and API use one hostname; these dynamic requests are not cached. [CloudFront VPC origins](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html) |
| 3        | Amazon ECS, AWS Fargate, and Amazon ECR             | An ASP.NET Core .NET 10 API image from ECR is deployed as an ECS container. The backend provides sign-in, profile features, project discovery, recruiting flows, and SignalR messaging. Task readiness is monitored through ALB health checks.                                                                                                                           |
| 4        | Media S3 bucket, GuardDuty, EventBridge, and SQS    | Uploaded media are kept in a nonpublic S3 bucket. PDF certificates and AI source documents pass through the malware-scanning process. Scan results from GuardDuty are passed by EventBridge to SQS, where a worker processes them and updates file status. Messages that cannot be handled are retained in a dead-letter queue (DLQ) for troubleshooting.                                                       |
| 5        | Amazon Bedrock                                      | Before extracting information from an AI input PDF, the server checks that the matching S3 object has received a clean scan result. Direct text input is also supported. After validating the response, the backend presents proposed profile values, skills, experience, certificates, and warnings for the user to inspect.                           |
| 6        | Amazon RDS for PostgreSQL                           | Structured application records in PostgreSQL 17 include accounts, profiles, projects, positions, applications, invitations, and messages. The RDS instance is located in private subnets, uses mandatory TLS, and limits connectivity by security group.                                                                                         |

### Three Flows Through the Diagram

**Application access and data updates (1 → 2 → 3 → 6).** The browser obtains static application assets from S3 via CloudFront. Opening a project, filing an application, or starting chat sends a dynamic request through CloudFront’s VPC origin to the internal ALB. The load balancer forwards the request to a healthy Fargate task. Before any PostgreSQL read or write, the API checks the JWT and the caller’s permissions for the relevant project or conversation. Frontend pages, API endpoints, and chat traffic are served under the same hostname.

**Certificate upload and PDF checks (3 → 4).** Uploaded PDFs enter through the API and are saved in the private media bucket. GuardDuty examines the file; EventBridge sends the finding to SQS; a worker associates that result with the correct S3 object or version and changes the document status. Operations that depend on a clean scan, including certain reads and extraction, remain unavailable for pending or ineligible files. When all configured retries fail, the message is placed in the DLQ.

**Profile extraction (4 → 5 → 3 → 6).** Extraction of PDF-based AI input is permitted only after a clean scan. Users may alternatively provide text without uploading a PDF. The server requests a Bedrock response, checks its structure, and produces suggested profile details, including skills, experience, and certificates. Only the values confirmed after user review and possible editing are persisted by the API in PostgreSQL.

**Networking and availability.** Network resources are arranged across two Availability Zones. The load balancer is not internet-facing. In the current deployment, Fargate tasks reside in public subnets and are assigned public IPv4 addresses. Security group rules allow API ingress only through the ALB. The demonstration runbook specifies **one Fargate task** and the database template provisions **Single-AZ RDS**. The diagram’s two zones describe where subnets are placed, not a Multi-AZ database deployment. Any future increase in task count must first validate cross-task SignalR operation and background event handling.

**Security and operations.** Static web content and user media are stored in distinct S3 buckets. JWT authentication and authorization checks on individual resources are performed by the backend. Database credentials and other sensitive settings are kept in Secrets Manager. The deployment pipeline uses GitHub Actions with OIDC role assumption. CloudWatch provides application logs and signals for target health, 5xx errors, and DLQ activity. Where alerting is enabled, SNS delivers operational notifications.

### Components and Design Rationale

| **Layer**        | **Responsibility in the demo**                                                                                                              | **Why this design fits**                                                                                                                                               |
| :--------------- | :------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Web              | React and Vite produce static assets. CloudFront reads from S3 through Origin Access Control and serves application routes.                 | Frontend releases do not require rebuilding the backend. Static content and dynamic session requests are configured with different caching rules.                                                       |
| API              | ASP.NET Core runs in ECS Fargate. ECR stores release images. The ALB checks readiness and routes traffic to healthy tasks.                  | An image digest identifies the running release; the rollout process also allows validation of a replacement revision.                                                                 |
| Business data    | RDS for PostgreSQL stores profiles, project relationships, recruitment states, and chat history.                                            | Writes can enforce relationships among projects, job openings, and participants. Database migrations coordinate schema revisions with software deployment. |
| Files and events | The private media bucket stores files. GuardDuty scans PDFs, EventBridge delivers scan events, and the SQS consumer updates document state. | File submission and the completion of scanning are asynchronous. SQS makes outstanding processing visible, while the DLQ stores failures for review.                                 |
| AI               | When enabled, the backend calls Bedrock and returns a structured draft.                                                                     | Suggested content can be checked against the original text or document. The server checks the proposed fields and their mapping to recognized skills and roles ahead of user review.                                    |

### Data Boundaries, Access, and Observability

**Data placement.** Identity data, user profiles, project associations, and process states are recorded in PostgreSQL. HTML files and compiled frontend assets reside in the website bucket. Uploaded document contents are held in the media bucket. To validate a recovery, the manifest must identify both an RDS snapshot and the related S3 objects; this allows the team to confirm that restored database records still reference available documents.

**Access by layer.** Connectivity between the ALB, backend containers, and database is controlled through security groups. For each request, the backend checks identity and access rights for the target project, profile, or chat. The ECS execution role is used during task startup to obtain container images, initialize logs, and retrieve configured secrets. AWS requests made by the running application—media operations, scan-event handling, and optional AI inference—are granted through the ECS task role. GitHub Actions deployment permissions granted through OIDC are separate from application runtime privileges.

**Operational signals.** ALB target status and HTTP 5xx rates can indicate whether a fault involves routing or application processing. ECS events together with CloudWatch Logs provide evidence of startup, shutdown, and application errors. PDF investigations should compare Tindy’s document status against its GuardDuty finding and any messages outstanding in SQS or its DLQ. In-app recruitment alerts are retrieved by polling the API. SignalR powers real-time messaging. Configured operational alert delivery uses SNS.

### Demo Trade-offs and What Would Justify Scaling

| **Decision**                                                     | **What the demo prioritizes**                                                                      | **Evidence needed for a change**                                                                                                                                    |
| :--------------------------------------------------------------- | :------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| One Fargate task behind the ALB                                  | Limit continuously active resources and reduce complexity in demonstration chat.                  | Collect request-volume and response-time measurements. Verify SignalR event delivery across tasks and check that background event processing remains consistent before increasing task count. |
| Single-AZ RDS                                                    | Keep the starting configuration simple and preserve a documented snapshot recovery path.           | Establish limits for downtime and recoverable data loss. Estimate the cost of Multi-AZ and rehearse recovery before committing to higher availability.                             |
| API tasks in public subnets with ingress through an internal ALB | Retain the present outbound connectivity route for integrations. Security groups restrict access to the API.       | Before moving to private subnets, inventory AWS and external destinations and compare endpoints and outbound paths for connectivity, cost, and operating effort.    |
| On-demand AI extraction                                          | Perform extraction on demand and record latency and per-request expense. | Measure invalid outputs, latency, and token use before considering a background processing queue.                                                                   |

## 4. Implementation and Testing

### Prepare the Infrastructure and Data

The infrastructure is organized by AWS CloudFormation into six stacks: **foundation, network, database, integrations, runtime, and edge**. S3 buckets, ECR, IAM resources, and network components come from the foundation and network stacks. The database stack establishes PostgreSQL. The integrations stack manages PDF-scan findings. The API, load balancer, and VPC origin belong to the runtime stack. After runtime readiness is confirmed, the edge stack forwards dynamic traffic using the VPC origin.

The **Fresh** option provisions a new database, applies migrations, and populates sample data; **Restore** reinstates the schema and records from a snapshot. When a release alters the database schema, capture a snapshot beforehand and execute the EF Core migration as a standalone ECS task. Confirm compatibility between the active API and updated schema to retain the ability to roll back the application revision after a failed rollout.

### Release the Application

1. Once continuous integration succeeds, choose the commit approved for release. Build the backend image, push it to ECR, and document the image digest used in the ECS task definition.
2. Check ECS service stability and confirm that the ALB reports healthy targets. With the edge in `live` mode, check that `/api/health/ready` returns HTTP `200` with status `Healthy` through CloudFront.
3. Compile the frontend using Vite, upload it to S3, and trigger CloudFront invalidation. Inspect the home page, login page, and JavaScript bundle through the same hostname.
4. Execute acceptance tests using demonstration accounts and sample PDF files. Document the commit, image digest, ECS revision, test results, and snapshot ID where applicable. During environment rebuilding, retain maintenance mode on the edge until backend services, PostgreSQL, and essential integrations pass readiness checks.

### Technical and Non-Functional Requirements

- **Release environment.** The application uses React/Vite, ASP.NET Core .NET 10, EF Core, and PostgreSQL. Build the frontend to use same-origin API paths. Supply environment settings and sensitive configuration through the deployment workflow and AWS Secrets Manager.
- **Consistency.** Adding a member must depend on the latest project status and seat availability. Check both membership and the position’s filled-seat count after acceptance and reload.
- **Privacy.** Demonstrations should rely on fictional accounts and non-sensitive sample files. Verify permissions when opening a document, requesting an AI draft, or accessing a conversation. Keep references and status evidence in test records. Keep secret values and résumé contents out of those records.
- **Testability.** Include test cases that are expected to succeed as well as cases that should be denied. Include a position with no open seats, a candidate who has not opted in, a PDF with a pending scan, and a user who lacks conversation access.
- **Recoverability.** Capture the database snapshot and software version before modifying schemas. Identify a sample set of application records and media files to reconcile after Restore.

### Lifecycle Between Demonstrations

At the end of each demo, confirm whether data changes or PDF-scanning jobs are still running. Once the environment is ready for backup, document the snapshot ID, timestamp, encryption status, API release, and current migration. Confirm that the S3 objects and ECR image needed by that recovery point are retained.

When shutting down the demonstration environment, enable CloudFront maintenance mode while preserving the distribution hostname. The project runbook defines checks before removing runtime, database, and integrations in dependency order. Preserve the agreed recovery resources, including the snapshot, S3 data, ECR image, and configuration references. At the next demo, select Restore or Fresh based on the goal, verify the database endpoint and health checks, and reopen the edge when the required services are ready.

## 5. Sprint Plan and Deployment Milestones

Development work on Tindy starts on **Monday, October 5, 2026 (Week 4)**. Weeks 4–9 are allocated to three development iterations lasting two weeks each. **Week 10 (November 16–22, 2026)** covers AWS deployment, validation after launch, and final demonstration work.

| **Sprint**                                          | **Dates**                                   | **Main work**                                                                                                                                                                                         | **End-of-sprint deliverable**                                                                                                               |
| :-------------------------------------------------- | :------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| **Sprint 1 · Application foundation**               | **Weeks 4–5** · October 5–18, 2026          | Begin implementation. Agree on user flows and the data model. Set up React/Vite, ASP.NET Core, and PostgreSQL. Build sign-in, onboarding, profiles, and the skill and role catalogs.                     | A sample user can sign in, complete onboarding, and save a profile through the API.                                                         |
| **Sprint 2 · Projects and recruiting**              | **Weeks 6–7** · October 19–November 1, 2026 | Implement project and position administration, project discovery, match scoring, applications, teammate discovery, and invitations. Recheck recruitment eligibility and capacity at acceptance.               | Sample data demonstrates the application and invitation flows. Successful acceptance updates project membership and filled seats correctly. |
| **Sprint 3 · Integrations and release preparation** | **Weeks 8–9** · November 2–15, 2026         | Finish SignalR messaging and in-application notifications. Integrate file storage, PDF scanning, and Bedrock profile drafts. Prepare CloudFormation templates, CI/CD configuration, and demo data.               | A release candidate, test scenarios for core workflows, and reviewed AWS configuration.                                                     |
| **Sprint 4 · Deployment and completion**            | **Week 10** · November 16–22, 2026          | Publish the frontend and API on AWS. Check CloudFront, the ALB, ECS, and RDS. Perform acceptance scenarios through the demo hostname, resolve issues affecting core workflows, and record release details. | A deployed AWS demo with API readiness returning HTTP `200` and status `Healthy`, acceptance results, and redeployment instructions.        |

The target for project completion falls in **Week 10**. From Week 11 through the **December 14, 2026** internship wrap-up, reporting work covers the architecture diagram, demo scenarios, acceptance results, release details, and cost assumptions.

## 6. Budget Estimate

The continuous-operation cost model assumes **730 hours per month** in `us-east-1`. The prices are drawn from the `Tindy-Always-on` workload estimate exported from AWS Pricing Calculator on **November 18, 2026**, and reflect undiscounted prices without taxes or credits.

| **Cost item**                 | **Basis**                                                                                                                                                 | **Reference cost** |
| :---------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------- | -----------------: |
| AWS Fargate                   | A Linux/x86 container task provisioned with 0.5 vCPU and 2 GiB of memory. The calculation accounts for 365 vCPU-hours and 1,460 GB-hours of RAM.                                        |    USD 21.26/month |
| Application Load Balancer     | A single internal ALB operating for 730 hours.                                                                                                                           |    USD 16.43/month |
| Public IPv4                   | One assigned public IPv4 address used by the task for 730 hours.                                                                                                |     USD 3.65/month |
| RDS for PostgreSQL            | A single Single-AZ `db.t4g.micro` for 730 hours (USD 11.68) and 20 GiB gp3 (USD 2.30).                                                                         |    USD 13.98/month |
| Edge, media, security, and AI | CloudFront, S3, ECR, Secrets Manager, GuardDuty, EventBridge, SQS, CloudWatch, and Bedrock. Actual charges depend on stored bytes, requests, logging, file scans, and token consumption. |        Usage-based |

**The always-on baseline is USD 55.32 per month.** This baseline does not include ALB LCUs, any RDS CPU credit charges and additional backup or snapshot storage, and the usage-based services listed above. Include retained snapshots and other retained resources when estimating the scheduled demo scenario. [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=75ccee07f5778dcee28c50a2bdf393694e87a6de)

The publicly shared estimate indicates **USD 55.34 per month**. Relative to the exported AWS Console calculation, the displayed Fargate and ALB costs are each USD 0.01 higher. The public ALB calculation uses 0.001 GB/hour of assumed traffic because the form requires a positive LCU input.

**For the pilot workload, allow approximately USD 58 per month** for always-on infrastructure and the modeled supporting services. The Cost Explorer export for November 1–17, 2026 shows core infrastructure activity concentrated on November 11–17. Treat that week as representative of a 30-day month. Add USD 1.60 for four stored secrets and scale the other imported usage priced in AWS Pricing Calculator (USD 0.1804 × 30/7, including Secrets Manager API calls). This planning figure excludes ALB LCUs, unverified EventBridge charges, RDS CPU credits, additional backup or snapshot storage, and taxes.

### Comparing Operating Scenarios

| **Cost input**                     | **Always-on scenario**                                                                                   | **Scheduled demo scenario**                                                                                              |
| :--------------------------------- | :------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| Fargate and public IPv4            | Model runtime against the 730-hour reference month.                                                      | Count actual task hours, including setup and verification before each demo.                                              |
| ALB and RDS                        | Count the hours the ALB and database are provisioned or running and add configuration-dependent charges. | Record ALB lifetime, database runtime, and snapshot storage kept between demos separately.                               |
| Retained storage and configuration | Measure S3 and ECR storage, backups, and retained configuration items.                                   | Include resources retained for recovery while the edge is in maintenance.                                                |
| Traffic, scanning, and AI          | Work with a defined baseline for requests, downloads, scans, and tokens.                                       | Measure activity per session and record repeat processing. Hold usage assumptions constant when comparing runtime costs. |

Following the trial, reconcile each service charge against the period for which its resources were allocated. Classify differences as runtime, storage, traffic, or document processing. AWS Budgets may notify the team at the chosen cost limit, and Cost Explorer can help locate the services responsible for unexpected spending.

## 7. Risk Assessment

| **Risk**                                               | **Impact**                                                       | **Mitigation**                                                                                                                  |
| :----------------------------------------------------- | :--------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------ |
| API or database interruption in the demo configuration | Application features become unavailable until service is restored.          | Observe readiness checks, ECS logs, and ALB target status. Define snapshot recovery procedures and acceptable downtime.                       |
| PDF rejection, failed scan, or slow processing         | The uploaded certificate or résumé remains unavailable for its intended purpose.                          | Display scanning progress, block reads and extraction until eligibility is met, monitor the DLQ, and verify the corresponding S3 object. |
| Incomplete or incorrect AI suggestions                 | Unverified recommendations may introduce errors into saved profiles.       | Check the response schema and skill catalog mappings, then require user review and allow corrections before saving.         |
| Migration or release mismatch                          | Version differences may cause incompatibility among frontend, backend, and database schema.  | Take a snapshot before changes, release in a compatible order, and document the commit, digest, and revision for recovery.        |
| Resources continue accruing charges after the demo     | Compute, ALB, database, logging, and stored snapshots can continue to incur fees. | Monitor spending limits, identify retained resources, and review billing after cleanup.                                                |

### Incident Triage and Recovery

1. **Identify the affected path.** Begin with API readiness, ALB target status, and ECS events. If the web and API are reachable and only PDF processing fails, inspect document state, the scan result, and the queue independently.
2. **Preserve evidence and recovery options.** Capture the incident timestamp, running revision, image digest, and latest change. For a suspected rollout failure, verify schema compatibility before attempting to return to an earlier API revision.
3. **Confirm recovery with sample data.** During RDS recovery, choose the correct snapshot and validate TLS access, permissions, sample records, and related media objects on S3. Compare the snapshot time with the last valid update to identify changes that need reconciliation.
4. **Reopen when checks pass.** Restore CloudFront to `live` only after health checks and the essential user journeys succeed. Document the cause, how the issue was detected, and any runbook step to update.

## 8. Expected Outcomes and Acceptance Criteria

| **Acceptance scenario**         | **How to test**                                                                                                                                       | **Expected evidence**                                                                                                                                                                        |
| :------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Member submits an application   | Work with a recruiting position with an open seat. Submit an application as a member and accept it as the project lead.                                     | The application moves from **Pending** to **Accepted**. The member appears in the project, the position’s filled-seat count increases once, and the data remains consistent after reloading. |
| Project lead invites a teammate | Prepare an independent test dataset. The prospective member opts in, the lead sends an invitation, and the candidate opens the notification and accepts.                    | The invitation identifies the correct project and position. The seat count stays the same while the invitation is pending and updates after successful acceptance.                           |
| Chat through CloudFront         | Open two signed-in sessions with access to the same conversation and send a message from one session.                                                 | The other session receives it in the correct conversation. DevTools records the WebSocket upgrade with status `101`.                                                                         |
| PDF scanning                    | Upload a sample certificate or AI input PDF. Track the scan state from pending to verdict.                                                            | Actions requiring a clean verdict remain blocked while the scan is pending. A clean verdict for the matching S3 object or version makes the document eligible for its intended use.          |
| AI profile draft                | Submit text or a scanned and eligible PDF, review the suggested fields, make edits, and confirm save.                                                 | Suggestions are structured and warnings identify missing or conflicting data. The saved profile reflects the fields the user confirmed.                                                      |
| Release and recovery            | Open the site through CloudFront, check API readiness and core workflows, then rebuild a test environment from a selected snapshot using the runbook. | Web and API share a hostname, readiness returns `200` and `Healthy`, and sample data matches the recovery point. Release details, snapshot ID, and reconciliation results are recorded.      |

### Handover Evidence

Each acceptance test should document the environment, version, executed action, intended outcome, and observed behavior. Use sample accounts for recruitment and document the before-and-after states. For releases, capture the commit, image digest, readiness result, and snapshot reference. For PDF and AI checks, use sample documents and retain the scan state and an example reviewed draft. These records support independent verification without disclosing real account credentials or private information.

**Technical basis.** See the templates in `tindy-infrastructure/cloudformation`, the Tindy release and redeployment runbooks, `DISCOVERY_PROJECT_FLOW.md`, `TEAMMATE_FLOW.md`, `AI_PROFILE_EXTRACTION.md`, and the backend chat documentation.
