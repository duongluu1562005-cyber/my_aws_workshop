
# Database and Integrations

## Tổng quan

Sau khi hoàn tất Foundation và Network, bước tiếp theo là triển khai cơ sở dữ liệu và các dịch vụ tích hợp cho ứng dụng Tindy.

Phần này gồm hai thành phần chính:

- **Database:** Triển khai Amazon RDS for PostgreSQL để lưu trữ dữ liệu của ứng dụng, sử dụng hệ thống mạng đã tạo ở phần 5.3.
- **Integrations:** Cấu hình tính năng quét mã độc cho tệp PDF được tải lên S3, chuyển kết quả quét đến API worker và thiết lập cảnh báo vận hành.

Trong lần triển khai này, hệ thống sử dụng **cơ sở dữ liệu mới (Fresh Database)**. Việc khởi tạo schema, chạy migrations và thêm dữ liệu demo sẽ được thực hiện ở phần 5.5, sau khi RDS hoàn tất.

## 5.4.1 — Tạo cơ sở dữ liệu mới

Template `database.yaml` định nghĩa các tài nguyên cần thiết để triển khai PostgreSQL trên Amazon RDS.

### Các tài nguyên và cấu hình chính

| Tài nguyên hoặc cấu hình | Chức năng |
|---|---|
| DB Subnet Group | Đặt RDS trong các private database subnet được tạo từ Network. |
| PostgreSQL Parameter Group | Bật `rds.force_ssl` để yêu cầu kết nối TLS. |
| RDS Instance | Lưu trữ dữ liệu Tindy bằng PostgreSQL 17, sử dụng `db.t4g.micro` và cấu hình Single-AZ. |
| RDS-managed Master Secret | Lưu thông tin đăng nhập quản trị để bootstrap task sử dụng. |
| Deletion Protection và Snapshot Policy | Bảo vệ cơ sở dữ liệu khỏi việc xóa và yêu cầu tạo snapshot theo chính sách của template. |

### Cấu hình triển khai

Trong quá trình tạo Database stack, sử dụng các thiết lập:

| Tham số | Giá trị |
|---|---|
| `DBProvisionMode` | `Fresh` |
| Snapshot Identifier | Để trống |
| Deletion Protection | Bật |
| Database Name | `tindy` |
| Master Username | `tindy_admin` |

Với chế độ `Fresh`, RDS tạo một cơ sở dữ liệu mới thay vì khôi phục từ snapshot.

Tài khoản `tindy_admin` được dùng cho công việc quản trị và khởi tạo cơ sở dữ liệu. Backend API sẽ sử dụng một tài khoản riêng được chuẩn bị trong các bước tiếp theo.

### Kết quả triển khai

Database stack đã được tạo thành công với trạng thái:

`CREATE_COMPLETE`

CloudFormation cung cấp **5 Outputs**, bao gồm:

- `DBEndpointAddress`: Địa chỉ endpoint của RDS.
- Database Port: Cổng kết nối PostgreSQL.
- Database Identifier: Mã định danh của RDS instance.
- `DBMasterSecretArn`: ARN của secret chứa thông tin đăng nhập quản trị.
- `DBProvisionMode`: Chế độ triển khai cơ sở dữ liệu.

Các Outputs này sẽ được sử dụng khi cấu hình Runtime và thực hiện bootstrap database.

Việc tạo schema, chạy migrations và thêm dữ liệu demo sẽ được thực hiện ở **phần 5.5 — Runtime**.

## 5.4.2 — Triển khai Integrations

Sau khi tạo Database, tiếp tục triển khai các dịch vụ tích hợp bằng template `integrations.yaml`.

Stack này tập trung vào việc quét mã độc cho tệp PDF, chuyển kết quả đến backend và gửi cảnh báo khi xảy ra sự cố.

### Các thành phần chính

| Thành phần | Chức năng |
|---|---|
| GuardDuty Malware Protection | Quét các đối tượng mới trong hai thư mục `certificates/pdf/` và `ai-input/pdf/` thuộc media bucket. |
| Object Tagging | Gắn thẻ kết quả quét lên đối tượng S3 để hỗ trợ kiểm soát truy cập. |
| Amazon EventBridge | Chuyển sự kiện kết quả quét đến hàng đợi. |
| Amazon SQS và Queue Policy | Tiếp nhận thông báo để API worker xử lý. |
| Dead-letter Queue và CloudWatch Alarm | Lưu những thông báo xử lý thất bại theo redrive policy và phát hiện tình trạng tồn đọng. |
| Amazon SNS | Gửi thông báo cảnh báo đến email đã xác nhận. |
| ALB/API Alarms | Theo dõi các chỉ số ALB và API sau khi Runtime được triển khai. |

