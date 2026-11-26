---
title: "DỌN DẸP TÀI NGUYÊN"
date: 2026-11-19
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


## Hướng dẫn thực hành

| **Trang** | **Quy trình và bằng chứng** |
| :--- | :--- |
| [5.7.1 Kiểm tra hạ tầng trước khi dọn dẹp](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.1-review-infrastructure-before-cleanup/) | Kiểm tra hạ tầng đã triển khai và xác định các tài nguyên trước khi dọn dẹp. |
| [5.7.2 Bật chế độ bảo trì CloudFront](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.2-enable-cloudfront-maintenance/) | Bật chế độ bảo trì thông qua CloudFront trước khi xóa tài nguyên. |
| [5.7.3 Chuẩn bị và xóa Runtime](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.3-prepare-and-delete-runtime/) | Chuẩn bị dọn dẹp stack Runtime và xác minh việc xóa stack. |
| [5.7.4 Tắt bảo vệ và xóa Database](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.4-disable-protection-and-delete-database/) | Tắt tính năng bảo vệ xóa cơ sở dữ liệu và xác minh việc xóa stack Database. |
| [5.7.5 Kiểm tra và xóa Integrations](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.5-review-and-delete-integrations/) | Kiểm tra các tài nguyên tích hợp và xác minh việc xóa stack. |
| [5.7.6 Tắt bảo vệ và xóa Edge](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.6-disable-protection-and-delete-edge/) | Tắt các cơ chế bảo vệ cần thiết và xóa stack Edge. |
| [5.7.7 Tắt bảo vệ và xóa Network](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.7-disable-protection-and-delete-network/) | Gỡ các thiết lập bảo vệ mạng và xác minh việc xóa stack Network. |
| [5.7.8 Xuất thông tin ánh xạ và xóa Foundation](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.8-export-mappings-and-delete-foundation/) | Xuất cấu hình và thông tin ánh xạ Foundation trước khi xóa stack. |
| [5.7.9 Kết quả dọn dẹp và chuẩn bị triển khai lại](/my_aws_workshop/5-workshop/5.7-clean-up-resources/5.7.9-cleanup-results-and-redeployment-preparation/) | Kiểm tra bằng chứng dọn dẹp, tài nguyên được giữ lại và công tác chuẩn bị triển khai lại. |
