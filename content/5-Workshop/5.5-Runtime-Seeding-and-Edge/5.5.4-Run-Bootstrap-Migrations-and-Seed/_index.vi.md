---
title: "CHẠY BOOTSTRAP, MIGRATIONS VÀ SEED"
date: 2026-11-19
weight: 4
chapter: false
pre: "<b> 5.5.4 </b>"
---

Runtime đã đạt trạng thái UPDATE_COMPLETE với đầy đủ cả ba database task definition. Các bước sau đây là hướng dẫn đang chờ bằng chứng thực thi. Giữ API `DesiredCount = 0` và Edge ở chế độ maintenance trong khi chuẩn bị cơ sở dữ liệu.

## Trình tự cơ sở dữ liệu Fresh

| **Task** | **Mục đích** | **Điều kiện hoàn tất** |
| :--- | :--- | :--- |
| `tindy-runtime-db-bootstrap:2` | Sử dụng thông tin xác thực master để tạo các role API/migration, cấu hình quyền schema và quyền mặc định cho các object mới. | Container exit code 0 và `Database bootstrap complete.` trong log. |
| `tindy-runtime-migration:5` | Chạy `migrate-db` với thông tin xác thực migration để tạo/cập nhật schema ứng dụng. | Exit code 0 và log migration đã được xem xét. |
| `tindy-runtime-seed:5` | Chạy `seed-demo --fresh` để tạo dữ liệu trình diễn sau migrations. | Exit code 0 và log seed đã được xem xét. |

Chạy từng task theo thứ tự và xác minh kết quả trước khi tiếp tục.

## 1. Chạy DB bootstrap

Mở **Amazon ECS → Clusters → tindy-runtime → Tasks → Run new task**. Tên các trường trên Console có thể thay đổi tùy theo phiên bản.

| **Trường** | **Giá trị** | **Mục đích** |
| :--- | :--- | :--- |
| Compute / Launch type | FARGATE | Chạy container được định nghĩa bởi task definition. |
| Task definition family | `tindy-runtime-db-bootstrap` | Chuẩn bị các PostgreSQL role và quyền được cấp. |
| Revision | `2` | Revision hiện tại được ghi lại trong Runtime Outputs. |
| Desired tasks | `1` | Khởi chạy một bootstrap task. |
| Platform version | LATEST | Sử dụng nền tảng Fargate hiện tại. |
| VPC | `vpc-0209344341dce92fb` | VPC được tạo bởi Network. |
| Subnets | `subnet-01748b133fa7297ac`, `subnet-0a7df83a7449da864` | Các public task subnet của Network. |
| Security group | `sg-03032e44e0d9f2bf7` | MigrationSecurityGroupId có quyền truy cập cơ sở dữ liệu. |
| Public IP | ENABLED | Cho phép pull image và tải RDS CA qua Internet. |

Chọn security group hiện có bằng ID của nó. Giữ nguyên command và environment của container `db-bootstrap`. Task definition của nó tham chiếu đến thông tin xác thực trong Secrets Manager và sử dụng TLS cho các kết nối RDS.

**📸 Chụp ảnh cấu hình task và Networking trước khi chọn Run/Create.**

## 2. Xác minh bootstrap

Mở task mới, chờ trạng thái **Stopped**, sau đó kiểm tra container `db-bootstrap` có **Exit code 0**. Chỉ riêng trạng thái Stopped không xác nhận thành công.

Mở **Logs** và tìm `Database bootstrap complete.`. Log group là `/ecs/tindy-runtime`, với stream prefix `db-bootstrap`.

**📸 Chụp ảnh trạng thái/exit code của container và log hoàn tất.** Kiểm tra exit code khác 0 hoặc thiếu thông báo hoàn tất trước khi chạy migrations.

## 3. Migrations và seed

Sau khi bootstrap được xác minh, sử dụng cùng cluster và cấu hình Networking để chạy migration revision 5. Kiểm tra exit code và logs trước khi chạy seed revision 5. Các chi tiết bổ sung và ảnh chụp kết quả sẽ được thêm vào khi từng bước được thực hiện.

## Các màn hình cấu hình bootstrap đã ghi lại

### Chọn cluster và xác minh API không có task đang chạy

