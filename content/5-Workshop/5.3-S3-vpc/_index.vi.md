---
title : "Foundation và Network"
date : 2026-11-19 
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---
# Foundation và Network

Xây dựng hệ thống lưu trữ, cấu hình ứng dụng và mạng cho Tindy. Foundation cung cấp các bucket, kho lưu trữ image và các tham chiếu secret. Network cung cấp VPC, subnet và security group được Database và Runtime sử dụng.

Phiên triển khai này diễn ra sau quá trình dọn dẹp. Chín tài nguyên Foundation đã được giữ lại, vì vậy cần import chúng vào stack mới trước khi khôi phục template chính.

## 5.3.1 — Triển khai Foundation

`foundation.yaml` quản lý các tài nguyên được duy trì lâu dài và các vai trò GitHub Actions có điều kiện.

| **Nhóm tài nguyên** | **Mục đích trong Tindy** |
| :--- | :--- |
| Frontend bucket | Lưu trữ các tài nguyên build frontend, được phân phối bởi CloudFront thông qua Origin Access Control. |
| Media bucket và policy | Lưu trữ các tệp tải lên và thực thi các điều kiện truy cập được sử dụng trong quá trình quét PDF. |
| ECR repository | Lưu trữ các image API, được cố định bằng digest cho Runtime. |
| Application secrets | Cung cấp các tham chiếu cấu hình DB, JWT và OAuth cho các tác vụ ECS. |
| GitHub OIDC provider | Hỗ trợ xác thực GitHub Actions thông qua các vai trò triển khai. |
| Deployment/operations IAM roles | Cấp quyền cho các workflow khi các tham số của chúng kích hoạt các vai trò này. |

Đầu tiên, import chín tài nguyên được giữ lại bằng `foundation-import.yaml`. Sau khi đạt trạng thái `IMPORT_COMPLETE`, cập nhật bằng `foundation.yaml` để khôi phục Outputs và tạo lại `InfraPlannerRole`. Cấu hình các vai trò phụ thuộc vào đầu vào CloudFront và Runtime mới khi các đầu vào đó đã có sẵn.



**Kết quả mong đợi** là stack đạt trạng thái `UPDATE_COMPLETE`, với Outputs bao gồm tên các bucket, ECR URI, secret ARNs, OIDC provider và vai trò đã được kích hoạt.

## 5.3.2 — Triển khai Network

`network.yaml` tạo VPC `10.20.0.0/16`. Mỗi nhóm subnet trải rộng trên hai Availability Zone và phục vụ một thành phần cụ thể.

| **Thành phần** | **Mục đích sử dụng** |
| :--- | :--- |
| Public task subnets | Fargate API và các tác vụ chạy một lần với việc gán public IP được cấu hình trong ECS. |
| Private app subnets | ALB nội bộ, được kết nối với CloudFront thông qua VPC Origin. |
| Private DB subnets | Nhóm subnet cho RDS PostgreSQL. |
| Internet Gateway và route tables | Định tuyến cho các nhóm subnet. |
| Security groups và ingress rules | Kiểm soát lưu lượng giữa ALB, API, các tác vụ migration và cơ sở dữ liệu. |

Template định nghĩa 26 tài nguyên và chín Outputs. Sử dụng các ID mạng mới trong phần 5.4 và 5.5. Các subnet trải rộng trên hai Availability Zone, trong khi số lượng API task và cơ chế Multi-AZ của cơ sở dữ liệu tuân theo các thiết lập Runtime và Database.

## Các phụ thuộc cho những stack tiếp theo

| **Output từ phần này** | **Thành phần sử dụng** |
| :--- | :--- |
| `MediaBucketName` | Kế hoạch bảo vệ của Integrations và media của Runtime. |
| `FrontendBucketName` | S3 origin và bucket policy của Edge. |
| `ApiRepositoryUri`, secret ARNs | Image và cấu hình ứng dụng của Runtime. |
| `PrivateDbSubnetIds`, `DatabaseSecurityGroupId` | RDS trong phần 5.4.1. |
| VPC, các subnet và security group của API/ALB | Runtime trong phần 5.5.2. |

## Kết quả đã xác minh

| **Thành phần** | **Trạng thái** |
| :--- | :--- |
| Foundation | Import hoàn tất và quá trình cập nhật template chính đạt trạng thái `UPDATE_COMPLETE`, với 14 Outputs tại giai đoạn này. |
| Network | `CREATE_COMPLETE`, với chín Outputs. |

## Hướng dẫn thực hành

| **Trang** | **Quy trình và bằng chứng** |
| :--- | :--- |
| [5.3.1 Triển khai Foundation](/aws_my_workshop/5-workshop/5.3-foundation-network/5.3.1-foundation/) | Import các tài nguyên được giữ lại, khôi phục template chính, xem xét vai trò và Outputs. |
| [5.3.2 Triển khai Network](/aws_my_workshop/5-workshop/5.3-foundation-network/5.3.2-network/) | Tạo hệ thống mạng, thêm tags và xác minh các Outputs mới. |
