---
title: "CHUẨN BỊ VÀ XÓA RUNTIME"
date: 2026-11-19
weight: 3
chapter: false
pre: "<b> 5.7.3 </b>"
---

## Chuẩn bị Runtime để xóa

`runtime.yaml` quản lý ECS Fargate, ALB nội bộ và CloudFront VPC Origin. Chuyển nguồn của `AlbIngressFromCloudFront` từ service security group sang CloudFront origin-facing prefix list trước khi xóa Runtime.

1. Ghi lại Runtime Outputs, bao gồm cluster, service, task definition, ALB, target group và VPC Origin.

![Runtime Outputs](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06a-runtime-outputs-before.png)

2. Tạo một Standard change set bằng template hiện có, đặt tên là `tindy-runtime-prepare-delete`.

![Tạo Runtime change set](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06b-runtime-create-changeset.png)

3. Xóa giá trị `CloudFrontServiceSecurityGroupId`, giữ nguyên các tham số khác.

![Tham số security group để trống](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06-runtime-delete-parameters.png)

4. Đặt `CloudFrontOriginPrefixListId` thành giá trị đã xác minh `pl-3b927c52` cho lần chạy này. Đối với môi trường khác, tra cứu IPv4 managed prefix list `com.amazonaws.global.cloudfront.origin-facing` trong Region mục tiêu.

![Tham số prefix list](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06c-runtime-prefix-list-parameter.png)

5. Xác nhận IAM capabilities, giữ Express mode ở trạng thái tắt và rollback ở trạng thái bật, sau đó tạo change set.

![IAM capabilities](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06d-runtime-iam-capabilities.png)



![Tùy chọn Review Runtime](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-06e-runtime-changeset-review.png)

6. Chờ `CREATE_COMPLETE` và `AVAILABLE`. Xác nhận chỉ có `AlbIngressFromCloudFront → Modify → Replacement = True`. Policy action `ReplaceAndDelete` áp dụng cho quy tắc ingress đó.

**📸 Chụp ảnh Resource changes trước khi thực thi.**



![Thay thế ingress](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-07-runtime-delete-changeset.png)

7. Thực thi bằng deletion policy và theo dõi Events. Ảnh này ghi lại thời điểm bắt đầu cập nhật.

![Runtime bắt đầu cập nhật](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-08a-runtime-update-started.png)

8. Chờ stack đạt `UPDATE_COMPLETE`. Trong Resources, xác nhận quy tắc đã được cập nhật thành công.

![Runtime đã chuẩn bị](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-08-runtime-prepared.png)

9. Mở **tindy-network → Resources → AlbSecurityGroup**, truy cập Physical ID của nó và kiểm tra Inbound rules. Xác nhận HTTP TCP 80 sử dụng nguồn `pl-3b927c52`.

**📸 Chụp ảnh security group, port và nguồn prefix list.**



![ALB ingress đã xác minh](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-09-alb-prefix-list.png)



Nếu Console báo cáo không có thay đổi trong khi service security group vẫn được tham chiếu, truyền một tham số rỗng rõ ràng thông qua CLI. Lần chạy được ghi lại này đã hoàn tất thay đổi thông qua Console.

## Xóa Runtime

1. Chọn **CloudFormation → tindy-runtime → Delete stack**.
2. Nhập `tindy-runtime` và không chọn Express mode.

**📸 Chụp ảnh hộp thoại xác nhận.**



![Xác nhận xóa Runtime](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-10-runtime-delete-confirm.png)

3. Xác nhận xóa và theo dõi Events. Nếu stack biến mất, chọn **Deleted** và xác định stack hiện tại bằng thời gian tạo.
4. Xác nhận `tindy-runtime — DELETE_COMPLETE`.

**📸 Chụp ảnh quá trình xóa stack hoàn tất.**



![Runtime đã xóa](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.3/cleanup-11-runtime-deleted.png)



Đối với `DELETE_FAILED`, kiểm tra Logical ID và Status reason trước khi thử lại.
