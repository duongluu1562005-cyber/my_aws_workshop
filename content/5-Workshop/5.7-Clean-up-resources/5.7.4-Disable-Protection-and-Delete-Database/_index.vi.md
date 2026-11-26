---
title: "TẮT CHẾ ĐỘ BẢO VỆ VÀ XÓA DATABASE"
date: 2026-11-19
weight: 4
chapter: false
pre: "<b> 5.7.4 </b>"
---

## Chuẩn bị xóa Database

`database.yaml` cung cấp tham số `EnableDeletionProtection`. DB instance hiện tại có `DeletionPolicy: Snapshot` và `UpdateReplacePolicy: Snapshot`.

1. Quay lại danh sách Active stacks và mở `tindy-database`.

![Database trước khi cập nhật](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-12a-database-before.png)

2. Tạo một Standard change set bằng template hiện có.

![Tạo Database change set](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-12b-database-create-changeset.png)

3. Đặt tên là `tindy-database-prepare-delete`. Đặt `EnableDeletionProtection = false`, giữ nguyên các tham số khác. Stack hiện tại sử dụng `DBProvisionMode = Restore` và một snapshot identifier. Giữ nguyên các giá trị này cho lần cập nhật này. Chọn `Fresh` khi tạo stack thay thế.

**📸 Chụp ảnh các tham số.**



![Tham số Database](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-12-database-delete-parameter.png)

4. Giữ nguyên các tùy chọn hiện tại và tạo change set. Xác nhận chỉ có `PostgresInstance → Modify → Replacement = False`.

![Database change set](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-13-database-delete-changeset.png)

5. Thực thi với **Use deletion policy** và chờ `tindy-database — UPDATE_COMPLETE`.

![Cập nhật Database hoàn tất](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-14-database-prepared.png)

6. Truy cập Physical ID của `PostgresInstance` để đến RDS. Trong Configuration, xác nhận **Deletion protection = Disabled**. Ảnh chụp là phần cận cảnh của trường này.

![Chế độ bảo vệ đã tắt](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-15-database-protection-disabled.png)

7. Quay lại **CloudFormation → tindy-database → Delete stack**, nhập tên stack và không chọn Express mode.

**📸 Chụp ảnh hộp thoại xác nhận.**



![Xác nhận xóa Database](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-16-database-delete-confirm.png)

8. Xác nhận xóa và theo dõi Events. Template yêu cầu tạo một snapshot trước khi xóa DB instance.
9. Nếu stack biến mất, chọn **Deleted** và xác định stack hiện tại bằng thời gian tạo.
10. Xác minh `tindy-database — DELETE_COMPLETE`. Ảnh chụp cũng xác nhận việc xóa hoàn tất subnet group và parameter group.

**📸 Chụp ảnh quá trình xóa stack hoàn tất.**



![Xóa Database hoàn tất](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.4/cleanup-17-database-deleted.png)



Việc xóa Database stack đã hoàn tất. Kiểm tra riêng snapshot được tạo trong RDS Snapshots khi quản lý phần lưu trữ được giữ lại.

