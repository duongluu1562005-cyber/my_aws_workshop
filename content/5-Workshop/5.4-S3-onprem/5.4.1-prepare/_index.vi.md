
## 5.4.1 — Tạo cơ sở dữ liệu mới (Fresh Database)

### Tổng quan

Sau khi hoàn tất triển khai Network, bước tiếp theo là tạo cơ sở dữ liệu PostgreSQL cho ứng dụng Tindy.

Template `database.yaml` được sử dụng để triển khai Amazon RDS và cấu hình các thành phần mạng, bảo mật cần thiết cho cơ sở dữ liệu.

Các thành phần chính bao gồm:

- **Amazon RDS:** Chạy PostgreSQL với cấu hình Single-AZ.
- **DB Subnet Group:** Đặt cơ sở dữ liệu trong hai private DB subnets được tạo từ Network.
- **PostgreSQL Parameter Group:** Bật `rds.force_ssl` để yêu cầu kết nối được mã hóa bằng TLS.
- **RDS-managed Master Secret:** Lưu trữ mật khẩu quản trị cơ sở dữ liệu an toàn trong AWS Secrets Manager.
- **Deletion Protection:** Giúp ngăn chặn việc vô tình xóa cơ sở dữ liệu.

Trong lần triển khai này, hệ thống sử dụng chế độ **Fresh**, tạo cơ sở dữ liệu mới có tên `tindy` với tài khoản quản trị `tindy_admin`.

Trước khi triển khai, AWS CLI đã xác nhận PostgreSQL phiên bản `17.11` có sẵn tại khu vực `us-east-1`.

Việc chạy migrations và chuẩn bị dữ liệu demo sẽ được thực hiện trong giai đoạn Runtime.

### Bước 1: Tải lên Database Template

Truy cập **AWS Management Console** và chọn khu vực **US East (N. Virginia) — us-east-1**.

Đi đến:

**CloudFormation → Stacks → Create stack → With new resources (standard)**

Tại mục **Specify template**, thực hiện:

1. Chọn **Choose an existing template**.
2. Chọn **Upload a template file**.
3. Tải lên tệp `database.yaml` từ repository hạ tầng.
4. Nhấn **Next** để tiếp tục.

![Tải lên Database template](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-19-database-template.png)

### Bước 2: Cấu hình tham số Fresh Database

Tại trang **Specify stack details**, nhập tên stack:

`tindy-database`

Cấu hình các tham số như sau:

| Tham số | Giá trị |
|---|---|
| `DBProvisionMode` | `Fresh` |
| `DBSnapshotIdentifier` | Để trống |
| `EnableDeletionProtection` | `true` |
| `DatabaseSecurityGroupId` | Lấy từ Outputs của Network |
| `PrivateDbSubnetIds` | Hai private DB subnets từ Outputs của Network |

Chế độ `Fresh` cho phép tạo một cơ sở dữ liệu PostgreSQL mới thay vì khôi phục từ snapshot có sẵn.

Security Group và các subnet được chọn phải khớp với tài nguyên đã tạo trong stack `tindy-network`.

Tính năng Deletion Protection được bật để hạn chế nguy cơ xóa nhầm RDS instance.

![Cấu hình tham số Fresh Database](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20-database-fresh-parameters.png)

### Bước 3: Cấu hình Stack Options

Tiếp tục đến trang cấu hình stack và thêm các tags sau:

| Khóa (Key) | Giá trị (Value) |
|---|---|
| Project | Tindy |
| Environment | tindy |
| Owner | TindyTeam |

Các tags giúp nhận diện và quản lý những tài nguyên cơ sở dữ liệu thuộc dự án Tindy.

![Cấu hình tags cho Database stack](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20a-database-tags.png)

Giữ nguyên các tùy chọn:

- **Express mode:** Tắt.
- **Rollback:** Bật.
- **Deletion policy option:** Chọn Use deletion policy.

![Tùy chọn cấu hình Database stack](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20b-database-options.png)

