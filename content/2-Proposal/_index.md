---
title: "Proposal"
date: 2026-19-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
# Tindy — An AWS Platform for Finding Projects and Teammates

## 1. Executive Summary

Tindy is a web application that helps students and early-career professionals find projects that fit their skills and availability. Project leads publish openings for specific positions, track applications, and send invitations. Once a candidate is accepted, Tindy updates project membership and the position’s filled seats so both users see the same state.

The proposed demo runs in **US East (N. Virginia), `us-east-1`**. CloudFront serves the React frontend from S3 and sends dynamic requests to the ASP.NET Core API on ECS Fargate. The API stores business data in RDS for PostgreSQL. The private media bucket stores documents, while GuardDuty scans PDFs and Amazon Bedrock drafts profile suggestions.

## 2. Problem and Scope

### Problem

Recruiting information is often spread across chat groups, posts, and forms. Applicants struggle to see which positions are open, which skills they require, and how many hours they expect each week. Leads must ask for the same information again and track applications, invitations, and open seats across channels. Tindy brings this information together for each project and position.

### Demo Scope

| Workflow | User action | Result in Tindy |
| :--- | :--- | :--- |
| Build a profile | A member completes onboarding and enters skills, preferred role, experience, and availability. Eligible text or CV PDF input can be used to generate a draft. | The profile is updated after its owner reviews and saves it. |
| Apply to a position | A lead publishes an open position. A member discovers the project, selects a position, and confirms the application. The lead reviews the submission. | The submitted application is Pending. Once accepted, the member appears in the project and the position’s filled-seat count increases. |
| Invite a teammate | A lead selects a project and position, finds a candidate who has opted in, and sends an invitation. The candidate opens the notification and responds. | The invitation is Pending until resolved. When the candidate accepts and the API verifies eligibility, the application creates a membership and updates the seat count. |
| Communicate | Members exchange direct messages or join a project conversation where they have permission. | Authorized users see new messages in the correct conversation. |
| Operate the demo | The operator releases the frontend and API, checks service health, backs up data, and rebuilds the environment if needed. | Release details, verification results, and backup references support recovery. |

### Interpreting Match Scores

The API scores each position using four base weights: required skills 40%, preferred role 30%, availability 20%, and optional skills 10%. If a position has no skills in a category or availability cannot be calculated, the API normalizes the remaining applicable weights. A missing preferred role contributes zero for that criterion.

For example, a candidate who matches all required and optional skills and the preferred role, and can contribute 5 of the 10 hours per week the project needs, scores 90/100. The score explains fit. Users make recruitment decisions and the API rechecks permission, project state, and capacity when processing acceptance.

### Benefits and How to Evaluate Them

For members, discovery exposes the position being recruited for, expected weekly hours, and any missing skills. A profile can be reused across positions. For project leads, applications and invitations are organized by project and position. When recruiting succeeds, the application state, project membership, and filled-seat count can be checked against the same business records.

The trial will measure the value of these workflows. For recruitment, record the steps and elapsed time from selecting a position to both parties confirming its outcome. For profile entry, use the same sample information to compare manual entry with drafting, reviewing, and correcting AI suggestions. Infrastructure cost, demo setup time, and the effort needed to review suggestions inform the evaluation. These measurements support an assessment of value after the trial.

## 3. Solution Architecture

![Tindy architecture diagram](/my_aws_workshop/images/2-Proposal/3.png)

*Tindy architecture diagram for the proposal. The numbered walkthrough below reflects the repository configuration and application code.*

| Step | Component | Request or data flow |
| :--- | :--- | :--- |
| 1 | Amazon CloudFront and frontend S3 bucket | Users open the site over HTTPS. CloudFront serves the React/Vite build from a private S3 bucket. Origin Access Control (OAC) restricts frontend reads to the distribution. |
| 2 | CloudFront VPC Origin and Application Load Balancer | Dynamic paths such as `/api/*`, `/hubs/*`, and sign-in callbacks reach an internal ALB. The web app and API share a hostname, and caching is disabled for these dynamic paths. [CloudFront VPC origins](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html) |
| 3 | Amazon ECS, AWS Fargate, and Amazon ECR | ECS runs the ASP.NET Core .NET 10 API container from ECR. The API handles authentication, profiles, discovery, recruitment, and SignalR chat. ALB health checks track task health. |
| 4 | Media S3 bucket, GuardDuty, EventBridge, and SQS | A private bucket stores media. Certificate and AI input PDFs go through the scanning workflow. EventBridge routes GuardDuty verdicts to SQS so a worker can update document status. A dead-letter queue (DLQ) holds failed messages for investigation. |
| 5 | Amazon Bedrock | For AI input PDFs, the backend verifies a clean scan verdict for the corresponding S3 object before allowing extraction. Users can also submit text. The backend validates the output and returns draft profile fields, skills, experience, certificates, and warnings for review. |
| 6 | Amazon RDS for PostgreSQL | PostgreSQL 17 stores structured data such as accounts, profiles, projects, positions, applications, invitations, and messages. RDS uses private subnets, requires TLS, and restricts access through security groups. |

