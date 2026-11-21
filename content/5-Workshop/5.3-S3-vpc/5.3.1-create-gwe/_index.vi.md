---
title: "TRIỂN KHAI FOUNDATION"
date: 2026-11-22
weight: 1
chapter: false
pre: "<b> 5.3.1 </b>"
---

Foundation cung cấp các S3 bucket frontend/media, ECR repository cho backend, application secrets và GitHub OIDC. Template chính `foundation.yaml` cũng định nghĩa các IAM role cho triển khai và vận hành.

## 1. Chuẩn bị import các tài nguyên được giữ lại

Quá trình dọn dẹp đã giữ lại chín tài nguyên Foundation. Khôi phục quyền quản lý của CloudFormation bằng `foundation-import.yaml`, được chuẩn bị từ template đã triển khai. Template này chứa chín tài nguyên đó, loại bỏ bốn IAM role đã xóa và toàn bộ phần Outputs, đồng thời đặt mặc định `GitHubOidcProviderMode` thành `Create` để provider được đưa vào danh sách import. Các identifier đã được xuất trước khi dọn dẹp.

Sau khi import, sử dụng `foundation.yaml` để thêm các IAM role thông qua change set khi các đầu vào CloudFront và Runtime mới có sẵn. Các tài nguyên mục tiêu để import phải tồn tại từ trước.

## 2. Mở quy trình import

1. Chọn **N. Virginia (`us-east-1`) → CloudFormation → Stacks**.
2. Mở menu thả xuống **Create stack** và chọn **With existing resources (import resources)**.

**📸 Chụp ảnh menu import.**

[Import các tài nguyên hiện có](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01-foundation-import-start.png)

3. Đọc phần giới thiệu và nhấn Next.

[Giới thiệu import](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01a-foundation-import-introduction.png)

4. Chọn **Upload a template file**, chọn tệp `foundation-import.yaml` đã chuẩn bị từ thư mục backup.

**📸 Chụp ảnh tên tệp đã chọn.**

[Tải lên template import](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-02-foundation-import-template.png)

5. Nhấn Next và xác minh **Resources to import (9)** trong phần Identify resources.

[Phần trên của identifiers](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03-foundation-identifiers-empty-top.png)

[Phần dưới của identifiers](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03a-foundation-identifiers-empty-bottom.png)

## 3. Điền identifiers từ bảng ánh xạ đã lưu

| **Logical ID** | **Thuộc tính định danh** | **Giá trị ánh xạ** |
| :--- | :--- | :--- |
| `ApiRepository` | `RepositoryName` | Tên repository `tindy-api`. |
| `MediaBucketPolicy` | `Bucket` | Tên media bucket. |
| `GitHubOAuthSecret` | `Id` | ARN đầy đủ của GitHub OAuth secret. |
| `DatabaseConnectionSecret` | `Id` | ARN đầy đủ của DB connection secret. |
| `GoogleOAuthSecret` | `Id` | ARN đầy đủ của Google OAuth secret. |
| `JwtSigningKeySecret` | `Id` | ARN đầy đủ của JWT secret. |
| `MediaBucket` | `BucketName` | Cùng tên media bucket với MediaBucketPolicy. |
| `FrontendBucket` | `BucketName` | Tên frontend bucket. |
| `GitHubOidcProvider` | `Arn` | ARN của provider cho `token.actions.githubusercontent.com`. |

Sao chép các giá trị từ tệp `console-import-identifiers.csv` đã xuất. Các trường Id của Secrets Manager sử dụng ARN của tài nguyên. Các giá trị secret vẫn được lưu trong Secrets Manager.

**📸 Chụp ảnh tất cả chín identifier đã điền trước khi nhấn Next, sử dụng hai ảnh chụp màn hình nếu cần.**

Tất cả chín identifier đã được điền và kiểm tra đối chiếu với bảng ánh xạ trước khi dọn dẹp.

[Phần trên của identifiers đã điền](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04-foundation-identifiers-filled-top.png)

[Phần dưới của identifiers đã điền](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04a-foundation-identifiers-filled-bottom.png)

