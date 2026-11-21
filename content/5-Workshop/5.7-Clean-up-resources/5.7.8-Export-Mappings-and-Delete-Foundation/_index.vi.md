
# XUẤT BẢNG ÁNH XẠ VÀ XÓA FOUNDATION

## Xuất cấu hình Foundation trước khi xóa

Sau khi Network đã bị xóa, các bản xuất AWS CLI được lưu vào `backup/tindy-foundation-before-cleanup` trong Worklog repository. Các file bao gồm `resources.json`, `stack-metadata.json`, `deployed-template.json` và `retained-resource-mapping.csv`. Các bản ghi import phục vụ vận hành này nằm ngoài thư mục static của website.



Foundation có 13 tài nguyên và termination protection đang được bật. Template đã triển khai của stack này bao gồm chín tài nguyên sử dụng `RetainExceptOnCreate`, bao gồm hai S3 bucket, media bucket policy, ECR repository, bốn application secret và GitHub OIDC provider. Bốn deployment/operations IAM role còn lại sẽ bị xóa theo template. Bảng ánh xạ đã lưu chứa logical ID, type và physical ID của chín tài nguyên được giữ lại.

1. Mở **CloudFormation → Stacks → Active → tindy-foundation → Resources** và đối chiếu 13 tài nguyên của stack với bảng ánh xạ đã xuất.

**📸 Chụp ảnh cả hai phần của Resources.**



[Tài nguyên Foundation trước khi xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-28-foundation-resources-before.png)



[Phần dưới của tài nguyên Foundation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-28a-foundation-resources-bottom.png)

2. Mở **Outputs** và ghi lại 17 outputs bao gồm các tham chiếu ECR, S3, secret, OIDC và IAM role cùng Data Protection path. `DataProtectionParameterPath` xác định SSM path được API sử dụng. Các key được API tạo trong thời gian chạy yêu cầu quản lý riêng biệt với các tài nguyên stack.

**📸 Chụp ảnh cả hai phần của Outputs.**



[Foundation Outputs trước khi xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-29-foundation-outputs-before.png)



[Phần dưới của Foundation Outputs](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-29a-foundation-outputs-bottom.png)

3. Chọn **Stack actions → Edit termination protection**, xác nhận `tindy-foundation`, chọn **Deactivated** và lưu.

**📸 Chụp ảnh Deactivated trước khi lưu.**



[Tắt termination protection của Foundation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-30-foundation-disable-termination-protection.png)

4. Chọn **Delete stack**, nhập `tindy-foundation` và không chọn Express mode.

**📸 Chụp ảnh xác nhận xóa.**



[Xác nhận xóa Foundation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-31-foundation-delete-confirm.png)

5. Xác nhận xóa và theo dõi Events. Nếu stack biến mất khỏi Active, chọn **Deleted** và tìm Foundation.
6. Xác minh `tindy-foundation — DELETE_COMPLETE`. `DELETE_SKIPPED` cho biết các tài nguyên được giữ lại theo policy của template. Ảnh chụp hiển thị OIDC provider, ECR repository và frontend bucket được giữ lại, trong khi các role được hiển thị đã bị xóa.

**📸 Chụp ảnh quá trình xóa stack hoàn tất và các tài nguyên DELETE_SKIPPED.**



[Foundation đã xóa với các tài nguyên được giữ lại](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-32-foundation-deleted.png)



Cả sáu stack trong phiên triển khai này đều đã được xóa thành công. Sử dụng bảng ánh xạ đã lưu để import các tài nguyên Foundation được giữ lại vào stack mới. Xác minh sự tồn tại và cấu hình của chúng trước khi import.