### Three Flows Through the Diagram

**Application access and data updates (1 → 2 → 3 → 6).** The browser loads the frontend from S3 through CloudFront. When users open projects, apply, or connect to chat, CloudFront routes the dynamic request through the VPC origin to the internal ALB. The ALB selects a healthy Fargate task. The API verifies the JWT and permissions for the project or conversation before reading from or writing to PostgreSQL. Web, API, and chat share a hostname.

**Certificate upload and PDF checks (3 → 4).** The API receives media and writes the PDF to private S3 storage. GuardDuty scans the file, EventBridge routes the verdict to SQS, and a worker matches it with the S3 object or version and updates document status. The API blocks reads or extraction that require a clean verdict while the document is pending or ineligible. The DLQ holds messages that fail processing after the configured retries.

**Profile extraction (4 → 5 → 3 → 6).** An AI input PDF can proceed to extraction after a clean scan. Users can also submit text directly. The backend calls Bedrock, validates the response, and returns draft profile fields, skills, experience, and certificates. The API writes the selected fields to PostgreSQL after the user reviews, edits, and confirms the save.

**Networking and availability.** The VPC spans two Availability Zones. The ALB is internal. Fargate tasks use public subnets and receive public IPv4 addresses in the current configuration. Security groups restrict API ingress to the ALB. The demo runbook uses one Fargate task, and the database template configures Single-AZ RDS. The two zones in the diagram show subnet placement. Scaling out requires verification of SignalR across tasks and background event processing.

**Security and operations.** Frontend assets and media use separate S3 buckets. The backend authenticates users with JWTs and enforces resource-level permissions. Secrets Manager holds connection details and sensitive configuration. GitHub Actions assumes deployment roles through OIDC. CloudWatch collects logs and monitors target health, HTTP 5xx responses, and the DLQ. SNS delivers alerts when notifications are configured.

### Components and Design Rationale

| Layer | Responsibility in the demo | Why this design fits |
| :--- | :--- | :--- |
| Web | React and Vite produce static assets. CloudFront reads from S3 through Origin Access Control and serves application routes. | The frontend can be released independently. Static assets and dynamic session data use separate cache behaviors. |
| API | ASP.NET Core runs in ECS Fargate. ECR stores release images. The ALB checks readiness and routes traffic to healthy tasks. | The active release can be traced to an image digest, and a new revision can be checked during rollout. |
| Business data | RDS for PostgreSQL stores profiles, project relationships, recruitment states, and chat history. | Relationships among projects, positions, and members can be checked when the application writes data. Migrations manage schema changes alongside application releases. |
| Files and events | The private media bucket stores files. GuardDuty scans PDFs, EventBridge delivers scan events, and the SQS consumer updates document state. | Upload and scan completion happen at different times. The queue exposes pending work and the DLQ provides a place to inspect failures. |
| AI | When enabled, the backend calls Bedrock and returns a structured draft. | Users can compare suggestions with source material. The backend validates profile fields and skill and role mappings before review. |

### Data Boundaries, Access, and Observability

**Data placement.** PostgreSQL stores identities, profiles, project relationships, and workflow states. The frontend S3 bucket contains HTML and static bundles. The media S3 bucket contains file content. A recovery manifest needs both the RDS snapshot reference and the relevant S3 object references so the team can verify that restored records still resolve to the corresponding files.

**Access by layer.** Security groups govern network access among the ALB, API tasks, and database. The backend authenticates the user and authorizes access to the relevant project, profile, or conversation. The ECS execution role supports startup operations such as pulling the image, initializing logging, and resolving configured secrets. The ECS task role covers AWS actions needed by application code, such as media access, scan-result processing, and AI calls when enabled. The OIDC permissions used by GitHub Actions for releases are managed independently of runtime permissions.