Danh sách ECS hiển thị `tindy-runtime` với một service và không có task đang chạy. Service `tindy-api` ở trạng thái Active với `0/0 Tasks running`. Đây là trạng thái chuẩn bị dự kiến cho cơ sở dữ liệu Fresh trong khi API vẫn dừng.

[Danh sách ECS cluster](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44a-ecs-clusters.png)

[API service với số task bằng 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44b-runtime-service-zero.png)

Mở **Tasks → Run new task**. Danh sách được chụp đã lọc desired status **Stopped** và không chứa task nào. Sau khi khởi chạy, điều chỉnh bộ lọc để tìm bootstrap task đang chạy hoặc đã dừng.

[Tab Tasks trước bootstrap](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44c-runtime-tasks.png)

### Chi tiết task

Chọn family `tindy-runtime-db-bootstrap`, revision `2` và Desired tasks `1`. Family xác định bootstrap, revision chọn definition được tạo bởi CloudFormation, và Desired tasks kiểm soát số lượng task được khởi chạy trong lần chạy này. Task group có thể để trống.

[Chi tiết bootstrap task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44d-bootstrap-task-details.png)

### Environment và compute

Cluster hiện có là `tindy-runtime`. Trong Compute configuration, chọn **Launch type → Fargate** và platform version **LATEST**. Troubleshooting và Monitoring đang được thu gọn. Giữ cấu hình mặc định của chúng cho lần chạy này.

[Fargate và platform version](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44e-bootstrap-fargate.png)

### Networking

Ảnh chụp màn hình xác nhận VPC `vpc-0209344341dce92fb`, các subnet `tindy-ecs-public-a` và `tindy-ecs-public-b`, security group `sg-03032e44e0d9f2bf7` (`tindy-migration-sg`), và Public IP **Turned on**. Các public subnet với public IP cung cấp kết nối outbound để pull image và tải CA. Migration security group cung cấp nguồn đã cấu hình cho quyền truy cập chuẩn bị cơ sở dữ liệu.

[Networking của bootstrap](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-44f-bootstrap-network.png)

**Tiến độ đã xác minh:** cấu hình trước khi khởi chạy khớp với hướng dẫn. Việc khởi chạy và hoàn tất task chưa có bằng chứng. Tiếp theo, chọn Run/Create, mở task và xác minh exit code cùng logs theo mục 2 ở trên.

### Bootstrap đã được khởi chạy

Task `e5c9c5841c004c6eb10289e9a40be2d9` xuất hiện trong danh sách task. Ảnh chụp ghi lại Last status **Deprovisioning**, Desired status **Stopped**, và Health status **Unknown**. ECS đang giải phóng tài nguyên task. Mở chi tiết container để xác minh exit code và Logs trước khi xác nhận bootstrap thành công hoặc chạy migrations.

[Bootstrap đang deprovisioning](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-45a-bootstrap-deprovisioning.png)

### Bootstrap kết thúc với code 0

Task `e5c9c5841c004c6eb10289e9a40be2d9`, sử dụng `tindy-runtime-db-bootstrap:2`, đạt trạng thái **Stopped**. Chi tiết container `db-bootstrap` xác nhận **Exit code 0**. Thông báo `Essential container in task exited` mô tả việc container thiết yếu kết thúc. Cùng với exit code 0, điều này ghi nhận quá trình hoàn tất thành công của one-off task.

[Bootstrap dừng với exit code 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-45b-bootstrap-exit-zero.png)

Bằng chứng exit code đã được ghi lại. Mở Logs và chụp `Database bootstrap complete.` trước khi tiếp tục migrations. Bằng chứng log chưa được cung cấp tại mốc này.

### Logs xác nhận bootstrap hoàn tất

Container `db-bootstrap` ghi lại cả bốn thông báo bao gồm việc tạo role qua TLS đã xác minh, cấp quyền cho các object được tạo bởi migration role, xác minh API role qua TLS đã xác minh, và `Database bootstrap complete.`. Cùng với exit code 0 trước đó, những thông tin này xác nhận bootstrap hoàn tất thành công.

[Logs hoàn tất bootstrap](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-46-bootstrap-complete-logs.png)

## 4. Chạy migrations

