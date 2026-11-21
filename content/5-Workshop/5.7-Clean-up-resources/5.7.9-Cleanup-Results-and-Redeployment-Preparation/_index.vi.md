---
title: "ẾT QUẢ DỌN DẸP VÀ CHUẨN BỊ TRIỂN KHAI LẠI"
date: 2026-11-22
weight: 9
chapter: false
pre: "<b> 5.7.9 </b>"
---

## Kết quả dọn dẹp và chuẩn bị triển khai lại

Runtime, Database, Integrations, Edge, Network và Foundation đều có bằng chứng đã xác minh trạng thái `DELETE_COMPLETE`. Quá trình triển khai lại bắt đầu bằng việc import các tài nguyên Foundation. Cơ sở dữ liệu mới sẽ sử dụng chế độ khởi tạo Fresh, tiếp theo là bootstrap, migrations và seed dữ liệu demo.



Kiểm kê riêng các RDS snapshot, S3, ECR, secrets, OIDC và dữ liệu runtime được giữ lại nằm ngoài quyền quản lý của stack. Các tài nguyên được giữ lại có thể tiếp tục phát sinh chi phí. Thư mục backup lưu trữ các bảng ánh xạ và cấu hình trước khi xóa để phục vụ triển khai lại.



Kiểm tra các account ID và thông tin trong ARN trên ảnh chụp màn hình trước khi công bố. Các quy trình triển khai tiếp theo và ảnh chụp màn hình sẽ được bổ sung vào các phần workshop tương ứng.



Sau khi xóa cả sáu stack, chọn **Active**. Danh sách trống xác nhận không còn stack nào đang hoạt động trong Region này.



[Các stack Active trong CloudFormation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.9/cleanup-33-no-active-tindy-stacks.png)

