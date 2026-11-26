
---
title: "Worklog Week 7"
date: 2026-10-26
weight: 7
chapter: false
pre: "<b>1.7. </b>"
---

# Introduction to Data Backup and Application Security on AWS

### Weekly Objectives

- Understand AWS Backup and how to back up data on AWS.
- Become familiar with Backup Vaults, Backup Plans, and data recovery.
- Learn about AWS WAF and how it protects web applications.
- Explore how to manage backups and control access to web applications.

---

### Weekly Activities

| Day | Activities | Start Date | Completion Date | References |
|---|---|---|---|---|
| 2 | - Learn what AWS Backup is and understand its purpose.<br>- Study Backup Vaults, Backup Plans, and Recovery Points.<br>- Explore AWS resources that support backup, such as EC2, EBS, and RDS. | 26/10/2026 | 26/10/2026 | [AWS Backup Workshop](https://000013.awsfcaj.com/)<br>[AWS Backup Documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) |
| 3 | - Review AWS Backup tutorials covering:<br>&emsp;+ Creating a Backup Vault.<br>&emsp;+ Creating a Backup Plan.<br>&emsp;+ Selecting resources for backup.<br>&emsp;+ Configuring backup schedules.<br>- Learn how to monitor backup job status. | 27/10/2026 | 27/10/2026 | [AWS Backup Workshop](https://000013.awsfcaj.com/) |
| 4 | - Learn how to restore data from backups.<br>- Become familiar with Recovery Points and Restore Jobs.<br>- Learn how to check backups and manage data retention periods. | 28/10/2026 | 28/10/2026 | [AWS Backup Documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)<br>[AWS Backup Workshop](https://000013.awsfcaj.com/) |
| 5 | - Learn what AWS WAF is and understand its purpose.<br>- Study Web ACLs and rules used to protect web applications.<br>- Learn how AWS WAF inspects HTTP and HTTPS requests. | 29/10/2026 | 29/10/2026 | [AWS WAF Workshop](https://000026.awsfcaj.com/)<br>[AWS WAF Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html) |
| 6 | - Review tutorials on creating Web ACLs and configuring security rules.<br>- Learn about AWS WAF rule actions:<br>&emsp;+ Allow.<br>&emsp;+ Block.<br>&emsp;+ Count.<br>- Explore how to monitor blocked web requests.<br>- Take notes and summarize the knowledge gained during the week. | 30/10/2026 | 30/10/2026 | [AWS WAF Workshop](https://000026.awsfcaj.com/)<br>[AWS WAF Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html) |

---

### Learning Outcomes

- Understood that AWS Backup is used to centrally manage data backup and recovery on AWS.
- Became familiar with the main AWS Backup components:
  - **Backup Vault:** A storage location used to organize and store backups.
  - **Backup Plan:** Defines backup schedules, rules, and retention settings.
  - **Recovery Point:** A backup representing the state of a resource at a specific point in time.
- Learned the steps for creating Backup Plans, selecting resources, and monitoring backup jobs through AWS tutorials.
- Understood how data can be restored from backups when needed.
- Learned that AWS WAF helps protect web applications by inspecting and filtering incoming web requests.
- Became familiar with Web ACLs and rule actions such as Allow, Block, and Count.
- Gained a better understanding of how data backup and web request filtering contribute to system security on AWS.
