---
title: "CLEAN UP RESOURCES"
date: 2026-11-22
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

## Cleanup steps

| **Guide page** |
| :--- |
| [5.7.1 Review infrastructure before cleanup](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.1-inventory/) |
| [5.7.2 Enable CloudFront maintenance](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.2-maintenance/) |
| [5.7.3 Prepare and delete Runtime](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.3-runtime/) |
| [5.7.4 Disable protection and delete Database](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.4-database/) |
| [5.7.5 Review and delete Integrations](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.5-integrations/) |
| [5.7.6 Disable protection and delete Edge](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.6-edge/) |
| [5.7.7 Disable protection and delete Network](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.7-network/) |
| [5.7.8 Export mappings and delete Foundation](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.8-foundation/) |
| [5.7.9 Cleanup results and redeployment preparation](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.9-results/) |
