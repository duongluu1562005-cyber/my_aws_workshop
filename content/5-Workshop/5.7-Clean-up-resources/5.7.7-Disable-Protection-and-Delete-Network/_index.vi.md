---
title: "TẮT CHẾ ĐỘ BẢO VỆ VÀ XÓA NETWORK"
date: 2026-11-22
weight: 7
chapter: false
pre: "<b> 5.7.7 </b>"
---

## Chuẩn bị xóa Network

Sau khi xóa Runtime, Database, Integrations và Edge, kiểm tra các thành phần phụ thuộc của VPC trước khi xóa Network. Lần đọc AWS CLI trước đó không trả về network interface nào cho Tindy VPC. Network đang bật termination protection.

1. Chọn **CloudFormation → Stacks → Active → tindy-network → Resources**.
2. Xem xét 26 tài nguyên được quản lý bởi `network.yaml`, bao gồm VPC, Internet Gateway và attachment, subnets, route tables, route, subnet associations, security groups và ingress rules.

**📸 Chụp ảnh cả hai phần của danh sách tài nguyên.**



[Tài nguyên Network](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-24-network-resources-before.png)



[Subnets, route tables và VPC](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-24a-network-resources-bottom.png)

3. Chọn **Stack actions → Edit termination protection**, xác nhận `tindy-network` và chọn **Deactivated**.

**📸 Chụp ảnh lựa chọn trước khi lưu.**



[Tắt termination protection của Network](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-25-network-disable-termination-protection.png)

4. Lưu, mở lại **Delete stack**, nhập `tindy-network` và không chọn Express mode.

**📸 Chụp ảnh xác nhận xóa.**



[Xác nhận xóa Network](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-26-network-delete-confirm.png)

5. Xác nhận xóa và theo dõi Events. Nếu stack biến mất, chọn **Deleted** và tìm `tindy-network`.
6. Xác minh `tindy-network — DELETE_COMPLETE`. Ảnh cũng xác nhận việc xóa hoàn tất VPC và Internet Gateway.

**📸 Chụp ảnh quá trình xóa stack hoàn tất.**



[Network đã bị xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.7/cleanup-27-network-deleted.png)



Việc xóa Network đã hoàn tất. Đối với `DELETE_FAILED` trong một lần chạy khác, kiểm tra Logical ID và Status reason để giải quyết các thành phần phụ thuộc trước khi thử lại.
