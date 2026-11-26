
---
title: "Worklog Week 4"
date: 2026-10-05
weight: 4
chapter: false
pre: "<b>1.4. </b>"
---

# Week 4: Introduction to Cloud Networking with Amazon VPC

### Weekly Objectives

- Understand Amazon VPC and how networking is managed on AWS.
- Become familiar with networking components such as Subnets, Route Tables, and Internet Gateways.
- Learn how Security Groups and Network ACLs are used to protect AWS resources.
- Explore how to create a VPC and connect EC2 Instances within an AWS network.

---

### Weekly Activities

| Day | Activities | Start Date | Completion Date | References |
|---|---|---|---|---|
| 2 | - Learn what Amazon VPC is and understand its purpose.<br>- Study CIDR and how IP address ranges are allocated within a VPC.<br>- Understand the differences between Public Subnets and Private Subnets. | 05/10/2026 | 05/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/)<br>[Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) |
| 3 | - Learn about Route Tables and how network traffic is routed.<br>- Study Internet Gateways and NAT Gateways.<br>- Explore how resources within a VPC connect to the Internet. | 06/10/2026 | 06/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| 4 | - Learn about Security Groups and Network Access Control Lists (Network ACLs).<br>- Study how to configure Inbound Rules and Outbound Rules.<br>- Compare how Security Groups and Network ACLs control network traffic. | 07/10/2026 | 07/10/2026 | [AWS VPC Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)<br>[Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| 5 | - Review AWS VPC tutorials covering:<br>&emsp;+ Creating a VPC.<br>&emsp;+ Creating Public and Private Subnets.<br>&emsp;+ Creating an Internet Gateway.<br>&emsp;+ Configuring Route Tables.<br>&emsp;+ Creating a Security Group. | 08/10/2026 | 08/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| 6 | - Learn how to launch an EC2 Instance within a VPC.<br>- Review tutorials on checking connectivity to an EC2 Instance.<br>- Learn how to delete AWS resources after practical exercises to avoid unnecessary costs.<br>- Review the VPC concepts studied during the week. | 09/10/2026 | 09/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |

---

### Learning Outcomes

- Understood that Amazon VPC is used to create and manage isolated virtual networks on AWS.
- Became familiar with the main VPC components:
  - **Subnet:** Divides a VPC IP address range into smaller network segments.
  - **Route Table:** Defines routes that determine where network traffic is directed.
  - **Internet Gateway:** Enables communication between resources in a VPC and the Internet when properly configured.
  - **NAT Gateway:** Allows resources in Private Subnets to initiate outbound connections to the Internet without accepting unsolicited inbound connections from the Internet.
- Understood the differences between Public Subnets and Private Subnets.
- Learned how Security Groups and Network ACLs help control network access.
- Learned the steps for creating a VPC, Subnets, Internet Gateways, and Route Tables through AWS tutorials.
- Understood how EC2 Instances communicate with other resources within a VPC.
