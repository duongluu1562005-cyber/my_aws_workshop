
---
title: "Worklog Week 9"
date: 2026-11-09
weight: 9
chapter: false
pre: "<b>1.9. </b>"
---

# Introduction to Deployment and Automation with AWS CloudFormation

### Weekly Objectives

- Understand AWS CloudFormation and how it manages AWS resources.
- Become familiar with Infrastructure as Code (IaC).
- Learn about CloudFormation Templates and Stacks.
- Explore how to create, update, and delete AWS resources using CloudFormation.

---

### Weekly Activities

| Day | Activities | Start Date | Completion Date | References |
|---|---|---|---|---|
| 2 | - Learn what AWS CloudFormation is and understand its purpose.<br>- Study Infrastructure as Code (IaC).<br>- Explore the benefits of creating and managing multiple AWS resources using a single Template. | 09/11/2026 | 09/11/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/)<br>[AWS CloudFormation Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| 3 | - Learn about CloudFormation Templates and Stacks.<br>- Become familiar with writing Templates using YAML or JSON.<br>- Study the basic sections of a Template:<br>&emsp;+ Parameters.<br>&emsp;+ Resources.<br>&emsp;+ Outputs. | 10/11/2026 | 10/11/2026 | [CloudFormation Template Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/template-anatomy.html)<br>[AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| 4 | - Review AWS CloudFormation tutorials covering:<br>&emsp;+ Preparing a Template.<br>&emsp;+ Creating a Stack.<br>&emsp;+ Checking Stack status.<br>&emsp;+ Viewing the created AWS resources.<br>- Understand how CloudFormation uses Templates to provision AWS resources. | 11/11/2026 | 11/11/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/)<br>[CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| 5 | - Learn how to update CloudFormation Stacks when Templates are modified.<br>- Become familiar with Change Sets and how to review proposed changes before updating a Stack.<br>- Learn how to check Stack status during deployment. | 12/11/2026 | 12/11/2026 | [AWS CloudFormation Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| 6 | - Learn how to delete CloudFormation Stacks when they are no longer needed.<br>- Explore how CloudFormation manages resource deletion and how some resources can be retained based on their configurations. | 13/11/2026 | 13/11/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/)<br>[AWS CloudFormation Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |

---

### Learning Outcomes

- Understood that AWS CloudFormation helps create and manage AWS resources using configuration files.
- Learned that Infrastructure as Code (IaC) is an approach to managing infrastructure through configuration code instead of manually creating individual resources.
- Became familiar with the main CloudFormation components:
  - **Template:** A YAML or JSON file that defines AWS resources and their configurations.
  - **Stack:** A collection of AWS resources managed together by CloudFormation.
  - **Parameters:** Input values that can be provided when creating or updating a Stack.
  - **Resources:** Defines the AWS resources to be created and managed.
  - **Outputs:** Provides information or values from the deployed Stack.
- Learned the steps for preparing Templates, creating Stacks, and checking Stack status through AWS tutorials.
- Understood how Stacks can be updated when Templates change and how Change Sets help review proposed updates.
- Learned how to delete Stacks and check the status of associated resources when they are no longer needed.
- Understood that AWS CloudFormation makes AWS resource deployment and management more consistent and easier to maintain.