**Operational signals.** Target health and 5xx responses help narrow down whether a problem is in routing or the application. ECS events and CloudWatch Logs show whether a task could start, stopped, or returned an error. For PDFs, correlate the document state in Tindy with the GuardDuty result and pending SQS or DLQ messages. Recruitment notifications appear in the application through API polling. Chat uses SignalR. SNS serves operational alerts when configured.

### Demo Trade-offs and What Would Justify Scaling

| Decision | What the demo prioritizes | Evidence needed for a change |
| :--- | :--- | :--- |
| One Fargate task behind the ALB | Keep the always-running footprint small and simplify real-time chat for the demo. | Measure traffic and latency. Verify SignalR event delivery across tasks and check that background event processing remains consistent before increasing task count. |
| Single-AZ RDS | Keep the starting configuration simple and preserve a documented snapshot recovery path. | Define acceptable downtime and data loss. Estimate the cost of Multi-AZ and rehearse recovery before committing to higher availability. |
| API tasks in public subnets with ingress through an internal ALB | Use the existing outbound path for integrations. Security groups restrict access to the API. | Before moving to private subnets, inventory AWS and external destinations and compare endpoints and outbound paths for connectivity, cost, and operating effort. |
| On-demand AI extraction | Process inputs when a user requests a draft and make the run time and cost per attempt measurable. | Measure invalid outputs, latency, and token use before considering a background processing queue. |

## 4. Implementation and Testing

### Prepare the Infrastructure and Data

AWS CloudFormation defines six stacks: foundation, network, database, integrations, runtime, and edge. Foundation and network provide buckets, ECR, IAM, and networking. Database provides PostgreSQL. Integrations handles PDF scan results. Runtime deploys the API, ALB, and VPC origin. Edge uses that origin to route dynamic requests once the service is ready.

Choose Fresh to create a database, apply migrations, and seed demo data, or Restore to recover schema and data from a snapshot. For a release that changes the schema, take a snapshot first and run the EF Core migration in a separate ECS task. Verify the running API remains compatible with the migrated schema so the API can return to the previous revision if rollout fails.

### Release the Application

After CI passes, select the approved commit. Build the backend image, push it to ECR, and record the image digest used in the ECS task definition.

Verify the ECS service is stable and ALB targets are healthy. With the edge in live mode, check that `/api/health/ready` returns HTTP 200 with status Healthy through CloudFront.

Build the frontend with Vite, publish it to S3, and invalidate the CloudFront cache. Check the home page, login page, and JavaScript bundle through the same hostname.

Run acceptance scenarios with sample accounts and PDFs. Record the commit, image digest, ECS revision, test results, and snapshot ID where applicable. When rebuilding the demo, keep the edge in maintenance mode until the API, database, and required integrations are ready.

### Technical and Non-Functional Requirements

**Release environment.** The application uses React/Vite, ASP.NET Core .NET 10, EF Core, and PostgreSQL. Build the frontend to use same-origin API paths. Supply environment settings and sensitive configuration through the deployment workflow and AWS Secrets Manager.

**Consistency.** Membership admission must use the current project and position state. Check both membership and the position’s filled-seat count after acceptance and reload.

**Privacy.** Use test accounts and sample documents for demonstrations. Verify permissions when opening a document, requesting an AI draft, or accessing a conversation. Keep references and status evidence in test records. Keep secret values and résumé contents out of those records.

**Testability.** Prepare test data for successful actions and for rejected actions. Include a position with no open seats, a candidate who has not opted in, a PDF with a pending scan, and a user who lacks conversation access.

**Recoverability.** Record the relevant snapshot and version before schema changes. Identify a sample set of application records and media files to reconcile after Restore.

### Lifecycle Between Demonstrations

After a demo, check whether application writes or PDF scan work are still in progress. Once the environment is ready for backup, record the snapshot ID, timestamp, encryption status, API release, and current migration. Confirm that the S3 objects and ECR image needed by that recovery point are retained.

If the environment is taken down between demos, switch CloudFront to maintenance and verify that the distribution hostname is retained. The project runbook defines checks before removing runtime, database, and integrations in dependency order. Preserve the agreed recovery resources, including the snapshot, S3 data, ECR image, and configuration references. At the next demo, select Restore or Fresh based on the goal, verify the database endpoint and health checks, and reopen the edge when the required services are ready.

## 5. Sprint Plan and Deployment Milestones

Tindy development begins on Monday, October 5, 2026 (Week 4). The plan groups Weeks 4–9 into three two-week development sprints. Week 10 (November 16–22, 2026) is reserved for deployment to AWS, post-deployment verification, and completion of the demo.