Quay lại **ECS → Clusters → tindy-runtime → Tasks → Run new task**. Chọn family `tindy-runtime-migration`, revision `5`, Desired tasks `1`, launch type **Fargate**, và platform **LATEST**. Giữ nguyên command `migrate-db` và environment của task definition.

Sử dụng VPC `vpc-0209344341dce92fb`, public task subnets `subnet-01748b133fa7297ac` và `subnet-0a7df83a7449da864`, security group `sg-03032e44e0d9f2bf7`, và Public IP **Turned on**. Task này sử dụng migration connection secret đã chuẩn bị để cập nhật schema.

**📸 Chụp ảnh Task details và Networking trước khi chọn Run/Create.** Sau khi khởi chạy, mở task, chờ Stopped và xác minh container `migration` có exit code 0. **📸 Chụp ảnh exit code và logs hoàn tất migration.** Xem xét các lỗi trước khi chạy seed. Giữ API DesiredCount 0 và Edge ở chế độ maintenance.

Việc thực thi migration và seed vẫn chưa được xác minh tại mốc này.

### Các màn hình cấu hình migration đã ghi lại

Trước khi tạo migration task, bootstrap task ở trạng thái Stopped và cluster báo cáo không có task running/pending. Banner Tasks launched vẫn tham chiếu bootstrap task `e5c9c5841c004c6eb10289e9a40be2d9`. Ảnh chụp này ghi lại điểm bắt đầu migration và chưa xác nhận migration đã được khởi chạy.

[Cluster trước migration](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47a-before-migration.png)

**Task details:** family `tindy-runtime-migration`, revision `5`, Desired tasks `1`, với Task group để trống. Definition này chạy `migrate-db` để tạo/cập nhật schema bằng thông tin xác thực migration đã chuẩn bị. Desired tasks ở đây kiểm soát số lượng one-off task được khởi chạy. API service vẫn ở DesiredCount 0.

[Chi tiết migration task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47b-migration-task-details.png)

**Environment:** cluster hiện có `tindy-runtime`, Compute options được đặt thành Launch type, launch type Fargate, và platform version LATEST. Các thiết lập này chọn tài nguyên compute cho migration task.

[Cấu hình Migration Fargate](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47c-migration-fargate.png)

**Networking:** VPC `vpc-0209344341dce92fb`, public subnets `subnet-0a7df83a7449da864` và `subnet-01748b133fa7297ac`, security group hiện có `sg-03032e44e0d9f2bf7` (`tindy-migration-sg`), và Public IP Turned on. Các thiết lập này tái sử dụng cấu hình mạng đã chạy bootstrap thành công để pull image và kết nối RDS theo các quy tắc security group.

[Networking của migration](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-47d-migration-network.png)

Các ảnh chụp xác nhận cấu hình trước khi khởi chạy khớp với hướng dẫn. Chọn Run/Create, mở migration task mới, sau đó kiểm tra exit code và Logs của container `migration`. Kết quả migration vẫn chưa được xác minh tại mốc này.

### Migration hoàn tất

Task `d6839873357f48e2a4cc830dbc480677` sử dụng `tindy-runtime-migration:5` với command `migrate-db` và đạt trạng thái Stopped với container exit code **0**.

[Migration exit code 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-48-migration-exit-zero.png)

Logs xác nhận `Database migration complete. Applied history: 26. Latest migration: 20261112124706_Media_AllowAiPdfQuarantine.`. Command migration đưa ra thông báo hoàn tất sau khi áp dụng migrations thành công và đọc migration history.

Logs cũng báo cáo rằng không thể tải `libgssapi_krb5.so.2`. Thông tin chẩn đoán này không ngăn lần chạy migration hoàn tất, như được chứng minh bằng exit code 0 và thông báo hoàn tất sau đó. Nguyên nhân cơ bản của vấn đề này chưa được xác minh tại mốc này.

[Migration hoàn tất và thông tin chẩn đoán thư viện](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-49-migration-complete-logs.png)

## 5. Chạy demo seed cho cơ sở dữ liệu Fresh

Mở **ECS → Clusters → tindy-runtime → Tasks → Run new task**. Chọn family `tindy-runtime-seed`, revision `5`, Desired tasks `1`, launch type **Fargate**, platform **LATEST**.

