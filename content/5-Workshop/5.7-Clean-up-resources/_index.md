---
title: "CLEAN UP RESOURCES"
date: 2026-11-19
weight: 7
chapter: false
pre: "<b> 5.7. </b>"
---

This section records cleanup of the existing Tindy environment through CloudFormation in **N. Virginia (`us-east-1`)**. Cleanup precedes redeployment of six stacks, a **Fresh** database, bootstrap, migrations and demo seeding. The web demonstration will use a new CloudFront hostname.

## Verified progress

| **Component** | **Recorded state**                                                                                             |
| :------------ | :------------------------------------------------------------------------------------------------------------- |
| Edge          | Maintenance verified, termination protection disabled and stack deletion confirmed as `DELETE_COMPLETE`.       |
| Runtime       | ALB ingress switched to the CloudFront prefix list. Stack deletion completed.                                  |
| Database      | Deletion protection disabled and stack deletion verified as `DELETE_COMPLETE`.                                 |
| Integrations  | Stack and displayed resources report `DELETE_COMPLETE`.                                                        |
| Network       | Termination protection disabled and deletion verified as `DELETE_COMPLETE`.                                    |
| Foundation    | Mapping exported and stack deletion verified as `DELETE_COMPLETE`. Retained resources report `DELETE_SKIPPED`. |


## Practical guides

| **Page** | **Procedure and evidence** |
| :--- | :--- |
| [5.7.1 Review Infrastructure Before Cleanup](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.1-review-infrastructure-before-cleanup/) | Review deployed infrastructure and identify resources before cleanup. |
| [5.7.2 Enable CloudFront Maintenance](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.2-enable-cloudfront-maintenance/) | Enable maintenance mode through CloudFront before deleting resources. |
| [5.7.3 Prepare and Delete Runtime](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.3-prepare-and-delete-runtime/) | Prepare the Runtime stack for cleanup and verify stack deletion. |
| [5.7.4 Disable Protection and Delete Database](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.4-disable-protection-and-delete-database/) | Disable database deletion protection and verify database stack removal. |
| [5.7.5 Review and Delete Integrations](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.5-review-and-delete-integrations/) | Review integration resources and verify stack deletion. |
| [5.7.6 Disable Protection and Delete Edge](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.6-disable-protection-and-delete-edge/) | Disable protection where required and delete the Edge stack. |
| [5.7.7 Disable Protection and Delete Network](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.7-disable-protection-and-delete-network/) | Remove network protection settings and verify Network stack deletion. |
| [5.7.8 Export Mappings and Delete Foundation](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.8-export-mappings-and-delete-foundation/) | Export Foundation configuration and mappings before deleting the stack. |
| [5.7.9 Cleanup Results and Redeployment Preparation](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.9-cleanup-results-and-redeployment-preparation/) | Review cleanup evidence, retained resources and redeployment preparation. |
