---
title: "DISABLE PROTECTION AND DELETE DATABASE"
date: 2026-11-19
weight: 4
chapter: false
pre: "<b> 5.7.4 </b>"
---

## Prepare Database deletion

`database.yaml` exposes `EnableDeletionProtection`. The DB instance currently has `DeletionPolicy: Snapshot` and `UpdateReplacePolicy: Snapshot`.

1. Return to Active stacks and open `tindy-database`.

![Database before updating](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-12a-database-before.png)

2. Create a Standard change set using the existing template.

![Create Database change set](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-12b-database-create-changeset.png)

3. Name it `tindy-database-prepare-delete`. Set `EnableDeletionProtection = false`, retaining other parameters. The existing stack uses `DBProvisionMode = Restore` and a snapshot identifier. Preserve these for this update. Choose `Fresh` when creating the replacement stack.

**📸 Capture the parameters.**



![Database parameters](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-12-database-delete-parameter.png)

4. Keep the current options and create the change set. Confirm only `PostgresInstance → Modify → Replacement = False`.

![Database change set](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-13-database-delete-changeset.png)

5. Execute with **Use deletion policy** and wait for `tindy-database — UPDATE_COMPLETE`.

![Database update completed](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-14-database-prepared.png)

6. Follow the `PostgresInstance` Physical ID to RDS. Under Configuration, confirm **Deletion protection = Disabled**. The screenshot is a close-up of this field.

![Protection disabled](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-15-database-protection-disabled.png)

7. Return to **CloudFormation → tindy-database → Delete stack**, enter its name, and leave Express mode unchecked.

**📸 Capture the confirmation dialog.**



![Database deletion confirmation](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-16-database-delete-confirm.png)

8. Confirm deletion and monitor Events. The template requests a snapshot before deleting the DB instance.
9. If the stack disappears, select **Deleted** and identify the current stack by creation time.
10. Verify `tindy-database — DELETE_COMPLETE`. The screenshot also confirms completed deletion of the subnet and parameter groups.

**📸 Capture completed stack deletion.**



![Database deletion completed](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-17-database-deleted.png)



Database stack deletion is complete. Check the resulting snapshot separately in RDS Snapshots when managing retained storage.

