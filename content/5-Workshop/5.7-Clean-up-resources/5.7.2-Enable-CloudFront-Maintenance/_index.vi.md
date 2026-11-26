---
title: "KÍCH HOẠT CHẾ ĐỘ BẢO TRÌ CLOUDFRONT"
date: 2026-11-19
weight: 2
chapter: false
pre: "<b> 5.7.2 </b>"
---

## Kích hoạt chế độ bảo trì CloudFront

Tham số `EdgeMode` trong `edge.yaml` kiểm soát backend origin và hành vi API. Chế độ maintenance loại bỏ backend origin và trả về phản hồi bảo trì trước khi Runtime VPC Origin bị xóa.

1. Trên `tindy-edge`, chọn **Stack actions → Create change set for current stack**.
2. Chọn **Standard change set** và **Use existing template**.
3. Đặt tên là `tindy-edge-maintenance`. Đặt `EdgeMode = maintenance`, giữ nguyên `FrontendBucketName`, `AlbDnsName` và `VpcOriginId` hiện tại. Các điều kiện trong template loại bỏ backend origin.

**📸 Chụp ảnh các tham số trước khi tạo change set.**



![Tham số Edge](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03a-edge-maintenance-parameters.png)

4. Giữ nguyên role hiện có, Express mode ở trạng thái tắt, rollback ở trạng thái bật và deployment validations được bật. Tạo change set.

![Tùy chọn Edge](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03b-edge-maintenance-options.png)

5. Chờ `CREATE_COMPLETE` và `AVAILABLE`. Xác nhận chỉ có `EdgeDistribution → Modify → Replacement = False`.

**📸 Chụp ảnh Resource changes trước khi thực thi.**



![Edge change set](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03-edge-maintenance-changeset.png)

6. Thực thi, giữ **Use deletion policy**.

![Xác nhận thực thi Edge](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-03c-edge-maintenance-execute.png)

7. Làm mới Events cho đến khi đạt `UPDATE_COMPLETE`.

![Cập nhật Edge hoàn tất](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-04-edge-maintenance-complete.png)

8. Xác nhận `EdgeMode = maintenance` trong Outputs.

![Outputs chế độ maintenance](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-04a-edge-maintenance-outputs.png)

9. Thay `<cloudfront-hostname>` bằng distribution hostname đã ghi lại và chạy trong PowerShell.

```powershell
curl.exe -i "https://<cloudfront-hostname>/api/health/ready"
```

Phản hồi đã xác minh bao gồm HTTP `503 Service Unavailable`, `Server: CloudFront`, `Cache-Control: no-store` và `{"status":"maintenance"}`.



**📸 Chụp ảnh lệnh, trạng thái HTTP và JSON cùng nhau.**



![Phản hồi maintenance](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.2/cleanup-05-api-maintenance.png)