### Cấu hình triển khai

Integrations sử dụng media bucket đã được tạo trong Foundation.

GuardDuty Malware Protection được cấu hình để theo dõi hai tiền tố S3:

- `certificates/pdf/`
- `ai-input/pdf/`

Khi có tệp mới thuộc phạm vi được bảo vệ, GuardDuty thực hiện quét và ghi nhận kết quả.

Thông tin kết quả được chuyển qua EventBridge đến SQS để API worker xử lý.

Ở lần tạo stack đầu tiên, hai tham số metric dimension của ALB được để trống vì Runtime chưa được triển khai.

Sau khi Runtime có ALB và target group mới, Integrations sẽ được cập nhật để cấu hình các cảnh báo tương ứng.

### Kiểm tra sau triển khai

Sau khi tạo Integrations stack, thực hiện kiểm tra:

1. Xác nhận CloudFormation đạt trạng thái `CREATE_COMPLETE`.
2. Kiểm tra GuardDuty Malware Protection plan ở trạng thái **Active**.
3. Xác minh tính năng gắn thẻ kết quả quét đã được bật.
4. Kiểm tra hai tiền tố PDF đã được cấu hình chính xác.
5. Xác nhận email subscription của Amazon SNS.

### Kết quả triển khai

Integrations stack đã được tạo thành công với trạng thái:

`CREATE_COMPLETE`

CloudFormation cung cấp **7 Outputs**.

Các kết quả đã được xác minh gồm:

- SNS email subscription đã được xác nhận.
- GuardDuty Malware Protection plan hoạt động ở trạng thái Active.
- Tính năng gắn thẻ kết quả quét đã được bật.
- Hai tiền tố PDF đã được cấu hình đúng.

Việc kiểm thử tải tệp PDF thông qua ứng dụng sẽ được thực hiện sau khi API và database schema sẵn sàng.

## Thông tin kết nối giữa các Stack

Các stack sử dụng Outputs của nhau để triển khai đúng tài nguyên và cấu hình.

| Thành phần kết nối | Thông tin sử dụng |
|---|---|
| Network → Database | `PrivateDbSubnetIds`, `DatabaseSecurityGroupId` |
| Foundation → Integrations | `MediaBucketName` |
| Database → Runtime | `DBEndpointAddress`, `DBMasterSecretArn`, `DBProvisionMode` |
| Integrations → Runtime | `GuardDutyProtectionPlanArn`, `GuardDutyQueueUrl` |
| Runtime → Integrations | Thông tin ALB và target group mới để cấu hình cảnh báo |

Việc sử dụng các Outputs giúp các stack liên kết với nhau mà không cần nhập thủ công các thông tin định danh tài nguyên.

## Tổng kết triển khai

Cả Database và Integrations đều đã được triển khai thành công.

| Thành phần | Kết quả |
|---|---|
| Database | `CREATE_COMPLETE` |
| Chế độ Database | `Fresh` |
| Database Outputs | 5 |
| Integrations | `CREATE_COMPLETE` |
| Integrations Outputs | 7 |
| SNS Subscription | Đã xác nhận |
| GuardDuty Protection Plan | Active |
| Object Tagging | Đã bật |
| PDF Prefixes | Đã xác minh cả hai |

Database đã sẵn sàng cho quá trình khởi tạo schema và dữ liệu ứng dụng. Đồng thời, Integrations đã cung cấp hạ tầng cần thiết để xử lý kết quả quét PDF và gửi cảnh báo.

**Bước tiếp theo là triển khai Runtime, chạy migrations và chuẩn bị dữ liệu demo cho Tindy.**

## Hướng dẫn triển khai chi tiết

Các bước thực hiện và hình ảnh minh chứng được trình bày trong hai phần:

| Hướng dẫn | Nội dung |
|---|---|
| [5.4.1 — Tạo cơ sở dữ liệu mới](/my_aws_workshop/5-workshop/5.4-database-integrations/5.4.1-database/) | Cấu hình Fresh Database, Network, Options và kiểm tra RDS Outputs. |
| [5.4.2 — Triển khai Integrations](/my_aws_workshop/5-workshop/5.4-database-integrations/5.4.2-integrations/) | Cấu hình quét PDF, xác nhận SNS và kiểm tra GuardDuty Protection Plan. |
