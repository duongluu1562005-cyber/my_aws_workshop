# TẮT CHẾ ĐỘ BẢO VỆ VÀ XÓA EDGE

## Chuẩn bị xóa Edge và xử lý termination protection

Sau khi Integrations đã bị xóa, chọn **CloudFormation → Stacks → Active → tindy-edge → Resources**. Bốn tài nguyên của stack này là `EdgeDistribution`, `FrontendBucketPolicy`, `FrontendOriginAccessControl` và `MaintenanceRoutingFunction`.

**📸 Chụp ảnh danh sách tài nguyên trước khi xóa.**

[Tài nguyên Edge trước khi xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-21-edge-resources-before.png)

Khi chọn **Delete stack**, sẽ hiển thị thông báo rằng termination protection đang được bật và phải tắt trước.

[Termination protection của Edge đang bật](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-21a-edge-termination-protection.png)

1. Chọn **Edit termination protection** trong thông báo.
2. Xác nhận hộp thoại hiển thị `tindy-edge`, sau đó chọn **Deactivated**.

**📸 Chụp ảnh lựa chọn trước khi lưu.**

[Chọn Deactivated cho Edge](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-21b-edge-disable-termination-protection.png)

3. Nhấp **Save** để áp dụng, sau đó mở lại **Delete stack**.
4. Sau khi lưu, chọn **Delete stack**, nhập `tindy-edge` và không chọn Express mode.

**📸 Chụp ảnh xác nhận xóa.**

[Xác nhận xóa Edge](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-22-edge-delete-confirm.png)

5. Xác nhận xóa và theo dõi Events. Nếu stack biến mất, chọn **Deleted** và tìm stack được tạo vào **11 November 2026 at 12:23:27 UTC+07**.
6. Xác minh `tindy-edge — DELETE_COMPLETE`. Events cũng cho thấy việc xóa hoàn tất của `MaintenanceRoutingFunction` và `FrontendOriginAccessControl`.

**📸 Chụp ảnh quá trình xóa stack hoàn tất.**

[Edge đã bị xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.6/cleanup-23-edge-deleted.png)

Việc xóa Edge đã hoàn tất. Quá trình triển khai lại sẽ tạo một distribution mới và cập nhật cài đặt ứng dụng cho hostname của nó.
