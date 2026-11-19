
## 5.4.2 — Deploy Integrations

### Overview

After deploying the database, the next step is to configure the services responsible for PDF malware scanning, scan result processing, and system monitoring in Tindy.

The `integrations.yaml` template manages the following AWS services:

- **Amazon GuardDuty Malware Protection:** Scans PDF files uploaded to Amazon S3.
- **Amazon EventBridge:** Routes malware scan result events to the processing queue.
- **Amazon SQS:** Receives scan result messages for the API worker.
- **Dead-letter Queue (DLQ):** Retains messages that cannot be processed successfully under the redrive policy.
- **Amazon SNS:** Sends operational alerts through email notifications.
- **Amazon CloudWatch:** Monitors application and infrastructure health.
- **IAM and Queue Policies:** Provide the permissions required for these services.

The deployment has two stages:

1. Create the Integrations stack, enable malware scanning, and confirm the SNS subscription.
2. Add ALB and API monitoring after Runtime and Edge are deployed.

### Step 1: Upload the Integrations Template

Open the AWS Management Console and select **US East (N. Virginia) — us-east-1**.

Navigate to:

**CloudFormation → Stacks → Create stack → With new resources (standard)**

Under **Specify template**:

1. Select **Choose an existing template**.
2. Choose **Upload a template file**.
3. Upload `integrations.yaml` from the infrastructure repository.
4. Click **Next**.

![Upload Integrations template](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-23-integrations-template.png)

### Step 2: Configure Integrations Parameters

On the **Specify stack details** page, enter the stack name:

`tindy-integrations`

Configure the following parameters:

| Parameter | Value |
|---|---|
| `MediaBucketName` | From Foundation Outputs |
| `ScanAlarmEmail` | Email address for operational alerts |
| `AlbMetricLoadBalancer` | Empty |
| `AlbMetricTargetGroup` | Empty |

`MediaBucketName` must match the existing media bucket created by the Foundation stack.

The `ScanAlarmEmail` parameter is used to configure the SNS email subscription. Its value is masked by the template's `NoEcho` setting.

Both ALB metric dimensions are left empty because Runtime has not yet created the new ALB and target group.

These parameters will be updated through a CloudFormation change set after Runtime deployment.

![Integrations parameters](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24-integrations-parameters.png)

### Step 3: Configure Stack Options

Continue to the stack configuration page and add the following tags:

| Key | Value |
|---|---|
| Project | Tindy |
| Environment | tindy |
| Owner | TindyTeam |

These tags help identify and organize the AWS resources associated with the Tindy project.

![Integrations stack tags](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24a-integrations-tags.png)

Keep the following options:

- **Express mode:** Disabled.
- **Rollback:** Enabled.
- **Deletion policy option:** Use deletion policy.

![Integrations stack options](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24b-integrations-options.png)

Because the template creates IAM-related resources, acknowledge the required IAM capabilities before deployment.

![IAM capabilities acknowledgement](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24c-integrations-iam-capabilities.png)

Review the template, parameters, and deployment settings before creating the stack.

### Step 4: Create and Verify the Integrations Stack

Submit the CloudFormation stack creation.

CloudFormation begins provisioning GuardDuty, SQS, SNS, EventBridge, and their supporting resources.

Open the **Events** tab to monitor the deployment.

The stack `tindy-integrations` was created successfully with the status:

`CREATE_COMPLETE`

![Integrations deployment completed](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-25-integrations-complete.png)

After deployment, open the **Outputs** tab.

CloudFormation generated **7 Outputs**:

| Output | Purpose |
|---|---|
| `GuardDutyProtectionPlanArn` | ARN of the GuardDuty Malware Protection plan. |
| Protection Plan ID | Identifier of the protection plan. |
| `GuardDutyQueueUrl` | URL of the SQS queue receiving scan results. |
| Queue ARN | ARN of the scan result processing queue. |
| DLQ URL | URL of the dead-letter queue. |
| DLQ Alarm Name | Name of the CloudWatch alarm monitoring the DLQ. |
| SNS Topic ARN | ARN of the SNS topic used for notifications. |

Two important Outputs for Runtime are:

- `GuardDutyProtectionPlanArn`
- `GuardDutyQueueUrl`

![Seven Integrations Outputs](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-26-integrations-outputs.png)

### Step 5: Confirm the SNS Email Subscription

After stack creation, Amazon SNS sends a subscription confirmation email to the configured recipient.

Open the email titled:

**AWS Notification - Subscription Confirmation**

Verify the SNS topic:

`tindy-integrations-scan-alerts`

Click **Confirm subscription**.

![SNS confirmation email](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-27-integrations-sns-email.png)

The SNS confirmation page opens in the browser.

Click **Confirm Subscription** to complete the process.

![SNS subscription confirmation page](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-27a-integrations-sns-confirm-page.png)

After confirmation, the page displays:

`Subscription confirmed!`

The subscription ARN is also available for verification.

![SNS subscription confirmed](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-27b-integrations-sns-confirmed.png)

The email subscription is now confirmed and ready to receive operational notifications.

### Step 6: Verify GuardDuty Malware Protection

After deploying Integrations and confirming SNS, verify the GuardDuty configuration.

Navigate to:

**Amazon GuardDuty → Malware Protection → S3 → View feature**

The overview shows one protected S3 bucket.

![GuardDuty Malware Protection overview](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-28a-guardduty-malware-protection.png)

Open **Protected buckets** and locate the Tindy media bucket.

The Console confirms:

- The Foundation media bucket is protected.
- The protection plan is **Active**.
- Two S3 prefixes are configured.

![GuardDuty protected media bucket](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-28b-guardduty-protected-bucket.png)

Open the bucket details and verify:

| Setting | Result |
|---|---|
| Protection Status | Active |
| Tag scanned objects | Yes |
| Protected prefixes | 2 |

![Active GuardDuty plan and object tagging](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-28-integrations-guardduty-active.png)

AWS CLI verification also confirmed:

- **Protection Plan ID:** `9cd0f69716d5ca261abc`
- **Protection status:** `ACTIVE`
- **Status reasons:** None
- **Object tagging:** `ENABLED`

The protected S3 prefixes are:

- `certificates/pdf/`
- `ai-input/pdf/`

AWS CLI also confirmed that the SNS email subscription ARN matches the completed confirmation.

At this stage, the GuardDuty configuration, stack Outputs, and SNS subscription have been verified.

Application-level PDF upload testing will be performed after the API and database schema are ready.

### Step 7: Prepare Monitoring After Runtime Deployment

After Runtime and Edge are deployed, update Integrations to enable monitoring for the new ALB and API target group.

Before the update, the `tindy-integrations` stack remained at `CREATE_COMPLETE` with seven Outputs, including its GuardDuty, SQS, DLQ, and SNS resources.

![Integrations before monitoring update](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68a-integrations-before-monitoring.png)

The new ALB and target group references were obtained through a read-only inspection of the `tindy-runtime` CloudFormation resources.

Open `tindy-integrations` and select:

**Stack actions → Create a change set**

Configure:

- **Change set type:** Standard change set.
- **Template:** Use existing template.
- **Change set name:** `tindy-integrations-enable-alb-alarms`

Using the existing template allows the conditional CloudWatch alarms to be enabled through parameter updates.

![Use existing template for monitoring update](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68b-monitoring-existing-template.png)

### Step 8: Configure ALB and Target Group Dimensions

Update the following parameters:

| Parameter | Value |
|---|---|
| `AlbMetricLoadBalancer` | `app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4` |
| `AlbMetricTargetGroup` | `targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa` |

These values were obtained from the ARN suffixes of the deployed Internal ALB and API Target Group.

**Note:** These values belong to the verified deployment. If Runtime is redeployed and the resource ARNs change, retrieve the updated values from the current resources.

#### ALB Metric Dimension

Set `AlbMetricLoadBalancer` to:

`app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4`

This CloudWatch dimension identifies the Application Load Balancer being monitored.

![ALB metric dimension](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68c-monitoring-alb-dimension.png)

#### Target Group Metric Dimension

Set `AlbMetricTargetGroup` to:

`targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa`

This dimension identifies the API target group for HealthyHostCount and HTTP 5xx monitoring.