Kiểm tra lại tên stack, template, tham số và các thiết lập trước khi xác nhận tạo stack.

![Kiểm tra cấu hình Database stack](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-20c-database-review.png)

### Bước 4: Triển khai và kiểm tra Database

Xác nhận tạo stack trên CloudFormation.

CloudFormation bắt đầu triển khai RDS instance, DB Subnet Group và PostgreSQL Parameter Group theo cấu hình trong template.

Mở tab **Events** để theo dõi tiến trình triển khai cho đến khi stack đạt trạng thái:

`CREATE_COMPLETE`

Nếu xảy ra lỗi, kiểm tra **Logical ID** và **Status reason** của tài nguyên gặp sự cố.

Quá trình triển khai đã hoàn tất thành công và CloudFormation hiển thị **5 Outputs**.

![Database triển khai thành công và hiển thị Outputs](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.1/deploy-22-database-outputs.png)

### Bước 5: Kiểm tra Database Outputs

Sau khi stack đạt trạng thái `CREATE_COMPLETE`, mở tab **Outputs**.

Các thông tin cấu hình đã được xác nhận gồm:

| Nội dung | Kết quả |
|---|---|
| Tên stack | `tindy-database` |
| Chế độ triển khai | `Fresh` |
| Phiên bản PostgreSQL | `17.11` |
| Tên cơ sở dữ liệu | `tindy` |
| Tài khoản quản trị | `tindy_admin` |
| Mã định danh cơ sở dữ liệu | `tindy-database-postgres` |
| Cổng kết nối | `5432` |
| Trạng thái stack | `CREATE_COMPLETE` |
| Số lượng Outputs | 5 |

Các Outputs cung cấp địa chỉ endpoint, cổng kết nối, mã định danh cơ sở dữ liệu, ARN của master secret và chế độ triển khai.

Hai thông tin quan trọng cho giai đoạn tiếp theo là:

- **`DBEndpointAddress`:** Địa chỉ PostgreSQL endpoint dùng để kết nối đến cơ sở dữ liệu.
- **`DBMasterSecretArn`:** ARN của secret trong AWS Secrets Manager chứa thông tin đăng nhập quản trị do RDS quản lý.

Những thông tin này sẽ được sử dụng khi khởi tạo cơ sở dữ liệu và cấu hình Runtime.

### Các bước tiếp theo

Hạ tầng cơ sở dữ liệu đã được tạo, nhưng schema và dữ liệu demo của ứng dụng vẫn chưa được khởi tạo.

Trong giai đoạn Runtime, các công việc tiếp theo bao gồm:

1. Thực hiện quy trình bootstrap cơ sở dữ liệu.
2. Chạy database migrations để tạo và cập nhật schema.
3. Thêm dữ liệu demo cần thiết.
4. Kiểm tra và cập nhật application database connection secret đã giữ lại để kết nối đến RDS mới.
5. Cấu hình backend API sử dụng thông tin đăng nhập cơ sở dữ liệu riêng.

RDS-managed master secret được sử dụng cho các tác vụ quản trị và bootstrap. Backend API sử dụng tài khoản riêng thông qua application connection secret.

### Kết quả triển khai

Quá trình tạo Fresh Database đã hoàn tất thành công.

| Nội dung kiểm tra | Kết quả |
|---|---|
| Database stack | `tindy-database` |
| Chế độ triển khai | Cơ sở dữ liệu mới (`Fresh`) |
| Cấu hình mạng | Private DB subnets |
| Deletion Protection | Đã bật |
| Trạng thái stack | `CREATE_COMPLETE` |
| Số lượng Outputs | 5 |
| Schema và dữ liệu demo | Chờ thực hiện trong Runtime |

Cơ sở dữ liệu PostgreSQL đã được triển khai thành công và sẵn sàng cho quá trình khởi tạo dữ liệu ứng dụng.

**Bước tiếp theo là triển khai Integrations, sau đó cấu hình Runtime và chuẩn bị cơ sở dữ liệu cho Tindy.**
