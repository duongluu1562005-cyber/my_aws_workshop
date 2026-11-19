
---
title: "Workshop"
date: 2026-19-11
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# TRIỂN KHAI TINDY TRÊN AWS BẰNG CLOUDFORMATION

## Tổng quan

Workshop này hướng dẫn triển khai lại **Tindy trên AWS bằng CloudFormation**, bao gồm tạo cơ sở dữ liệu PostgreSQL mới, chạy migrations, thêm dữ liệu demo và kiểm tra hoạt động của ứng dụng thông qua CloudFront trước khi quay video demo trên web.

Hạ tầng được triển khai bằng sáu CloudFormation templates: `foundation.yaml`, `network.yaml`, `database.yaml`, `integrations.yaml`, `runtime.yaml` và `edge.yaml`.

Mỗi phần trình bày các tài nguyên AWS được triển khai, cấu hình cần thiết, thông tin đầu ra (stack outputs) để sử dụng cho các stack tiếp theo, kết quả kiểm tra và hình ảnh minh họa.

Do Tindy đã được triển khai trước đó, workshop bắt đầu bằng việc dọn dẹp hạ tầng hiện có trước khi triển khai lại sáu stack trong cùng môi trường AWS. Lần triển khai mới sử dụng cơ sở dữ liệu PostgreSQL mới, dữ liệu demo và một tên miền CloudFront mới.

Quá trình dọn dẹp được trình bày tại mục 5.7, bao gồm chuyển hệ thống sang chế độ bảo trì và xóa toàn bộ sáu stack. Tất cả đều được xác nhận ở trạng thái **DELETE_COMPLETE**.

## Nội dung

1. [Tổng quan kiến trúc](5.1-overview/)
2. [Chuẩn bị trước khi triển khai](5.2-prerequisites/)
3. [Triển khai Foundation và Network](5.3-foundation-network/)
4. [Triển khai Database và Integrations](5.4-database-integrations/)
5. [Triển khai Runtime, thêm dữ liệu demo và Edge](5.5-runtime-edge/)
6. Kiểm tra ứng dụng và demo trên web
7. [Dọn dẹp tài nguyên](5.7-cleanup/)