| Sprint | Dates | Main work | End-of-sprint deliverable |
| :--- | :--- | :--- | :--- |
| Sprint 1 · Application foundation | Weeks 4–5 · October 5–18, 2026 | Start development. Agree on user flows and the data model. Set up React/Vite, ASP.NET Core, and PostgreSQL. Build sign-in, onboarding, profiles, and the skill and role catalogs. | A sample user can sign in, complete onboarding, and save a profile through the API. |
| Sprint 2 · Projects and recruiting | Weeks 6–7 · October 19–November 1, 2026 | Build project and position management, project discovery, match scoring, applications, teammate discovery, and invitations. Recheck recruitment eligibility and capacity at acceptance. | Sample data demonstrates the application and invitation flows. Successful acceptance updates project membership and filled seats correctly. |
| Sprint 3 · Integrations and release preparation | Weeks 8–9 · November 2–15, 2026 | Complete SignalR chat and in-app notifications. Integrate file storage, PDF scanning, and Bedrock profile drafts. Prepare CloudFormation templates, CI/CD configuration, and demo data. | A release candidate, test scenarios for core workflows, and reviewed AWS configuration. |
| Sprint 4 · Deployment and completion | Week 10 · November 16–22, 2026 | Deploy the frontend and API to AWS. Check CloudFront, the ALB, ECS, and RDS. Run acceptance scenarios through the demo hostname, resolve issues affecting core workflows, and record release details. | A deployed AWS demo with API readiness returning HTTP 200 and status Healthy, acceptance results, and redeployment instructions. |

The project completion milestone is in Week 10. From Week 11 through the December 14, 2026 internship wrap-up, reporting work covers the architecture diagram, demo scenarios, acceptance results, release details, and cost assumptions.

## 6. Budget Estimate

The always-on scenario runs for 730 hours per month in us-east-1. The figures come from the Tindy-Always-on workload estimate exported from AWS Pricing Calculator on November 18, 2026, using rates before discounts and excluding taxes and credits.

| Cost item | Basis | Reference cost |
| :--- | :--- | :--- |
| AWS Fargate | One Linux/x86 task with 0.5 vCPU and 2 GiB RAM. The estimate includes 365 vCPU-hours and 1,460 GB-hours of memory. | USD 21.26/month |
| Application Load Balancer | One internal ALB for 730 hours. | USD 16.43/month |
| Public IPv4 | One in-use public IPv4 address for the task for 730 hours. | USD 3.65/month |
| RDS for PostgreSQL | One Single-AZ db.t4g.micro for 730 hours (USD 11.68) and 20 GiB gp3 (USD 2.30). | USD 13.98/month |
| Edge, media, security, and AI | CloudFront, S3, ECR, Secrets Manager, GuardDuty, EventBridge, SQS, CloudWatch, and Bedrock. Charges vary with storage, requests, logs, scans, and tokens. | Usage-based |

The always-on baseline is USD 55.32 per month. The baseline excludes ALB LCUs, any RDS CPU credit charges and additional backup or snapshot storage, and the usage-based services listed above. Include retained snapshots and other retained resources when estimating the scheduled demo scenario. [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=9f501b7b4cfa90d4c3e108ec22f54c361374f883)

The public estimate displays USD 55.34 per month. Compared with the AWS Console export, the displayed Fargate and ALB costs are each USD 0.01 higher. The public ALB calculation uses 0.001 GB/hour of assumed traffic because the form requires a positive LCU input.

At pilot traffic, budget about USD 58 per month for always-on infrastructure and the modeled supporting services. The Cost Explorer export for November 1–17, 2026 shows core infrastructure activity concentrated on November 11–17. Treat that week as representative of a 30-day month. Add USD 1.60 for four stored secrets and scale the other imported usage priced in AWS Pricing Calculator (USD 0.1804 × 30/7, including Secrets Manager API calls). This planning figure excludes ALB LCUs, unverified EventBridge charges, RDS CPU credits, additional backup or snapshot storage, and taxes.

### Comparing Operating Scenarios

| Cost input | Always-on scenario | Scheduled demo scenario |
| :--- | :--- | :--- |
| Fargate and public IPv4 | Model runtime against the 730-hour reference month. | Count actual task hours, including setup and verification before each demo. |
| ALB and RDS | Count the hours the ALB and database are provisioned or running and add configuration-dependent charges. | Record ALB lifetime, database runtime, and snapshot storage kept between demos separately. |
| Retained storage and configuration | Measure S3 and ECR storage, backups, and retained configuration items. | Include resources retained for recovery while the edge is in maintenance. |
| Traffic, scanning, and AI | Use a defined baseline for requests, downloads, scans, and tokens. | Measure activity per session and record repeat processing. Hold usage assumptions constant when comparing runtime costs. |

