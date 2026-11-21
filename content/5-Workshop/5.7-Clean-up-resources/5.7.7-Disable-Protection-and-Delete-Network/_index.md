---
title: "DISABLE PROTECTION AND DELETE NETWORK"
date: 2026-11-22
weight: 7
chapter: false
pre: "<b> 5.7.7 </b>"
---

## Prepare Network deletion

After Runtime, Database, Integrations and Edge deletion, inspect VPC dependencies before removing Network. The preceding AWS CLI read returned no network interfaces for the Tindy VPC. Network has termination protection enabled.

1. Select **CloudFormation → Stacks → Active → tindy-network → Resources**.
2. Review the 26 resources managed by `network.yaml`, including the VPC, Internet Gateway and attachment, subnets, route tables, route, subnet associations, security groups and ingress rules.

**📸 Capture both parts of the resource list.**



[Network resources](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-24-network-resources-before.png)



[Subnets, route tables and VPC](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-24a-network-resources-bottom.png)

3. Select **Stack actions → Edit termination protection**, confirm `tindy-network` and choose **Deactivated**.

**📸 Capture the selection before saving.**



[Disable Network termination protection](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-25-network-disable-termination-protection.png)

4. Save, reopen **Delete stack**, enter `tindy-network` and leave Express mode unchecked.

**📸 Capture the deletion confirmation.**



[Network deletion confirmation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-26-network-delete-confirm.png)

5. Confirm deletion and monitor Events. If the stack disappears, select **Deleted** and locate `tindy-network`.
6. Verify `tindy-network — DELETE_COMPLETE`. The image also confirms completed deletion of the VPC and Internet Gateway.

**📸 Capture completed stack deletion.**



[Network deleted](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-27-network-deleted.png)



Network deletion is complete. For `DELETE_FAILED` in another run, inspect Logical ID and Status reason to resolve dependencies before retrying.