## 4. Tham số ở giai đoạn import

Nhấn **Next** để đến Specify stack details và nhập `tindy-foundation`. Sử dụng `GitHubOidcProviderMode = Create` để đưa provider hiện có vào quá trình import. Đặt `BackendDeployEnabled`, `InfraPlannerEnabled` và `LifecycleWorkflowsEnabled` thành `false`. Để trống `BackendApiExecutionRoleName`, `BackendApiTaskRoleName`, `FrontendDeployDistributionId` và `FrontendDeployOidcSubject` ở giai đoạn này.

**📸 Chụp ảnh tên stack và tất cả các tham số trước khi đến Review.**

Các giá trị đã xác minh cho thấy tên stack là `tindy-foundation`, `GitHubOidcProviderMode = Create`, cả ba tùy chọn kích hoạt role đều được đặt thành `false`, và bốn trường tham chiếu Runtime/Frontend để trống.

[Chi tiết stack Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05-foundation-import-stack-details.png)

[Tham số import Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05a-foundation-import-parameters.png)

## 5. Xem xét trước khi import

Nhấn **Next** để đến Review. Kiểm tra tên stack, các tham số và tất cả chín tài nguyên đối chiếu với bảng ánh xạ đã lưu. Chụp ảnh danh sách tài nguyên trước khi thực thi. Nếu Resource changes được hiển thị, dự kiến có chín mục với Action `Import`.

Trang Review hiển thị chín tài nguyên và các tham số stack đã chọn.

[Xem xét tài nguyên Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06-foundation-import-review-resources.png)

[Xem xét tham số Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06a-foundation-import-review-parameters.png)

### Xử lý lỗi xác thực Outputs trong quá trình import

Template import ban đầu vẫn chứa Outputs. CloudFormation báo lỗi `As part of the import operation, you cannot modify or add [Outputs]`. Change set không được tạo thành công và việc hoàn tất import vẫn chưa được xác minh.

[Lỗi xác thực Outputs](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06b-foundation-import-outputs-error.png)

Tệp `foundation-import.yaml` đã được sửa để loại bỏ toàn bộ phần Outputs. Quay lại **Specify template**, tải lên tệp đã sửa, xác minh tất cả chín identifier và giữ nguyên các tham số đã được ghi lại. Sau khi import, khôi phục Outputs thông qua một lần cập nhật bằng template chính `foundation.yaml`.

### Xem xét sau khi sửa template

Tải lại template không có Outputs, giữ nguyên identifiers và parameters. Việc tạo change set thành công với **Changes (9)** và cả chín action được đặt thành `Import`.

[Xem xét tài nguyên đã sửa](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07-foundation-import-review-resources.png)

[Xem xét tham số đã sửa](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07a-foundation-import-review-parameters.png)

**📸 Chụp ảnh tất cả chín action Import trước khi thực thi.**

[Chín action Import](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07b-foundation-nine-import-actions.png)

## 6. Thực thi import

Nhấn **Import resources** và theo dõi **Events** của `tindy-foundation` cho đến khi stack đạt trạng thái `IMPORT_COMPLETE`. Đối với trường hợp import rollback hoặc các lỗi khác, kiểm tra Logical ID và Status reason trước khi tiếp tục.

**📸 Chụp ảnh sự kiện IMPORT_COMPLETE của stack và Resources hiển thị chín tài nguyên đã import.**

Quá trình import hoàn tất thành công. Ảnh chụp màn hình Resources cho thấy stack `tindy-foundation` ở trạng thái `IMPORT_COMPLETE`, **Resources (9)** và `UPDATE_COMPLETE` đối với các tài nguyên được hiển thị.

[Các tài nguyên Foundation đã import](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09-foundation-imported-resources.png)

Tiếp theo, cập nhật bằng template chính `foundation.yaml` để khôi phục Outputs và cấu hình các IAM role khi các điều kiện tiên quyết cho việc triển khai có sẵn.

## 7. Khôi phục template chính và Outputs