After the trial, compare per-service billing with the amount of time resources remained provisioned. Classify differences as runtime, storage, traffic, or document processing. AWS Budgets can alert at an agreed threshold, while Cost Explorer provides a breakdown for investigating the source of a variance.

## 7. Risk Assessment

| Risk | Impact | Mitigation |
| :--- | :--- | :--- |
| API or database interruption in the demo configuration | Users temporarily lose access to application workflows. | Monitor readiness, ECS logs, and ALB health. Define snapshot recovery procedures and acceptable downtime. |
| PDF rejection, failed scan, or slow processing | A certificate or CV cannot yet be used. | Show scan status, block reads and extraction until eligibility is met, monitor the DLQ, and verify the corresponding S3 object. |
| Incomplete or incorrect AI suggestions | Incorrect information could enter a profile if unreviewed. | Validate the output structure and skill catalog mappings, then require user review and allow corrections before saving. |
| Migration or release mismatch | Web, API, and database schema versions can become incompatible. | Take a snapshot before changes, release in a compatible order, and record the commit, digest, and revision for recovery. |
| Resources continue accruing charges after the demo | ALB, compute, database, logs, or snapshots may continue billing. | Track the budget, identify retained resources, and review billing after cleanup. |

### Incident Triage and Recovery

**Identify the affected path.** Check API readiness, ALB target health, and ECS first. If the web and API are reachable and only PDF processing fails, inspect document state, the scan result, and the queue independently.

**Preserve evidence and recovery options.** Record the time, revision, image digest, and most recent change. For a suspected rollout failure, verify schema compatibility before attempting to return to an earlier API revision.

**Confirm recovery with sample data.** When restoring RDS, select the intended snapshot and verify TLS connectivity, database permissions, sample records, and linked S3 media. Compare the snapshot time with the last valid update to identify changes that need reconciliation.

**Reopen when checks pass.** Use readiness and the core workflow tests to decide when to return CloudFront to live. Record the cause, how the issue was detected, and any runbook step to update.

## 8. Expected Outcomes and Acceptance Criteria

| Acceptance scenario | How to test | Expected evidence |
| :--- | :--- | :--- |
| Member submits an application | Use a recruiting position with an open seat. Submit an application as a member and accept it as the project lead. | The application moves from Pending to Accepted. The member appears in the project, the position’s filled-seat count increases once, and the data remains consistent after reloading. |
| Project lead invites a teammate | Use separate test data. The candidate opts in, the lead sends an invitation, and the candidate opens the notification and accepts. | The invitation identifies the correct project and position. The seat count stays the same while the invitation is pending and updates after successful acceptance. |
| Chat through CloudFront | Open two signed-in sessions with access to the same conversation and send a message from one session. | The other session receives it in the correct conversation. DevTools records the WebSocket upgrade with status 101. |
| PDF scanning | Upload a sample certificate or AI input PDF. Track the scan state from pending to verdict. | Actions requiring a clean verdict remain blocked while the scan is pending. A clean verdict for the matching S3 object or version makes the document eligible for its intended use. |
| AI profile draft | Submit text or a scanned and eligible PDF, review the suggested fields, make edits, and confirm save. | Suggestions are structured and warnings identify missing or conflicting data. The saved profile reflects the fields the user confirmed. |
| Release and recovery | Open the site through CloudFront, check API readiness and core workflows, then rebuild a test environment from a selected snapshot using the runbook. | Web and API share a hostname, readiness returns 200 and Healthy, and sample data matches the recovery point. Release details, snapshot ID, and reconciliation results are recorded. |

### Handover Evidence

For each acceptance run, record the environment and version, the action performed, the expected result, and what was observed. Use sample accounts for recruitment and record the before-and-after states. For releases, capture the commit, image digest, readiness result, and snapshot reference. For PDF and AI checks, use sample documents and retain the scan state and an example reviewed draft. This lets a reader verify the result without access to an individual’s account or personal data.

**Technical basis.** See the templates in `tindy-infrastructure/cloudformation`, the Tindy release and redeployment runbooks, `DISCOVERY_PROJECT_FLOW.md`, `TEAMMATE_FLOW.md`, `AI_PROFILE_EXTRACTION.md`, and the backend chat documentation.
