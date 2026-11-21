---
title: "PREPARE AND DELETE RUNTIME"
date: 2026-11-22
weight: 3
chapter: false
pre: "<b> 5.7.3 </b>"
---

## Prepare Runtime for deletion

`runtime.yaml` manages ECS Fargate, the internal ALB and CloudFront VPC Origin. Switch the `AlbIngressFromCloudFront` source from the service security group to the CloudFront origin-facing prefix list before deleting Runtime.

1. Record Runtime Outputs, including the cluster, service, task definition, ALB, target group and VPC Origin.

[Runtime outputs](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06a-runtime-outputs-before.png)

2. Create a Standard change set using the existing template, named `tindy-runtime-prepare-delete`.

[Create Runtime change set](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06b-runtime-create-changeset.png)

3. Clear `CloudFrontServiceSecurityGroupId`, retaining other parameters.

[Empty security group parameter](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06-runtime-delete-parameters.png)

4. Set `CloudFrontOriginPrefixListId` to the verified value `pl-3b927c52` for this run. For another environment, look up IPv4 managed prefix list `com.amazonaws.global.cloudfront.origin-facing` in the target Region.

[Prefix list parameter](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06c-runtime-prefix-list-parameter.png)

5. Acknowledge IAM capabilities, keep Express mode disabled and rollback enabled, and create the change set.

[IAM capabilities](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06d-runtime-iam-capabilities.png)



[Runtime review options](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06e-runtime-changeset-review.png)

6. Wait for `CREATE_COMPLETE` and `AVAILABLE`. Confirm only `AlbIngressFromCloudFront → Modify → Replacement = True`. The `ReplaceAndDelete` policy action applies to that ingress rule.

**📸 Capture Resource changes before execution.**



[Ingress replacement](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-07-runtime-delete-changeset.png)

7. Execute using the deletion policy and monitor Events. This image captures the start of the update.

[Runtime update started](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-08a-runtime-update-started.png)

8. Wait for stack `UPDATE_COMPLETE`. In Resources, confirm the rule updated successfully.

[Runtime prepared](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-08-runtime-prepared.png)

9. Open **tindy-network → Resources → AlbSecurityGroup**, follow its Physical ID, and inspect Inbound rules. Confirm HTTP TCP 80 uses source `pl-3b927c52`.

**📸 Capture the security group, port and prefix list source.**



[Verified ALB ingress](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-09-alb-prefix-list.png)



If the Console reports no changes while the service security group is still referenced, pass an explicit empty parameter through the CLI. This recorded run completed the change through the Console.

## Delete Runtime

1. Select **CloudFormation → tindy-runtime → Delete stack**.
2. Enter `tindy-runtime` and leave Express mode unchecked.

**📸 Capture the confirmation dialog.**



[Runtime deletion confirmation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-10-runtime-delete-confirm.png)

3. Confirm deletion and monitor Events. If the stack disappears, select **Deleted** and identify the current stack by creation time.
4. Confirm `tindy-runtime — DELETE_COMPLETE`.

**📸 Capture completed stack deletion.**



[Runtime deleted](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-11-runtime-deleted.png)



For `DELETE_FAILED`, inspect Logical ID and Status reason before retrying.

