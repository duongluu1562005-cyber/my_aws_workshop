---
title: "RUNTIME, SEED DỮ LIỆU VÀ EDGE"
date: 2026-11-22
weight: 5
chapter: false
pre: "<b> 5.5 </b>"
---

Kết nối các stack đã triển khai thành một môi trường Tindy đang hoạt động. Edge cung cấp HTTPS hostname. Runtime chạy API phía sau CloudFront VPC Origin và một ALB nội bộ. Các tác vụ Database chuẩn bị DB roles, schema và dữ liệu demo trước khi bật API.

Giữ Edge ở chế độ maintenance và Runtime tại DesiredCount 0 trong khi chuẩn bị secrets, bootstrap, migrations và seed dữ liệu. Mở lưu lượng truy cập sau khi các tác vụ DB và kiểm tra sức khỏe API thành công.

## 5.5.1 — Tạo Edge ở chế độ maintenance

Tạo `edge.yaml` trước để lấy `DistributionDomainName`, được sử dụng làm Runtime `CloudFrontPublicHost` và để cập nhật OAuth callback trong quá trình xác minh xác thực.

| **Thành phần** | **Mục đích** |
| :--- | :--- |
| CloudFront distribution | Cung cấp điểm truy cập HTTPS cho frontend và API. |
| Origin Access Control | Cho phép CloudFront truy cập frontend S3 origin thông qua bucket policy. |
| Frontend bucket policy | Giới hạn quyền truy cập origin đối với distribution đã cấu hình. |
| Maintenance routing function | Trả về HTTP 503 và maintenance JSON cho các API route trong khi backend đang được chuẩn bị. |

Ban đầu sử dụng `EdgeMode = maintenance` và để trống `AlbDnsName` và `VpcOriginId`. Chuyển sang chế độ live với các giá trị Runtime mới sau khi API hoạt động ổn định.



**Kết quả mong đợi** là CREATE_COMPLETE, ba Outputs và HTTPS `/api/health/ready` trả về HTTP 503 với maintenance JSON.

## 5.5.2 — Tạo Runtime với DesiredCount bằng 0

`runtime.yaml` sử dụng các đầu vào từ Foundation, Network, Database, Integrations và Edge.

| **Nhóm tài nguyên** | **Mục đích** |
| :--- | :--- |
| ECS cluster, task definition và service | Định nghĩa image, cấu hình, các tham chiếu secret, CPU/memory và số lượng task. |
| Execution và task IAM roles | Hỗ trợ khởi động container và quyền truy cập AWS của ứng dụng. |
| Internal ALB, listener và target group | Nhận lưu lượng từ VPC Origin, định tuyến yêu cầu đến container và kiểm tra sức khỏe. |
| CloudFront VPC Origin | Kết nối distribution với ALB nội bộ. |
| Security group ingress | Giới hạn quyền truy cập ALB theo cấu hình CloudFront. |
| CloudWatch log group | Ghi lại log API và các tác vụ cơ sở dữ liệu. |
| Conditional one-off task definitions | Chạy bootstrap, migrations và seeding một lần khi các tham số bắt buộc được cung cấp. |

Tạo Runtime với image được cố định bằng digest, `DBProvisionMode = Fresh` và `DesiredCount = 0`. Giữ nguyên các thiết lập OAuth, media và Bedrock text đã được xác minh. Giữ AI PDF ở trạng thái false trong quá trình chuẩn bị schema. Khi service với số lượng task bằng 0 đã tồn tại, chuẩn bị secrets và cập nhật các tham số để kích hoạt các tác vụ DB.

## Các giai đoạn sau khi tạo Runtime

| **Thứ tự** | **Công việc** | **Xác minh** |
| :--- | :--- | :--- |
| 1 | Chuẩn bị application/migration/seed secrets và xác minh endpoint mới. | Các tham chiếu secret khớp với cấu hình DB Fresh. |
| 2 | Thêm các đầu vào one-off task thông qua một change set. | Các định nghĩa bootstrap, migration và seed xuất hiện trong Outputs. |
| 3 | Chạy bootstrap → migrations → seed theo thứ tự. | Mỗi task kết thúc với exit code 0 và log hoàn tất. |
| 4 | Cấu hình CloudFront service SG ingress và đặt DesiredCount thành 1. | ECS service ổn định và target group ở trạng thái Healthy. |
| 5 | Cập nhật alarm dimensions và chuyển Edge sang live. | API readiness trả về 200 thông qua hostname mới. |
| 6 | Cập nhật OAuth, phát hành frontend và xác minh các luồng demo. | Frontend và API hoạt động thông qua CloudFront. |

Mỗi giai đoạn sẽ có hướng dẫn chi tiết và ảnh chụp màn hình khi được thực hiện. Thông tin xác thực API và migration phục vụ các DB role riêng biệt, trong khi master secret hỗ trợ bootstrap.

## Tiến độ phiên triển khai

| **Thành phần** | **Trạng thái đã xác minh** |
| :--- | :--- |
| Edge | UPDATE_COMPLETE, EdgeMode live, CloudFront readiness trả về HTTP 200 Healthy. |
| Runtime | UPDATE_COMPLETE với chín Outputs. Secrets và cả ba DB task definition đã sẵn sàng. |
| Bootstrap, migrations và seed | Cả ba task đã hoàn tất với exit code 0 và log hoàn tất. API đang chạy. Việc xác minh web vẫn đang chờ thực hiện. |
| Xác minh API live và web | Runtime Desired 1 / Running 1 / Pending 0, ALB có 1 target Healthy. Edge ở chế độ live, với HTTP 200 Healthy readiness thông qua CloudFront. Đăng nhập demo và hiển thị dữ liệu Discover Projects đã được xác minh. Các luồng khác vẫn đang chờ thực hiện. |

## Hướng dẫn thực hành

| **Trang** | **Quy trình và bằng chứng** |
| :--- | :--- |
| [5.5.1 Tạo Edge ở chế độ maintenance](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.1-edge-maintenance/) | Tạo distribution, ghi lại hostname và xác minh HTTP 503. |
| [5.5.2 Tạo Runtime với DesiredCount bằng 0](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.2-runtime/) | Giải thích 28 tham số, tạo ECS/ALB/VPC Origin và thu thập Outputs. |
| [5.5.3 Chuẩn bị secrets cho cơ sở dữ liệu Fresh](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.3-secrets/) | Xác minh số lượng ECS bằng 0, chạy script và thu thập các tham chiếu DB task. |
| [5.5.4 Chạy bootstrap, migrations và seed](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.4-database-tasks/) | Cấu hình Fargate và xác minh exit code cùng log theo thứ tự. |
| [5.5.5 Kích hoạt API và xác minh Runtime](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.5-activate-runtime/) | Xác định CloudFront service SG, cập nhật ingress và khởi động API. |
| [5.5.6 Chuyển Edge sang live](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.6-edge-live/) | Thu thập Runtime Outputs và xem xét change set định tuyến API của CloudFront. |
| [5.5.7 Kết nối lại quyền CI/CD](/aws_my_workshop/5-workshop/5.5-runtime-edge/5.5.7-cicd/) | Khôi phục các deployment role và đối chiếu các biến repository. |
