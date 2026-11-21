---
title: "CHUẨN BỊ SECRETS CHO CƠ SỞ DỮ LIỆU FRESH"
date: 2026-11-22
weight: 3
chapter: false
pre: "<b> 5.5.3 </b>"
---

AWS CLI đã xác nhận ECS service ở trạng thái ACTIVE với Desired/Running/Pending đều bằng 0 và không có lỗi. VPC Origin ID được đọc trực tiếp từ Outputs báo cáo trạng thái Deployed.

## 1. Khởi tạo các tham chiếu secret

Script `scripts/prepare-runtime-secrets.ps1` hiện có đọc Foundation và Database Outputs, xác minh số lượng task bằng 0 và chuẩn bị application/migration/seed secrets. Các phiên bản AWSCURRENT hiện có được script giữ nguyên. Xác minh endpoint và cấu hình kết nối trước khi chạy các tác vụ DB.



Chạy trong PowerShell 7 từ infrastructure repository.

```powershell
Set-Location 'D:\FPT\Semester 6\OJT\aws\tindy-platform\tindy-infrastructure'
.\scripts\prepare-runtime-secrets.ps1 -AwsProfile tindy-admin | Format-List *
```

## 2. Ghi lại kết quả

Script xuất ra `DBEndpointAddress`, `DBMasterSecretArn`, `MigrationConnectionSecretArn` và `SeedPasswordSecretArn`. Các tham chiếu này cho phép kích hoạt các định nghĩa bootstrap/migration/seed thông qua một lần cập nhật Runtime.



**📸 Chụp ảnh lệnh và bốn trường đầu ra, mốc deploy-39.** Ghi lại các lỗi để chẩn đoán trước khi tiếp tục.



Script này chuẩn bị cấu hình. Database bootstrap, migration và seed sẽ chạy lần lượt dưới dạng các tác vụ ECS chạy một lần sau đó. Script đã hoàn tất thành công và trả về cả bốn trường được hiển thị bên dưới.



[Kết quả chuẩn bị secret cho Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-39-runtime-secrets-prepared.png)



Ảnh chụp màn hình ghi lại endpoint hiện tại và ba ARN cho cấu hình task. Tiếp theo, xác minh các thiết lập kết nối API/migration đối chiếu với Fresh DB, sau đó cập nhật bốn tham số Runtime thông qua một change set trong khi giữ DesiredCount bằng 0.

## 3. Xác minh thiết lập kết nối trước khi bootstrap

Secret JSON đã được đọc và phân tích trong bộ nhớ thông qua AWS CLI, chỉ báo cáo các trường cấu hình và kết quả so sánh. API sử dụng `tindy_api`, migrations sử dụng `tindy_migrator`. Cả hai đều tham chiếu đến endpoint của Fresh DB, cơ sở dữ liệu `tindy`, port `5432`, TLS `VerifyFull` và CA bundle `/app/certs/rds-us-east-1-bundle.crt`. Mật khẩu JSON và mật khẩu trong connection string khớp nhau đối với từng secret.

## 4. Kích hoạt các DB task definition của Runtime

Tạo change set `tindy-runtime-enable-db-tasks` bằng cách sử dụng Runtime template hiện có. Giữ DesiredCount bằng 0 và các tham số khác. Điền `DBEndpointAddress`, `DBMasterSecretArn`, `MigrationConnectionSecretArn` và `SeedPasswordSecretArn` từ đầu ra của script.



Các điều kiện trong template kích hoạt ba cặp execution role/task definition cho bootstrap, migration và seed, dự kiến có sáu action Add. Xem xét change set thực tế trước khi thực thi.



**📸 Chụp ảnh bốn tham số với DesiredCount bằng 0, sau đó là Resource changes.** Việc nhập tham số được ghi lại bên dưới. Việc tạo/thực thi change set và kết quả task vẫn đang chờ xác minh.

### Ảnh chụp màn hình nhập tham số DB task

Đã chọn **Create a change set**, giữ nguyên template hiện có và đặt tên là `tindy-runtime-enable-db-tasks`.



[Menu change set cho DB task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40a-runtime-db-tasks-menu.png)



[Runtime template hiện có](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40b-runtime-db-tasks-existing-template.png)



[Tên change set cho DB task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40c-runtime-db-tasks-changeset-name.png)



Bốn giá trị mới khớp với đầu ra của script. `DBProvisionMode = Fresh`, `DesiredCount = 0` và `AiPdfUploadsEnabled = false`. Các tham chiếu image, Edge, networking và Integrations khớp với cấu hình Runtime ban đầu.



[Đầu vào Image và Edge](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40d-runtime-db-tasks-image-edge.png)



[Đầu vào Database và DesiredCount](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40-runtime-db-task-parameters.png)



[Migration secret và đầu vào Integrations](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40e-runtime-db-tasks-migration.png)



[Seed secret và networking](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40f-runtime-db-tasks-seed-network.png)



Nhấn Next, giữ nguyên các tùy chọn và xác nhận IAM capabilities, sau đó tạo change set. **📸 Chụp ảnh Resource changes tại CREATE_COMPLETE và AVAILABLE**, dự kiến có sáu action Add. Việc tạo change set đã thành công; xem kết quả bên dưới.

### Xem xét change set cho DB task

IAM capabilities đã được xác nhận. Change set `tindy-runtime-enable-db-tasks` đạt trạng thái **CREATE_COMPLETE** và **AVAILABLE**.



[Xác nhận IAM cho DB task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-40g-runtime-db-tasks-iam.png)



Sáu action Add tạo `DbBootstrapExecutionRole`/`DbBootstrapTaskDefinition`, `MigrationExecutionRole`/`MigrationTaskDefinition` và `SeedExecutionRole`/`SeedTaskDefinition`.



[Sáu action Add cho DB task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-41-runtime-db-tasks-changeset.png)



Thực thi change set, giữ Use deletion policy nếu được yêu cầu và chờ Runtime đạt UPDATE_COMPLETE. Mở Outputs và ghi lại `DbBootstrapTaskDefinitionArn`, `MigrationTaskDefinitionArn` và `SeedTaskDefinitionArn`.



**📸 Chụp ảnh quá trình cập nhật stack hoàn tất và DB task Outputs.** Lần cập nhật này tạo các task definition. Bootstrap, migrations và seeding chạy tuần tự sau khi cập nhật hoàn tất. Bằng chứng thực thi hoàn tất được ghi lại bên dưới.



Hộp thoại thực thi chọn Roll back all stack resources và Use deletion policy. Thực thi change set và theo dõi Events cho đến khi UPDATE_COMPLETE.



[Xác nhận thực thi change set cho DB task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-41a-runtime-db-tasks-execute-confirm.png)

## 5. Kết quả cập nhật Runtime

Stack đạt trạng thái **UPDATE_COMPLETE** với **Outputs (9)**. Các Outputs mới xác định `tindy-runtime-db-bootstrap:2`, `tindy-runtime-migration:5` và `tindy-runtime-seed:5`.



[Cập nhật Runtime hoàn tất và DB task Outputs](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.3/deploy-43-runtime-db-task-outputs.png)



Các định nghĩa đã sẵn sàng. Chạy bootstrap, migrations và seed theo thứ tự, xác minh exit code 0 và log hoàn tất trước khi chạy task tiếp theo.
