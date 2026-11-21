---
title: "TỔNG QUAN KIẾN TRÚC TINDY"
date: 2026-11-22
weight: 1
chapter: false
pre: " <b> 5.1 </b> "
---

Tindy giúp người dùng tìm kiếm dự án và đồng đội, quản lý hồ sơ, ứng tuyển vào các dự án và giao tiếp với các thành viên khác. Workshop này triển khai hạ tầng frontend, backend, cơ sở dữ liệu và quét PDF của ứng dụng trên AWS thông qua sáu mẫu CloudFormation.

## Mục tiêu

Triển khai các stack theo thứ tự phụ thuộc, sử dụng Outputs của các stack làm đầu vào cho các bước tiếp theo, khởi tạo cơ sở dữ liệu Fresh, chạy các tác vụ bootstrap/migration/seed và xác minh ứng dụng thông qua CloudFront. Dữ liệu seed hỗ trợ phần trình diễn đăng nhập, khám phá dự án, ứng tuyển và đánh giá.

## Kiến trúc hệ thống

Sơ đồ thể hiện cách bố trí các nhóm dịch vụ và các thành phần của ứng dụng.



[Sơ đồ kiến trúc Tindy bổ sung](/aws_my_workshop/images/5-Workshop/5.1-Workshop-overview/tindy-architecture-supplement.png)

| **Thành phần** | **Vai trò** |
| :--- | :--- |
| CloudFront và S3 frontend | Phân phối frontend qua HTTPS, sử dụng Origin Access Control để truy cập bucket. |
| CloudFront VPC Origin và ALB nội bộ | Định tuyến các yêu cầu backend từ CloudFront đến ECS API. |
| ECS Fargate | Chạy API và các tác vụ chuẩn bị cơ sở dữ liệu chạy một lần. |
| RDS PostgreSQL | Lưu trữ dữ liệu ứng dụng trong các DB subnet riêng tư với kết nối TLS. |
| Media S3 | Lưu trữ các tệp được tải lên theo các tiền tố của ứng dụng. |
| GuardDuty, EventBridge và SQS | Quét PDF và gửi kết quả đến API worker, với DLQ để xử lý các trường hợp thất bại. |
| Secrets Manager và SSM | Lưu trữ các bí mật ứng dụng và khóa Data Protection. |
| ECR và GitHub OIDC | Lưu trữ các image backend và hỗ trợ GitHub Actions xác thực với AWS. |
| CloudWatch và SNS | Cung cấp nhật ký, cảnh báo và thông báo qua email. |

## Các mẫu CloudFormation

| **Mẫu** | **Phạm vi** |
| :--- | :--- |
| `foundation.yaml` | S3, ECR, secrets, OIDC và các vai trò triển khai/vận hành có điều kiện. |
| `network.yaml` | VPC, subnet, route và security group. |
| `database.yaml` | RDS instance, subnet group và parameter group. |
| `integrations.yaml` | Kế hoạch GuardDuty, quy tắc EventBridge, SQS/DLQ, SNS và cảnh báo. |
| `edge.yaml` | CloudFront, Origin Access Control, chính sách bucket frontend và định tuyến chế độ bảo trì. |
| `runtime.yaml` | ECS, ALB nội bộ, VPC Origin, IAM role và task definition. |

## Trình tự triển khai thực tế

1. Dọn dẹp môi trường hiện có, được ghi lại trong phần 5.7.
2. Import các tài nguyên Foundation được giữ lại, khôi phục mẫu chính và tạo Network trong phần 5.3.
3. Tạo Fresh Database và Integrations trong phần 5.4.
4. Tạo Edge ở chế độ bảo trì để lấy hostname, khởi tạo Runtime với DesiredCount 0, chuẩn bị và seed cơ sở dữ liệu, bật API và chuyển Edge sang chế độ hoạt động trong phần 5.5.
5. Xác minh và trình diễn ứng dụng trong phần 5.6.

Phiên triển khai này sử dụng tài khoản hiện có tại `us-east-1`, một hostname CloudFront mới và dữ liệu demo mới được seed. Các trang triển khai ghi lại kết quả đã xác minh và ảnh chụp màn hình trong quá trình thực hiện.