Tạo change set `tindy-foundation-restore-template` bằng cách sử dụng **Replace existing template → Upload a template file**, chọn `foundation.yaml` từ infrastructure repository. Giữ `GitHubOidcProviderMode = Create`. Khôi phục `InfraPlannerEnabled = true` từ cấu hình trước khi dọn dẹp để tạo role lập kế hoạch change set cho GitHub Actions. Đầu vào OIDC của role này đã có sẵn ở giai đoạn này.

Giữ `BackendDeployEnabled = false` và `LifecycleWorkflowsEnabled = false`. Để trống cả hai tên Runtime role và cả hai tham số frontend cho đến khi các Outputs Runtime và Edge mới có sẵn.

Xác nhận IAM capabilities và tạo change set. Resource changes dự kiến chỉ thêm `InfraPlannerRole`; template chính khôi phục Outputs. Xem xét các thay đổi thực tế trước khi thực thi.

**📸 Chụp ảnh tên tệp template chính, các tham số và Resource changes.** Phần này mô tả bước tiếp theo. Kết quả cập nhật sẽ được bổ sung sau khi xác minh.

Đã mở **Stack actions → Create a change set**, chọn **Standard change set → Replace existing template → Upload a template file**, và tải lên `foundation.yaml`.

[Foundation sau khi import](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09a-foundation-imported-resources.png)

[Menu tạo change set Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10a-foundation-create-changeset-menu.png)

[Template foundation.yaml chính đã chọn](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10-foundation-main-template.png)

Change set được đặt tên là `tindy-foundation-restore-template`. Các tham số đã xác minh là `GitHubOidcProviderMode = Create`, `InfraPlannerEnabled = true`, `BackendDeployEnabled = false` và `LifecycleWorkflowsEnabled = false`. Cả hai tên Runtime role và cả hai trường Frontend vẫn để trống ở giai đoạn này.

**📸 Chụp ảnh tên change set và tất cả các tham số.**

[Tham số Foundation chính](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11-foundation-main-parameters.png)

[Phần dưới của tham số Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11a-foundation-main-parameters-bottom.png)

Nhấn Next, giữ Express mode ở trạng thái tắt và rollback ở trạng thái bật, xác nhận IAM capabilities và tạo change set.

Change set `tindy-foundation-restore-template` đạt trạng thái `CREATE_COMPLETE` và `AVAILABLE`, với một thay đổi bổ sung `InfraPlannerRole` thuộc loại `AWS::IAM::Role`.

**📸 Chụp ảnh Resource changes trước khi thực thi.**

[Change set Foundation chính](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12-foundation-main-changeset.png)

Thực thi change set, giữ **Use deletion policy** nếu được hiển thị. Theo dõi Events cho đến khi `tindy-foundation — UPDATE_COMPLETE`. Kiểm tra Outputs để lấy các tham chiếu bucket, ECR, secret, OIDC và `InfraPlannerRoleArn` cần thiết trong các bước tiếp theo.

**📸 Chụp ảnh trạng thái stack đã hoàn tất và tất cả Outputs.**

Change set đã được thực thi với **Use deletion policy**.

[Thực thi change set Foundation](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12a-foundation-main-execute.png)

Stack đạt trạng thái **UPDATE_COMPLETE**. Ảnh chụp màn hình Outputs hiển thị trạng thái stack đã hoàn tất và **Outputs (14)**.

[Cập nhật Foundation hoàn tất](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-13-foundation-main-update-complete.png)

[Phần trên của Foundation Outputs](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14-foundation-main-outputs-top.png)

[Phần dưới của Foundation Outputs](/aws_my_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14a-foundation-main-outputs-bottom.png)

Các tham chiếu frontend/media bucket, ECR, application secret, OIDC, Data Protection path và `InfraPlannerRoleArn` đã có sẵn trở lại. Các role triển khai backend/frontend và lifecycle sẽ được cấu hình khi các đầu vào Runtime và Edge mới có sẵn. So sánh các biến role trong GitHub repository với Outputs mới trước khi chạy các workflow của chúng.

Foundation đã sẵn sàng cho việc triển khai Network.
