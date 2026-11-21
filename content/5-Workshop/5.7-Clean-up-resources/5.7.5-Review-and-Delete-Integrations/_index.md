---
title: "REVIEW AND DELETE INTEGRATIONS"
date: 2026-11-22
weight: 5
chapter: false
pre: "<b> 5.7.5 </b>"
---

## Review and delete Integrations

After Runtime and Database deletion, select **CloudFormation → Stacks → Active → tindy-integrations → Resources**. The stack reports `UPDATE_COMPLETE` and contains 14 resources.

`integrations.yaml` manages the GuardDuty PDF protection plan and role, EventBridge result rule, primary SQS queue and DLQ with queue policies, SNS topic/policy/subscription and CloudWatch alarms. ALB alarms remain owned by Integrations after Runtime deletion.

1. Confirm the stack name and resource list.
2. Scroll through the resources to record the scanning, alerting and queue components.

**📸 Capture the stack state and both parts of the resource list.**

[Integrations before deletion](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-18-integrations-resources-before.png)

[Scanning and alarm resources](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-18a-integrations-resources-top.png)

[SNS and SQS resources](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-18b-integrations-resources-bottom.png)

3. An AWS CLI check preceding these screenshots returned `0` for visible, in-flight and delayed messages in both `tindy-integrations-pdf-scan` and `tindy-integrations-pdf-scan-dlq`. These are approximate values at the time of that check.
4. Click **Delete stack**, confirm `tindy-integrations`, enter its name and leave Express mode unchecked.

**📸 Capture the deletion confirmation dialog.**

[Integrations deletion confirmation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-19-integrations-delete-confirm.png)

5. Confirm deletion. When the stack disappears from Active, select **Deleted** and identify the stack created on **13 November 2026 at 20:29:01 UTC+07**.
6. Verify the stack reports `DELETE_COMPLETE`. The Resources screenshot also shows completed deletion of the displayed alarms, protection plan, EventBridge rule, IAM role and SNS subscription.

**📸 Capture the stack status and deleted resources.**

[Integrations deleted](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-20-integrations-deleted.png)

Integrations deletion is complete. Edge, Network and Foundation are next.
