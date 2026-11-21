---
title: "TẠO CƠ SỞ DỮ LIỆU FRESH"
date: 2026-11-22
weight: 1
chapter: false
pre: "<b> 5.4.1 </b>"
---

`database.yaml` tạo một RDS PostgreSQL instance, một subnet group sử dụng hai private DB subnet và một parameter group yêu cầu TLS. Việc khởi tạo Fresh tạo cơ sở dữ liệu `tindy` với master user `tindy_admin`, trong đó RDS quản lý master password trong Secrets Manager. AWS CLI đã xác nhận PostgreSQL `17.11` có sẵn tại `us-east-1` trước bước này.

## Tạo stack

1. Chọn **CloudFormation → Create stack → With new resources (standard)** tại N. Virginia và tải lên `database.yaml` từ infrastructure repository.

**📸 Chụp ảnh tên tệp đã chọn.**



[Tải lên database.yaml](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-19-database-template.png)

2. Đặt tên stack là `tindy-database`. Đặt `DBProvisionMode = Fresh`, để trống `DBSnapshotIdentifier` và giữ `EnableDeletionProtection = true`.
3. Chọn `DatabaseSecurityGroupId` và cả hai `PrivateDbSubnetIds` bằng cách sử dụng Outputs từ Network stack mới được tạo.

**📸 Chụp ảnh tên stack và tất cả các tham số.**



[Tham số cơ sở dữ liệu Fresh](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20-database-fresh-parameters.png)

4. Thêm các tag `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Giữ Express mode ở trạng thái tắt và rollback ở trạng thái bật, xem xét và tạo stack.
5. Chờ đến khi đạt `CREATE_COMPLETE`, sau đó ghi lại Outputs bao gồm endpoint, port, provision mode và master secret ARN nếu được xuất cho Fresh.

**📸 Chụp ảnh trạng thái stack đã hoàn tất và Outputs.**



Bootstrap, migrations và seed dữ liệu demo được thực hiện trong quá trình thiết lập Runtime trước khi bật API. Xác minh và cập nhật API connection secret được giữ lại cho DB mới. Các tham số đã xác minh cho thấy stack `tindy-database`, chế độ khởi tạo Fresh, snapshot identifier để trống và deletion protection được bật. Security group và cả hai DB subnet đã chọn khớp với Network Outputs mới. Quá trình tạo cơ sở dữ liệu đã hoàn tất, với bằng chứng CREATE_COMPLETE và Outputs bên dưới.

## Kết quả triển khai đã xác minh

Các tag, Express mode ở trạng thái tắt, rollback và **Use deletion policy** đã được cấu hình trước khi xem xét tên stack và template.



[Tags của Database](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20a-database-tags.png)



[Tùy chọn Database](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20b-database-options.png)



[Xem xét Database](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-20c-database-review.png)



Stack `tindy-database` đạt trạng thái **CREATE_COMPLETE** với **Outputs (5)**, provision mode `Fresh`, port `5432` và identifier `tindy-database-postgres`. `DBEndpointAddress` và `DBMasterSecretArn` có sẵn cho quá trình bootstrap tiếp theo.



**📸 Ảnh Outputs cũng ghi lại trạng thái stack đã hoàn tất.**



[Cơ sở dữ liệu Fresh hoàn tất và Outputs](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.1/deploy-22-database-outputs.png)



RDS-managed master secret hỗ trợ bootstrap. API sử dụng thông tin xác thực riêng thông qua application connection secret, được chuẩn bị trong quá trình thiết lập Runtime. Tiếp theo là triển khai Integrations.
