---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# AWS IAM ACCESS ANALYZER – IDENTIFYING UNUSED ACCESS TO STRENGTHEN CLOUD SECURITY

**This article was prepared to be shared with the AWS Study Group community.**

**Published Post:** Not yet published.

## Article Content

Access management is an important part of securing an AWS environment. Over time, IAM users and roles may accumulate permissions that are no longer necessary, increasing potential security risks.

**AWS IAM Access Analyzer** helps organizations identify unused access and review permissions granted to IAM identities, as well as access to supported AWS resources. These findings support the **Principle of Least Privilege**, which recommends granting users and services only the permissions necessary to perform their tasks.

### Key Takeaways

- **Unused Access Analysis:** Identifies unused IAM roles, access keys, passwords, and permissions within a configured analysis period.
- **Least-Privilege Security:** Helps administrators identify permissions that may be broader than necessary.
- **External Access Findings:** Detects supported resources whose policies allow access from outside a defined zone of trust.
- **Access Findings:** Provides information for administrators to evaluate whether existing access permissions are appropriate.
- **Permission Remediation:** Supports reviewing and modifying IAM policies, removing unused credentials, and investigating inactive IAM roles.
- **Continuous Analysis:** Helps organizations regularly assess access permissions as AWS resources and configurations change.

However, unused access does not necessarily mean that a permission should be removed immediately. Administrators should verify whether it supports infrequent operations, emergency recovery, or other legitimate requirements.

### Practical Applications

AWS IAM Access Analyzer is useful for organizations managing multiple IAM users, IAM roles, and AWS resources.

For example, a development team may discover an inactive IAM role or permissions that have not been used for several months. After reviewing the findings and verifying application requirements, administrators can adjust access policies to reduce unnecessary privileges.

Regular access reviews help organizations maintain better security control as their cloud environments expand.

Additionally, unused access analysis may incur additional charges. Organizations should review AWS pricing before enabling this feature.

## Architecture Diagram

![AWS IAM Access Analyzer Workflow](/my_aws_workshop/images/3_Blog/anh2.png)

*Figure 2: IAM Access Analyzer workflow for reviewing unused permissions and improving access security.*

The security review process can be summarized as follows:

**IAM Users and IAM Roles → IAM Access Analyzer → Review Findings → Verify Access Requirements → Adjust Permissions**

This process helps administrators evaluate potentially unnecessary permissions before making changes to IAM policies.

## References

- [AWS Documentation – IAM Access Analyzer Findings](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-findings.html)
- [AWS Documentation – Understanding IAM Access Analyzer Findings](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-concepts.html)
- [AWS Documentation – Resolving IAM Access Analyzer Findings](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-findings-remediate.html)