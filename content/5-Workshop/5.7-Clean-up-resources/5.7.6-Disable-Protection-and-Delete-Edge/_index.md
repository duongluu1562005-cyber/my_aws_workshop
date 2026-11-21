---
title: "DISABLE PROTECTION AND DELETE EDGE"
date: 2026-11-22
weight: 6
chapter: false
pre: "<b> 5.7.6 </b>"
---

## Prepare Edge deletion and handle termination protection

After Integrations deletion, select **CloudFormation → Stacks → Active → tindy-edge → Resources**. Its four resources are `EdgeDistribution`, `FrontendBucketPolicy`, `FrontendOriginAccessControl` and `MaintenanceRoutingFunction`.

**📸 Capture the resource list before deletion.**

[Edge resources before deletion](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-21-edge-resources-before.png)

Selecting **Delete stack** displays a message that termination protection is enabled and must be disabled first.

[Edge termination protection enabled](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-21a-edge-termination-protection.png)

1. Select **Edit termination protection** in the message.
2. Confirm the dialog identifies `tindy-edge`, then choose **Deactivated**.

**📸 Capture the selection before saving.**

[Select Deactivated for Edge](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-21b-edge-disable-termination-protection.png)

3. Click **Save** to apply, then reopen **Delete stack**.
4. After saving, select **Delete stack**, enter `tindy-edge` and leave Express mode unchecked.

**📸 Capture the deletion confirmation.**

[Edge deletion confirmation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-22-edge-delete-confirm.png)

5. Confirm deletion and monitor Events. If the stack disappears, select **Deleted** and locate the stack created on **11 November 2026 at 12:23:27 UTC+07**.
6. Verify `tindy-edge — DELETE_COMPLETE`. Events also show completed deletion of `MaintenanceRoutingFunction` and `FrontendOriginAccessControl`.

**📸 Capture completed stack deletion.**

[Edge deleted](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-23-edge-deleted.png)

Edge deletion is complete. Redeployment will create a new distribution and update application settings for its hostname.