Tái sử dụng VPC `vpc-0209344341dce92fb`, public subnets `subnet-01748b133fa7297ac` và `subnet-0a7df83a7449da864`, security group `sg-03032e44e0d9f2bf7`, và Public IP **Turned on**. Giữ nguyên command `seed-demo --fresh` và environment. Definition lấy connection string từ migration secret và mật khẩu demo từ seed password secret.

**📸 Chụp ảnh Task details và Networking trước khi Run/Create.** Sau khi khởi chạy, xác minh container `seed` đạt trạng thái Stopped với exit code **0**, sau đó xem xét seed logs. Một bộ dữ liệu mới được tạo dự kiến sẽ hiển thị `Demo seed completed.`. **📸 Chụp ảnh exit code và Logs.** Việc thực thi seed vẫn chưa được xác minh. Giữ API DesiredCount 0 và Edge ở chế độ maintenance.

### Các màn hình cấu hình seed đã ghi lại

Cluster hiển thị bootstrap và migration task ở trạng thái Stopped, với không có task running/pending. Exit code và logs thành công của chúng đã được ghi lại ở trên. Đây là trạng thái trước khi khởi chạy seed.

[Cluster trước seed](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50a-before-seed.png)

**Task details:** family `tindy-runtime-seed`, revision `5`, Desired tasks `1`, và Task group để trống. Definition thực thi `seed-demo --fresh` để đưa dữ liệu vào cơ sở dữ liệu Fresh sau khi chuẩn bị schema. Số lượng này áp dụng cho lần khởi chạy seed one-off. API service vẫn ở DesiredCount 0.

[Chi tiết seed task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50b-seed-task-details.png)

**Environment:** cluster `tindy-runtime`, Compute options được đặt thành Launch type, launch type Fargate, platform version LATEST. Giữ nguyên command, environment và các tham chiếu secret của task definition.

[Cấu hình Seed Fargate](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50c-seed-fargate.png)

**Networking:** VPC `vpc-0209344341dce92fb`, public subnets `subnet-0a7df83a7449da864` và `subnet-01748b133fa7297ac`, security group hiện có `sg-03032e44e0d9f2bf7` (`tindy-migration-sg`), và Public IP Turned on. Điều này tái sử dụng các thiết lập mạng đã chạy bootstrap và migrations thành công.

[Networking của seed](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-50d-seed-network.png)

Các ảnh chụp xác nhận cấu hình trước khi khởi chạy khớp với hướng dẫn. Chọn Run/Create, mở seed task mới và xác minh container `seed` kết thúc với code 0 cùng logs hoàn tất. Việc khởi chạy và hoàn tất seed vẫn chưa được xác minh tại mốc này.

### Seed hoàn tất

Task `1e49f952ecc14f6eaed0dfe72a6144b5` sử dụng `tindy-runtime-seed:5` với command `seed-demo --fresh`. Task đạt trạng thái Stopped với container `seed` báo cáo **Exit code 0**.

[Seed exit code 0](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-51-seed-exit-zero.png)

Logs xác nhận `Demo seed completed.`. Các dòng SQL INSERT hiển thị trong ảnh chụp ghi lại việc tạo dữ liệu. Exit code 0 và thông báo hoàn tất xác nhận quá trình thực thi seed thành công. Số lượng bản ghi độc lập và các luồng web vẫn chưa được xác minh tại mốc này.

[Demo seed hoàn tất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.4/deploy-52-seed-complete-logs.png)

| **Task** | **Bằng chứng hoàn tất** |
| :--- | :--- |
| Bootstrap revision 2 | Exit code 0 và `Database bootstrap complete.` |
| Migration revision 5 | Exit code 0, 26 migrations và `Database migration complete.` |
| Seed revision 5 | Exit code 0 và `Demo seed completed.` |

Tiếp theo, xác định CloudFront VPC Origin service-managed security group trong VPC chính xác, sau đó cập nhật Runtime thông qua một change set bằng cách sử dụng `CloudFrontServiceSecurityGroupId` hiện tại và `DesiredCount = 1`. Việc thực thi API và trạng thái sức khỏe vẫn chưa được xác minh. Edge vẫn ở chế độ maintenance.
