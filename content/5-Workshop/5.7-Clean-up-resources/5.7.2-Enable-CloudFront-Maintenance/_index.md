---
title: "ENABLE CLOUDFRONT MAINTENANCE"
date: 2026-11-22
weight: 2
chapter: false
pre: "<b> 5.7.2 </b>"
---

## Enable CloudFront maintenance

The `EdgeMode` parameter in `edge.yaml` controls the backend origin and API behavior. Maintenance removes the backend origin and returns the maintenance response before the Runtime VPC Origin is removed.

1. On `tindy-edge`, select **Stack actions → Create change set for current stack**.
2. Choose **Standard change set** and **Use existing template**.
3. Name it `tindy-edge-maintenance`. Set `EdgeMode = maintenance`, retaining the current `FrontendBucketName`, `AlbDnsName` and `VpcOriginId`. Template conditions remove the backend origin.

**📸 Capture the parameters before creating the change set.**



[Edge parameters](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03a-edge-maintenance-parameters.png)

4. Keep the existing role, Express mode disabled, rollback enabled and deployment validations enabled. Create the change set.

[Edge options](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03b-edge-maintenance-options.png)

5. Wait for `CREATE_COMPLETE` and `AVAILABLE`. Confirm only `EdgeDistribution → Modify → Replacement = False`.

**📸 Capture Resource changes before execution.**



[Edge change set](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03-edge-maintenance-changeset.png)

6. Execute, keeping **Use deletion policy**.

[Edge execution confirmation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03c-edge-maintenance-execute.png)

7. Refresh Events until `UPDATE_COMPLETE`.

[Edge update completed](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-04-edge-maintenance-complete.png)

8. Confirm `EdgeMode = maintenance` in Outputs.

[Maintenance outputs](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-04a-edge-maintenance-outputs.png)

9. Replace `<cloudfront-hostname>` with the recorded distribution hostname and run in PowerShell.

```powershell
curl.exe -i "https://<cloudfront-hostname>/api/health/ready"
```

The verified response includes HTTP `503 Service Unavailable`, `Server: CloudFront`, `Cache-Control: no-store` and `{"status":"maintenance"}`.



**📸 Capture the command, HTTP status and JSON together.**



[Maintenance response](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-05-api-maintenance.png)