![Target group metric dimension](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68d-monitoring-target-group-dimension.png)

Keep `MediaBucketName`, `ScanAlarmEmail`, and the remaining parameters unchanged.

The previously confirmed SNS email subscription continues to receive notifications.

### Step 9: Review the Monitoring Change Set

Continue through the configuration pages, retaining the existing options and acknowledging IAM capabilities if requested.

Create the change set and wait until its status reaches:

`CREATE_COMPLETE — AVAILABLE`

The change set `tindy-integrations-enable-alb-alarms` was created successfully with **four resource changes**:

| Logical ID | Action | Resource Type |
|---|---|---|
| `AlbGenerated5xxAlarm` | Add | `AWS::CloudWatch::Alarm` |
| `AlbNoHealthyTargetsAlarm` | Add | `AWS::CloudWatch::Alarm` |
| `ApiTarget5xxAlarm` | Add | `AWS::CloudWatch::Alarm` |
| `ScanAlarmTopicPolicy` | Modify | `AWS::SNS::TopicPolicy` |

Three new CloudWatch alarms are added for ALB and API monitoring.

The SNS Topic Policy is updated in place, without replacement, to allow notifications from the new alarms.

![Monitoring change set review](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-69-monitoring-changeset.png)

The alarms use the following conditions:

| Alarm | Condition |
|---|---|
| `AlbNoHealthyTargetsAlarm` | Minimum HealthyHostCount below 1 for three consecutive 60-second periods, treating missing data as breaching. |
| `AlbGenerated5xxAlarm` | At least five ELB-generated HTTP 5xx responses within 300 seconds. |
| `ApiTarget5xxAlarm` | At least five target-generated HTTP 5xx responses within 300 seconds. |

### Step 10: Execute the Monitoring Change Set

After reviewing the changes, select:

**Execute change set**

Keep rollback enabled and retain **Use deletion policy**, then confirm execution.

![Execute monitoring change set](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-70a-monitoring-execute.png)

Monitor the CloudFormation stack until the update finishes.

After execution, `tindy-integrations` reached:

`UPDATE_COMPLETE`

![Integrations monitoring update completed](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-70b-monitoring-update-complete.png)

This confirms that the three CloudWatch alarms were added and the SNS Topic Policy was updated successfully.

### Step 11: Verify CloudWatch Alarms

After the update, navigate to:

**CloudWatch → Alarms → All alarms**

Select **US East (N. Virginia)** and search for:

`tindy-integrations`

CloudWatch displays four alarms:

- `ApiTarget5xxAlarm`
- `AlbGenerated5xxAlarm`
- `AlbNoHealthyTargetsAlarm`
- `ScanDeadLetterAlarm`

At the time of verification, all four alarms showed:

`OK`

Their actions were also:

`Actions enabled`

![Four CloudWatch alarms OK](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-71-monitoring-four-alarms-ok.png)

These results confirm that the alarms were created and were in the OK state when the screenshot was captured.

However, fault injection to verify alarm notification delivery has not been performed.

### Deployment Result

The Integrations deployment and monitoring update completed successfully.

| Verification | Result |
|---|---|
| Stack name | `tindy-integrations` |
| Initial stack status | `CREATE_COMPLETE` |
| Stack Outputs | 7 |
| GuardDuty Protection Plan | `ACTIVE` |
| Object Tagging | `ENABLED` |
| Protected PDF prefixes | 2 |
| SNS Email Subscription | Confirmed |
| Monitoring change set | `CREATE_COMPLETE — AVAILABLE` |
| Change set resource changes | 4 |
| Final stack status | `UPDATE_COMPLETE` |
| CloudWatch alarms | 4 |
| Alarm states at verification | `OK` |
| Alarm actions | Enabled |
| Alarm notification fault testing | Not performed |

Integrations now provides the infrastructure required for PDF malware scanning, scan result delivery to the API worker, and monitoring of the ALB, API, and dead-letter queue.

GuardDuty, SNS, and CloudWatch configurations have been verified. Application-level PDF upload and scan result processing will be tested after the API and database schema are ready.

**Integrations deployment and the post-Runtime monitoring update have been completed successfully.**
