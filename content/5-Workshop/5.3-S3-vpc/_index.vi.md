---
title : "Foundation và Network"
date : 2026-11-19 
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---


# Foundation và Network

Phần này trình bày quá trình triển khai hạ tầng lưu trữ, cấu hình ứng dụng và hệ thống mạng của Tindy. Foundation quản lý các S3 bucket, ECR repository và application secrets, trong khi Network cung cấp VPC, subnet và security group cần thiết cho Database và Runtime.

Do hạ tầng cũ đã được dọn dẹp trước khi triển khai lại, chín tài nguyên Foundation được giữ lại cần được import vào stack mới trước khi khôi phục template chính.

## 5.3.1 — Triển khai Foundation

Template `foundation.yaml` quản lý các tài nguyên AWS cần duy trì lâu dài và các IAM role được kích hoạt theo điều kiện để phục vụ GitHub Actions.

| **Nhóm tài nguyên** | **Vai trò trong Tindy** |
| :--- | :--- |
| Frontend bucket | Lưu trữ các tệp build frontend, được phân phối qua CloudFront bằng Origin Access Control. |
| Media bucket và policy | Lưu trữ tệp tải lên và áp dụng các điều kiện truy cập cần thiết cho quá trình quét PDF. |
| ECR repository | Lưu trữ container image của backend API, được Runtime tham chiếu thông qua image digest. |
| Application secrets | Cung cấp các thông tin tham chiếu cấu hình database, JWT và OAuth cho ECS task. |
| GitHub OIDC provider | Cho phép GitHub Actions xác thực với AWS thông qua các deployment role. |
| Deployment/operations IAM roles | Cấp quyền cho các workflow khi tham số tương ứng được kích hoạt. |

### Quy trình triển khai

Quá trình triển khai Foundation gồm các bước:

1. Import chín tài nguyên được giữ lại bằng `foundation-import.yaml`.
2. Kiểm tra stack đạt trạng thái `IMPORT_COMPLETE`.
3. Cập nhật stack bằng `foundation.yaml` để khôi phục Outputs và tạo lại `InfraPlannerRole`.
4. Cấu hình các role phụ thuộc vào CloudFront và Runtime sau khi có đầy đủ thông tin đầu vào.

**Kết quả mong đợi:** Foundation stack đạt trạng thái `UPDATE_COMPLETE`, cung cấp Outputs gồm tên S3 bucket, ECR URI, secret ARNs, OIDC provider và các role đã được kích hoạt.

## 5.3.2 — Triển khai Network

Template `network.yaml` tạo VPC với dải địa chỉ CIDR `10.20.0.0/16`. Hệ thống mạng được chia thành các nhóm subnet trải rộng trên hai Availability Zone, mỗi nhóm phục vụ một thành phần cụ thể.

| **Thành phần** | **Vai trò** |
| :--- | :--- |
| Public task subnets | Chạy Fargate API và các tác vụ thực hiện một lần, với việc cấp public IP được cấu hình thông qua ECS. |
| Private app subnets | Chứa internal ALB, kết nối với CloudFront thông qua VPC Origin. |
| Private DB subnets | Cung cấp subnet group cho RDS PostgreSQL. |
| Internet Gateway và route tables | Cấu hình định tuyến mạng cho các nhóm subnet. |
| Security groups và ingress rules | Kiểm soát lưu lượng giữa ALB, API, migration tasks và database. |

Network template định nghĩa tổng cộng **26 tài nguyên và 9 Outputs**.

Các ID tài nguyên mạng được tạo từ stack này sẽ được sử dụng trong mục 5.4 và 5.5.

Mặc dù các subnet được phân bố trên hai Availability Zone, số lượng API task đang chạy và cấu hình Multi-AZ của database vẫn phụ thuộc vào thiết lập trong Runtime và Database.

## Các thành phần phụ thuộc cho những stack tiếp theo

Outputs từ Foundation và Network được sử dụng để triển khai các phần hạ tầng còn lại.

| **Output** | **Được sử dụng bởi** |
| :--- | :--- |
| `MediaBucketName` | Integrations protection plan và hệ thống lưu trữ media của Runtime. |
| `FrontendBucketName` | Edge S3 origin và frontend bucket policy. |
| `ApiRepositoryUri`, secret ARNs | Container image và cấu hình ứng dụng trong Runtime. |
| `PrivateDbSubnetIds`, `DatabaseSecurityGroupId` | Triển khai RDS tại mục 5.4.1. |
| VPC, API/ALB subnets và security groups | Triển khai Runtime tại mục 5.5.2. |

## Kết quả kiểm tra

Sau khi hoàn tất triển khai, cả hai stack đều đạt trạng thái mong đợi.

| **Thành phần** | **Kết quả xác nhận** |
| :--- | :--- |
| Foundation | Import tài nguyên thành công. Quá trình cập nhật template chính đạt `UPDATE_COMPLETE`, với 14 Outputs tại thời điểm này. |
| Network | Stack đạt `CREATE_COMPLETE`, tạo ra 9 Outputs. |

## Hướng dẫn thực hành

Các trang dưới đây trình bày chi tiết từng bước triển khai và hình ảnh minh chứng.

| **Hướng dẫn** | **Nội dung** |
| :--- | :--- |
| [5.3.1 Triển khai Foundation](5.3.1-create-gwe/) | Import các tài nguyên được giữ lại, khôi phục template chính, kiểm tra IAM role và Outputs. |
| [5.3.2 Triển khai Network](5.3.2-test-gwe/) | Tạo tài nguyên mạng, cấu hình tags và kiểm tra các Outputs được tạo. |
