---
title: "DEPLOY NETWORK"
date: 2026-11-19
weight: 2
chapter: false
pre: "<b> 5.3.2 </b>"
---

`network.yaml` defines VPC `10.20.0.0/16`, an Internet Gateway, six subnets, route tables and security groups for ALB, ECS, migration and the database. Its 26 resources and nine Outputs support the database and runtime in `us-east-1`.

## 1. Create the stack

1. Select **CloudFormation → Create stack → With new resources (standard)** in **N. Virginia (`us-east-1`)**.
2. Choose **Choose an existing template → Upload a template file**, uploading `network.yaml` from `tindy-infrastructure/cloudformation`.

**📸 Capture the selected network.yaml file.**



![Upload network.yaml](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-15-network-template.png)

3. Click Next and name the stack `tindy-network`. This template has no Parameters section.

**📸 Capture the stack name.**

4. Add tags `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Keep Express mode disabled and rollback enabled.
5. Review the stack and template, then submit creation using the Console button.
6. Monitor Events until `tindy-network — CREATE_COMPLETE`. For errors, inspect Logical ID and Status reason before proceeding.

**📸 Capture CREATE_COMPLETE and all Outputs.**

## 2. Outputs for subsequent stacks

Outputs include `VpcId`, `PublicTaskSubnetIds` for ECS tasks, `PrivateAppSubnetIds` for the internal ALB, `PrivateDbSubnetIds` for RDS, `AlbSubnetCidrs` for trusted proxy configuration, and `AlbSecurityGroupId`, `EcsSecurityGroupId`, `MigrationSecurityGroupId`, `DatabaseSecurityGroupId` for their respective components.

## 3. Verified deployment results

The stack was named `tindy-network` and the Console confirmed no template parameters.



![Network stack name](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16-network-stack-name.png)



Project, Environment and Owner tags were configured.



![Network tags](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16a-network-tags.png)



The stack reached **CREATE_COMPLETE** with **Outputs (9)**. The screenshot records completed stack status and new networking references for Database and Runtime. Use the new stack IDs for subsequent deployments.



![Network completed and Outputs](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-18-network-outputs.png)
