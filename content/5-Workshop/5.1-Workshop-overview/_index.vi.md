
# Tổng quan kiến trúc Tindy

Tindy là ứng dụng web giúp người dùng tìm kiếm dự án, kết nối với đồng đội, quản lý hồ sơ cá nhân, ứng tuyển vào dự án và trao đổi với các thành viên khác. Trong workshop này, hạ tầng AWS cho frontend, backend, cơ sở dữ liệu và hệ thống quét tệp PDF được triển khai thông qua sáu mẫu CloudFormation.

## Mục tiêu

Workshop tập trung vào việc triển khai các CloudFormation stack theo đúng thứ tự phụ thuộc và sử dụng Outputs của các stack trước để cấu hình cho các stack tiếp theo.

Quá trình triển khai bao gồm tạo cơ sở dữ liệu mới, thực hiện các tác vụ bootstrap, migration và seed dữ liệu, sau đó kiểm tra hoạt động của ứng dụng thông qua CloudFront.

Dữ liệu mẫu được sử dụng để minh họa các chức năng đăng nhập, tìm kiếm dự án, ứng tuyển và đánh giá.

## Kiến trúc hệ thống

Sơ đồ kiến trúc thể hiện cách tổ chức và kết nối giữa các dịch vụ AWS và những thành phần chính của ứng dụng Tindy.

[ Sơ đồ kiến trúc bổ sung của Tindy](/my_aws_workshop/static/images/5-Workshop/5.1-Workshop-overview/tindy-architecture-supplement.png)

| **Thành phần** | **Vai trò** |
| :--- | :--- |
| CloudFront và Frontend S3 | Phân phối giao diện frontend qua HTTPS, sử dụng Origin Access Control để quản lý quyền truy cập vào S3 bucket. |
| CloudFront VPC Origin và Internal ALB | Chuyển tiếp các yêu cầu backend từ CloudFront đến ECS API. |
| ECS Fargate | Chạy API và các tác vụ chuẩn bị cơ sở dữ liệu được thực hiện một lần. |
| RDS PostgreSQL | Lưu trữ dữ liệu ứng dụng trong các private database subnet, sử dụng kết nối TLS. |
| Media S3 | Lưu trữ các tệp được tải lên theo từng prefix của ứng dụng. |
| GuardDuty, EventBridge và SQS | Quét các tệp PDF và chuyển kết quả đến API worker, sử dụng Dead Letter Queue (DLQ) để xử lý các trường hợp thất bại. |
| Secrets Manager và SSM | Quản lý thông tin bí mật của ứng dụng và các Data Protection key. |
| ECR và GitHub OIDC | Lưu trữ container image của backend và hỗ trợ GitHub Actions xác thực với AWS. |
| CloudWatch và SNS | Thu thập log, giám sát cảnh báo và gửi thông báo qua email. |

## Các mẫu CloudFormation

Hệ thống sử dụng sáu mẫu CloudFormation. Mỗi mẫu đảm nhiệm việc triển khai và cấu hình một nhóm tài nguyên AWS cụ thể.

| **Template** | **Chức năng** |
| :--- | :--- |
| `foundation.yaml` | Tạo S3, ECR, secrets, OIDC và các role triển khai/vận hành theo điều kiện. |
| `network.yaml` | Cấu hình VPC, subnet, route và security group. |
| `database.yaml` | Triển khai RDS instance, subnet group và parameter group. |
| `integrations.yaml` | Thiết lập GuardDuty plan, EventBridge rule, SQS/DLQ, SNS và các cảnh báo. |
| `edge.yaml` | Cấu hình CloudFront, Origin Access Control, frontend bucket policy và định tuyến chế độ bảo trì. |
| `runtime.yaml` | Triển khai ECS, internal ALB, VPC Origin, IAM role và task definition. |

## Quy trình triển khai thực tế

Quá trình triển khai được thực hiện theo năm bước chính:

1. **Dọn dẹp môi trường (Mục 5.7):** Xóa hạ tầng đã triển khai trước đó để chuẩn bị cho lần triển khai mới.

2. **Foundation và Network (Mục 5.3):** Import các tài nguyên Foundation được giữ lại, khôi phục template chính và tạo Network stack.

3. **Database và Integrations (Mục 5.4):** Tạo cơ sở dữ liệu mới và triển khai Integrations stack.

4. **Edge và Runtime (Mục 5.5):** Triển khai Edge ở chế độ bảo trì để lấy CloudFront hostname. Tiếp theo, tạo Runtime với `DesiredCount` bằng `0`, chuẩn bị cơ sở dữ liệu, thực hiện seed dữ liệu, kích hoạt API và chuyển Edge sang chế độ hoạt động chính thức.

5. **Kiểm tra ứng dụng (Mục 5.6):** Kiểm tra hệ thống sau khi triển khai và trình diễn các chức năng chính của ứng dụng.

Workshop sử dụng tài khoản AWS hiện có tại khu vực `us-east-1`, với CloudFront hostname mới và bộ dữ liệu demo được tạo lại từ đầu.

Các mục triển khai tiếp theo sẽ ghi lại kết quả kiểm tra thực tế và hình ảnh minh chứng trong quá trình thực hiện.
