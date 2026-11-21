
# DEPLOY INTEGRATIONS

`integrations.yaml` configures GuardDuty Malware Protection for S3 PDF scanning, EventBridge result delivery, SQS and a DLQ. It also defines IAM and queue policies, CloudWatch alarms and optional SNS email notification resources.

## Create the stack

1. In `us-east-1`, select **CloudFormation → Create stack → With new resources (standard)** and upload `integrations.yaml`.

**📸 Capture the selected filename.**

[Upload integrations.yaml](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-23-integrations-template.png)

2. Name the stack `tindy-integrations`. Set `MediaBucketName` from Foundation Outputs and provide the alarm recipient in `ScanAlarmEmail`. Leave `AlbMetricLoadBalancer` and `AlbMetricTargetGroup` empty until Runtime creates the new ALB and target group, then update both dimensions through a change set.

**📸 Capture the stack name and parameters. Review email visibility before publishing.**

[Integrations parameters](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24-integrations-parameters.png)

3. Add tags `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Keep Express mode disabled and rollback enabled, acknowledge IAM capabilities, review and create the stack.
4. Wait for `CREATE_COMPLETE` and record `GuardDutyProtectionPlanArn` and `GuardDutyQueueUrl` from Outputs for Runtime.
5. Under **GuardDuty → Malware Protection for S3**, verify the new plan is **Active**, tagging is enabled and prefixes include `certificates/pdf/` and `ai-input/pdf/`.
6. For the configured email, confirm the SNS subscription from its email and verify the confirmed state in SNS.

**📸 Capture completed stack status, Outputs, the Active protection plan and confirmed subscription.**

## Verified progress

The correct template and stack name are selected. The media bucket matches Foundation and both ALB metric dimensions are empty. An email was entered and is masked by the template NoEcho setting.

Project, Environment and Owner tags are configured. Express mode is disabled, rollback and Use deletion policy are selected, and IAM capabilities are acknowledged.

[Integrations tags](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24a-integrations-tags.png)

[Integrations options](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24b-integrations-options.png)

[Integrations IAM acknowledgement](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24c-integrations-iam-capabilities.png)

## Stack creation and SNS confirmation results

Stack `tindy-integrations` reached **CREATE_COMPLETE** with **Outputs (7)**.

[Integrations completed](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-25-integrations-complete.png)

[Seven Integrations Outputs](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-26-integrations-outputs.png)

Runtime will use `GuardDutyProtectionPlanArn` and `GuardDutyQueueUrl`. Other Outputs provide the plan ID, queue ARN, DLQ URL, DLQ alarm name and SNS topic ARN.

1. Open **AWS Notification - Subscription Confirmation**, verify topic `tindy-integrations-scan-alerts` and click **Confirm subscription**.

[SNS confirmation email](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-27-integrations-sns-email.png)

2. On the SNS page, click **Confirm Subscription**.

[SNS confirmation page](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-27a-integrations-sns-confirm-page.png)

3. Verify **Subscription confirmed!** and the subscription ARN.

[SNS confirmed](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-27b-integrations-sns-confirmed.png)

SNS confirmation evidence is complete. Verify protection plan status **Active**, tagging and both PDF prefixes to finish Integrations validation.

## AWS CLI verification

A subsequent read confirmed protection plan `9cd0f69716d5ca261abc` reports `ACTIVE`, no status reasons, tagging `ENABLED`, and protects the media bucket prefixes `certificates/pdf/` and `ai-input/pdf/`. SNS returns a confirmed email subscription ARN matching the confirmation page.

Section 5.4.2 resource configuration has been verified. The GuardDuty detail screenshots below confirm the Console state. ALB/API alarms will be configured after Runtime provides the new ALB and target group.

## GuardDuty Console verification

1. Open **GuardDuty → Malware Protection → S3 → View feature**. One bucket is protected.

[Malware Protection overview](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-28a-guardduty-malware-protection.png)

2. Under **Protected buckets**, verify the Foundation media bucket and new protection plan are **Active**. The list shows **2 prefix**.

[Protected bucket](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-28b-guardduty-protected-bucket.png)

3. Open bucket details. Confirm **Status = Active** and **Tag scanned objects = Yes**. CLI separately verified `certificates/pdf/` and `ai-input/pdf/`.

[Active plan and tagging](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-28-integrations-guardduty-active.png)

Section 5.4.2 verification is complete, covering stack completion, Outputs, confirmed SNS subscription and Active GuardDuty plan. PDF upload testing follows API and schema readiness.

## Add monitoring after Runtime becomes Healthy

After Runtime and Edge are live, a read-only inspection of `tindy-runtime` CloudFormation resources supplies the current ARNs. The following update instructions await execution evidence.

Create a Standard change set on `tindy-integrations`, choose Use existing template, and name it `tindy-integrations-enable-alb-alarms`. Update only:

| **Parameter**           | **Value**                                               |
| :---------------------- | :------------------------------------------------------ |
| `AlbMetricLoadBalancer` | `app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4`        |
| `AlbMetricTargetGroup`  | `targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa` |

These ARN suffixes supply CloudWatch dimensions. Keep the existing MediaBucketName and ScanAlarmEmail. The previously confirmed SNS email subscription continues to receive notifications.

| **Alarm**                  | **Template condition**                                                                                 |
| :------------------------- | :----------------------------------------------------------------------------------------------------- |
| `AlbNoHealthyTargetsAlarm` | Minimum HealthyHostCount below 1 over three 60-second periods, with missing data treated as breaching. |
| `AlbGenerated5xxAlarm`     | Sum of at least five ELB 5xx responses in 300 seconds.                                                 |
| `ApiTarget5xxAlarm`        | Sum of at least five target 5xx responses in 300 seconds.                                              |

Expect three alarm Adds and a Modify to `ScanAlarmTopicPolicy` allowing notifications from the new alarms. **📸 Capture Parameters and Resource changes at CREATE_COMPLETE / AVAILABLE** before execution. After updating, inspect CloudWatch alarms and dimensions and record their actual states.

### Recorded monitoring configuration

The pre-update screenshot records `tindy-integrations` at CREATE_COMPLETE with seven Outputs, including queue, DLQ, GuardDuty protection plan and scan alarm. These integration resources exist before adding the ALB alarms.

[Integrations before monitoring update](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68a-integrations-before-monitoring.png)

The wizard selects **Standard change set** and **Use existing template** for `tindy-integrations`. This uses the current template to enable conditional alarms through parameters.

[Monitoring uses existing template](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68b-monitoring-existing-template.png)

`AlbMetricLoadBalancer` is set to `app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4`, matching the current InternalAlb ARN suffix. CloudWatch uses this dimension to identify the monitored load balancer.

[ALB metric dimension](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68c-monitoring-alb-dimension.png)

`AlbMetricTargetGroup` is set to `targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa`, matching the current ApiTargetGroup ARN suffix. This dimension identifies API targets for HealthyHostCount and target 5xx alarms.

[Target group metric dimension](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68d-monitoring-target-group-dimension.png)

Both inputs match the read-only AWS resource inspection. Continue with Next, retain other inputs and options, acknowledge IAM capability if requested, and create the change set. **📸 Capture Resource changes at CREATE_COMPLETE / AVAILABLE.** Change-set creation and monitoring update execution remain unverified at this milestone.

### Monitoring change-set review

Change set `tindy-integrations-enable-alb-alarms` reached **CREATE_COMPLETE**, Execution status **AVAILABLE**, with exactly four changes:

| **Logical ID**             | **Action** | **Resource type**        | **Replacement** |
| :------------------------- | :--------- | :----------------------- | :-------------- |
| `AlbGenerated5xxAlarm`     | Add        | `AWS::CloudWatch::Alarm` | —               |
| `AlbNoHealthyTargetsAlarm` | Add        | `AWS::CloudWatch::Alarm` | —               |
| `ApiTarget5xxAlarm`        | Add        | `AWS::CloudWatch::Alarm` | —               |
| `ScanAlarmTopicPolicy`     | Modify     | `AWS::SNS::TopicPolicy`  | False           |

Three alarms are added for ALB/API monitoring. The SNS policy updates in place to allow notifications from the new alarms. The scope matches the guide. Execution remains unverified at this milestone.

[Monitoring change set reviewed](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-69-monitoring-changeset.png)

Select **Execute change set**, retain current rollback behavior and Use deletion policy if prompted, then confirm. Wait for stack **UPDATE_COMPLETE**. **📸 Capture the updated stack.** Open **CloudWatch → Alarms → All alarms**, Region N. Virginia, and search for `tindy-integrations`. **📸 Capture all four alarms**, including the three additions and existing ScanDeadLetterAlarm, with their actual states. If new alarms remain INSUFFICIENT_DATA, refresh after a few minutes. For ALARM, inspect State reason and metrics before drawing conclusions.

### Verified monitoring results

Execution retained rollback and Use deletion policy. After execution, `tindy-integrations` reached **UPDATE_COMPLETE**.

[Monitoring execution confirmation](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-70a-monitoring-execute.png)

[Integrations monitoring update complete](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-70b-monitoring-update-complete.png)

CloudWatch shows all four alarms **OK** with **Actions enabled**: ApiTarget5xxAlarm, AlbGenerated5xxAlarm, AlbNoHealthyTargetsAlarm and ScanDeadLetterAlarm. This records their states at capture time. Fault injection to verify alarm notification delivery has not been performed.

[Four alarms OK with actions enabled](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-71-monitoring-four-alarms-ok.png)
