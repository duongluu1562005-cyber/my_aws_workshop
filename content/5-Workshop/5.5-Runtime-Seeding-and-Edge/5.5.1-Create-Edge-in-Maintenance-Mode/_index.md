---
title: "CREATE EDGE IN MAINTENANCE MODE"
date: 2026-11-19
weight: 1
chapter: false
pre: "<b> 5.5.1 </b>"
---

`edge.yaml` creates the CloudFront distribution, Origin Access Control, frontend bucket policy and maintenance routing function. Create Edge before Runtime to obtain the new hostname for API public host and OAuth configuration.

1. In `us-east-1`, select **CloudFormation → Create stack → With new resources (standard)** and upload `edge.yaml`.

**📸 Capture the selected filename.**



![Uploaded Edge template](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-29-edge-template.png)

2. Name the stack `tindy-edge`. Set `FrontendBucketName` from Foundation Outputs, use `EdgeMode = maintenance`, and leave `VpcOriginId` and `AlbDnsName` empty.

**📸 Capture the stack name and all four parameters.**



![Edge maintenance parameters](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-30-edge-maintenance-parameters.png)

3. Add tags `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Keep Express mode disabled and rollback enabled, review and create the stack.
4. Wait for `CREATE_COMPLETE`, then record `DistributionId`, `DistributionDomainName` and `EdgeMode` from Outputs.

**📸 Capture completed status and Outputs.**

5. Replace `<cloudfront-hostname>` with the new distribution hostname and run in PowerShell.

```powershell
curl.exe -i "https://<cloudfront-hostname>/api/health/ready"
```

Verify HTTP `503` and `{"status":"maintenance"}`. Keep maintenance enabled until Runtime and the database schema are ready.



**📸 Capture the command, HTTP status and JSON.**

## Edge creation progress

The edge.yaml template is selected, with stack name tindy-edge, the Foundation frontend bucket, EdgeMode maintenance and empty AlbDnsName/VpcOriginId. All three operations tags were entered.



![Edge tags](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-30a-edge-tags.png)



Review shows the **Submit** button and termination protection **Deactivated**.



![Edge Review before Submit](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-30b-edge-review-submit.png)



The stack reached **CREATE_COMPLETE**. Outputs confirm distribution `E39MIF3N8DR3D6`, hostname `d1vcn4g2ifl3k8.cloudfront.net` and mode `maintenance`.



![Edge completed and new Outputs](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-32-edge-outputs.png)



Use this hostname for Runtime `CloudFrontPublicHost`. Check maintenance over HTTPS.

```powershell
curl.exe -i https://d1vcn4g2ifl3k8.cloudfront.net/api/health/ready
```

**📸 Capture the command, HTTP 503 and maintenance JSON.** An HTTPS curl check verified HTTP `503 Service Unavailable`, `Server: CloudFront`, `Cache-Control: no-store` and `{"status":"maintenance"}`. The terminal screenshot is recorded below.



![HTTPS 503 maintenance](/my_aws_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-33-edge-maintenance-check.png)
