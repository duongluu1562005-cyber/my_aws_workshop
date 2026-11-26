---
title: "DATABASE VÀ INTEGRATIONS"
date: 2026-11-19
weight: 4
chapter: false
pre: "<b> 5.4. </b>"
---

Chuẩn bị hệ thống lưu trữ ứng dụng và quét PDF trước khi chạy backend. Database sử dụng hạ tầng mạng từ phần 5.3. Integrations sử dụng media bucket của Foundation và gửi kết quả quét đến API worker.

Phiên triển khai này khởi tạo một cơ sở dữ liệu Fresh. Bootstrap, migrations và seed dữ liệu demo được thực hiện trong quá trình thiết lập Runtime sau khi hoàn tất việc tạo RDS.

## 5.4.1 — Tạo cơ sở dữ liệu Fresh

`database.yaml` định nghĩa ba tài nguyên chính và các thiết lập truy cập cơ sở dữ liệu.

| **Tài nguyên hoặc thiết lập** | **Mục đích** |
| :--- | :--- |
| DB subnet group | Đặt RDS trong các private DB subnet từ Network. |
| PostgreSQL parameter group | Kích hoạt `rds.force_ssl` để yêu cầu TLS. |
| RDS instance | Lưu trữ dữ liệu Tindy bằng PostgreSQL 17, `db.t4g.micro` và cấu hình Single-AZ. |
| RDS-managed master secret | Cung cấp thông tin xác thực quản trị cho tác vụ bootstrap. |
| Deletion protection và snapshot policy | Bảo vệ cơ sở dữ liệu khỏi bị xóa và yêu cầu tạo snapshot theo chính sách của template. |

Sử dụng `DBProvisionMode = Fresh`, để trống snapshot identifier và bật deletion protection. RDS tạo cơ sở dữ liệu `tindy` và master user `tindy_admin`. API sử dụng thông tin xác thực DB riêng được chuẩn bị sau đó.



**Kết quả mong đợi** là `CREATE_COMPLETE` và năm Outputs bao gồm endpoint, port, identifier, master secret ARN và provision mode. Việc chuẩn bị schema và dữ liệu demo được thực hiện tiếp trong phần 5.5.

## 5.4.2 — Triển khai Integrations

`integrations.yaml` cấu hình việc gửi kết quả quét PDF và các thông báo vận hành.

| **Thành phần** | **Mục đích** |
| :--- | :--- |
| GuardDuty Malware Protection plan | Quét các object mới trong `certificates/pdf/` và `ai-input/pdf/` thuộc media bucket. |
| Gắn tag cho các object đã quét | Ghi lại kết quả quét trên các object để hỗ trợ kiểm soát truy cập. |
| EventBridge rule | Gửi các sự kiện kết quả quét đến queue. |
| SQS queue và policy | Nhận các message cho API worker. |
| Dead-letter queue và alarm | Giữ lại các message xử lý thất bại theo redrive policy và phát hiện tình trạng tồn đọng. |
| SNS topic và email subscription | Gửi cảnh báo đến người nhận đã xác nhận. |
| Conditional ALB/API alarms | Sử dụng các metric dimension ALB/target group mới sau khi triển khai Runtime. |

Ban đầu để trống cả hai ALB metric dimension. Sau khi tạo, xác nhận SNS email subscription và xác minh protection plan ở trạng thái Active, việc gắn tag và cả hai tiền tố PDF.

## Đầu vào và đầu ra

| **Phụ thuộc** | **Tham chiếu** |
| :--- | :--- |
| Network → Database | `PrivateDbSubnetIds`, `DatabaseSecurityGroupId`. |
| Foundation → Integrations | `MediaBucketName`. |
| Database → Chuẩn bị Runtime | `DBEndpointAddress`, `DBMasterSecretArn`, `DBProvisionMode`. |
| Integrations → Runtime | `GuardDutyProtectionPlanArn`, `GuardDutyQueueUrl`. |
| Runtime → Cập nhật Integrations | Các metric dimension ALB/target group mới cho cảnh báo. |

## Kết quả đã xác minh

| **Thành phần** | **Trạng thái** |
| :--- | :--- |
| Database | `CREATE_COMPLETE`, chế độ Fresh và năm Outputs. |
| Integrations | `CREATE_COMPLETE`, bảy Outputs. |
| SNS | Subscription đã được xác nhận. |
| GuardDuty | Protection plan ở trạng thái Active, đã bật gắn tag và xác minh cả hai tiền tố. |

Việc kiểm thử tải lên PDF của ứng dụng được thực hiện sau khi API và schema sẵn sàng.

## Hướng dẫn thực hành

| **Trang** | **Quy trình và bằng chứng** |
| :--- | :--- |
| [5.4.1 Tạo cơ sở dữ liệu mới](/my_aws_workshop/5-workshop/5.4-s3-onprem/5.4.1-prepare/) | Các tham số khởi tạo mới, cấu hình mạng, tùy chọn và Outputs của RDS. |
| [5.4.2 Triển khai tích hợp](/my_aws_workshop/5-workshop/5.4-s3-onprem/5.4.2-create-interface-enpoint/) | Stack quét PDF, xác nhận SNS và kiểm tra kế hoạch bảo vệ. |
