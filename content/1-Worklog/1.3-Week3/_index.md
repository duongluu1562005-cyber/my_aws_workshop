
---
title: "Worklog Week 3"
date: 2026-09-28
weight: 3
chapter: false
pre: "<b>1.3. </b>"
---

# Introduction to Virtual Servers with Amazon EC2

### Weekly Objectives

- Understand Amazon EC2 and how virtual servers are used on AWS.
- Become familiar with the main EC2 components, including Instances, AMIs, Key Pairs, and Security Groups.
- Learn how to launch, connect to, and manage EC2 Instances.
- Explore data storage using Amazon Elastic Block Store (EBS).

---

### Weekly Activities

| Day | Activities | Start Date | Completion Date | References |
|---|---|---|---|---|
| 2 | - Learn what Amazon EC2 is and understand its purpose.<br>- Study different EC2 Instance Types and how to select suitable configurations.<br>- Learn about Amazon Machine Images (AMIs) and Key Pairs. | 28/09/2026 | 28/09/2026 | [Amazon EC2 Workshop](https://000004.awsfcaj.com/) |
| 3 | - Learn the steps for launching an EC2 Instance through the AWS Management Console.<br>- Study how to select an AMI, Instance Type, and Key Pair.<br>- Learn about Security Groups and how to configure network access to an EC2 Instance. | 29/09/2026 | 29/09/2026 | [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)<br>[Amazon EC2 Workshop](https://000004.awsfcaj.com/) |
| 4 | - Review tutorials on connecting to EC2 Instances using SSH.<br>- Understand Public IP addresses, Private IP addresses, and how Key Pairs are used for authentication.<br>- Become familiar with basic Linux commands when working with EC2 Instances. | 30/09/2026 | 30/09/2026 | [Connect to EC2 using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-to-linux-instance.html) |
| 5 | - Learn how to manage EC2 Instances using the following actions:<br>&emsp;+ Start.<br>&emsp;+ Stop.<br>&emsp;+ Reboot.<br>&emsp;+ Terminate.<br>- Understand the differences between stopping and terminating an EC2 Instance.<br>- Learn how to check EC2 Instance states. | 01/10/2026 | 01/10/2026 | [EC2 Instance Lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html) |
| 6 | - Learn about Amazon EBS and how it provides block storage for EC2 Instances.<br>- Review tutorials on creating and attaching EBS Volumes to EC2 Instances.<br>- Learn about EBS Snapshots and their use in data backup.<br>- Review the EC2 concepts studied during the week. | 02/10/2026 | 02/10/2026 | [Amazon EBS Documentation](https://docs.aws.amazon.com/ebs/)<br>[Amazon EC2 Workshop](https://000004.awsfcaj.com/) |

---

### Learning Outcomes

- Understood that Amazon EC2 is an AWS service used to provision and manage virtual servers in the cloud.
- Became familiar with the main EC2 components:
  - **Instance:** A virtual server running on AWS.
  - **AMI:** A template used to launch EC2 Instances.
  - **Key Pair:** A set of cryptographic keys used for authentication when connecting to EC2 Instances.
  - **Security Group:** A virtual firewall that controls allowed inbound and outbound network traffic.
- Learned the steps for launching an EC2 Instance and selecting suitable configurations through AWS tutorials.
- Understood how to connect to Linux EC2 Instances using SSH and became familiar with basic Linux commands.
- Learned the differences between Start, Stop, Reboot, and Terminate operations.
- Understood that Amazon EBS provides persistent block storage for EC2 Instances and that EBS Snapshots can be used for data backup.
