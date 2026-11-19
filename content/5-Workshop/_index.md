---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
# TINDY DEPLOYMENT ON AWS USING CLOUDFORMATION

## Overview

This workshop covers the redeployment of **Tindy on AWS using CloudFormation**, including setting up a fresh PostgreSQL database, running migrations, seeding demo data, and verifying the application through CloudFront before recording a web demonstration.

The infrastructure uses six CloudFormation templates: `foundation.yaml`, `network.yaml`, `database.yaml`, `integrations.yaml`, `runtime.yaml`, and `edge.yaml`.

Each section includes the deployed resources, required configurations, stack outputs, verification results, and screenshots.

Since Tindy was previously deployed, the workshop begins by cleaning up the existing infrastructure before redeploying all six stacks in the same AWS environment. The new deployment uses a fresh database, demo data, and a new CloudFront hostname.

The cleanup process, documented in Section 5.7, includes maintenance and deletion of all six stacks, each verified with the **DELETE_COMPLETE** status.

## Content

1. [Architecture overview](5.1-overview/)
2. [Deployment prerequisites](5.2-prerequisites/)
3. [Foundation and Network](5.3-foundation-network/)
4. [Database and Integrations](5.4-database-integrations/)
5. [Runtime, seeding and Edge](5.5-runtime-edge/)
6. Application verification and web demonstration
7. [Clean up resources](5.7-cleanup/)
