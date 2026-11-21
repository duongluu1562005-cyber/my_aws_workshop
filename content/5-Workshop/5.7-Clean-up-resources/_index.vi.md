---
title: "DỌN DẸP TÀI NGUYÊN"
date: 2026-11-22
weight: 7
chapter: false
pre: "<b> 5.7. </b>"
---

Phần này ghi lại quá trình dọn dẹp môi trường Tindy hiện có thông qua CloudFormation tại **N. Virginia (`us-east-1`)**. Việc dọn dẹp diễn ra trước khi triển khai lại sáu stack, một cơ sở dữ liệu **Fresh**, bootstrap, migrations và seed dữ liệu demo. Phần trình diễn web sẽ sử dụng một CloudFront hostname mới.

## Tiến độ đã xác minh

| **Thành phần** | **Trạng thái đã ghi nhận** |
| :--- | :--- |
| Edge | Đã xác minh chế độ maintenance, termination protection đã tắt và việc xóa stack được xác nhận ở trạng thái `DELETE_COMPLETE`. |
| Runtime | ALB ingress đã được chuyển sang CloudFront prefix list. Việc xóa stack đã hoàn tất. |
| Database | Deletion protection đã tắt và việc xóa stack được xác minh ở trạng thái `DELETE_COMPLETE`. |
| Integrations | Stack và các tài nguyên được hiển thị báo cáo trạng thái `DELETE_COMPLETE`. |
| Network | Termination protection đã tắt và việc xóa được xác minh ở trạng thái `DELETE_COMPLETE`. |
| Foundation | Bảng ánh xạ đã được xuất và việc xóa stack được xác minh ở trạng thái `DELETE_COMPLETE`. Các tài nguyên được giữ lại báo cáo trạng thái `DELETE_SKIPPED`. |

## Các bước dọn dẹp

| **Trang hướng dẫn** |
| :--- |
| [5.7.1 Kiểm tra hạ tầng trước khi dọn dẹp](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.1-inventory/) |
| [5.7.2 Kích hoạt chế độ bảo trì CloudFront](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.2-maintenance/) |
| [5.7.3 Chuẩn bị và xóa Runtime](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.3-runtime/) |
| [5.7.4 Tắt chế độ bảo vệ và xóa Database](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.4-database/) |
| [5.7.5 Kiểm tra và xóa Integrations](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.5-integrations/) |
| [5.7.6 Tắt chế độ bảo vệ và xóa Edge](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.6-edge/) |
| [5.7.7 Tắt chế độ bảo vệ và xóa Network](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.7-network/) |
| [5.7.8 Xuất bảng ánh xạ và xóa Foundation](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.8-foundation/) |
| [5.7.9 Kết quả dọn dẹp và chuẩn bị triển khai lại](/aws_my_workshop/5-workshop/5.7-cleanup/5.7.9-results/) |

