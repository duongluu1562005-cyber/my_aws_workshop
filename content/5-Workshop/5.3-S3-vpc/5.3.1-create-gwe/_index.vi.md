
## 5.3.1 — Triển khai Foundation

### Tổng quan

Foundation là stack quản lý các tài nguyên AWS dùng chung cho ứng dụng Tindy. Các tài nguyên chính bao gồm:

- **Amazon S3:** Lưu trữ mã nguồn frontend đã build và các tệp media được tải lên.
- **Amazon ECR:** Lưu trữ container image của backend API.
- **AWS Secrets Manager:** Quản lý thông tin kết nối cơ sở dữ liệu, khóa JWT và cấu hình OAuth.
- **GitHub OIDC:** Cho phép GitHub Actions xác thực với AWS mà không cần lưu trữ access key dài hạn.
- **IAM Roles:** Cung cấp quyền cần thiết cho việc lập kế hoạch hạ tầng, triển khai ứng dụng và các quy trình vận hành.

Do Tindy đã được triển khai trước đó, quá trình dọn dẹp hạ tầng vẫn giữ lại 9 tài nguyên thuộc Foundation. Các tài nguyên này cần được import trở lại CloudFormation trước khi khôi phục template chính.

Quá trình triển khai gồm hai giai đoạn:

1. Import 9 tài nguyên hiện có bằng `foundation-import.yaml`.
2. Khôi phục template chính `foundation.yaml`, phần Outputs và IAM role có thể cấu hình tại giai đoạn này.

### Bước 1: Chuẩn bị import tài nguyên Foundation

Template `foundation-import.yaml` được chuẩn bị từ template Foundation đã triển khai trước đó, nhằm đưa các tài nguyên được giữ lại trở về dưới sự quản lý của CloudFormation.

Template sử dụng cho quá trình import có những thay đổi sau:

- Giữ lại 9 tài nguyên AWS đã tồn tại.
- Loại bỏ 4 IAM role đã bị xóa trong quá trình dọn dẹp.
- Xóa toàn bộ phần `Outputs`.
- Đặt `GitHubOidcProviderMode = Create` để đưa OIDC provider hiện có vào danh sách import.

Thông tin định danh của các tài nguyên đã được xuất và lưu trong tệp `console-import-identifiers.csv` trước khi dọn dẹp hạ tầng.

**Lưu ý:** Import không tạo tài nguyên AWS mới. Cả 9 tài nguyên phải tồn tại trước khi thực hiện.

### Bước 2: Bắt đầu quá trình import

Truy cập **AWS Management Console** và chọn khu vực **US East (N. Virginia) — us-east-1**.

Đi đến:

**CloudFormation → Stacks → Create stack → With existing resources (import resources)**

![Bắt đầu import tài nguyên Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01-foundation-import-start.png)

Trang giới thiệu cung cấp thông tin về cách đưa các tài nguyên AWS hiện có vào CloudFormation.

Nhấn **Next** để tiếp tục.

![Trang giới thiệu import tài nguyên](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-01a-foundation-import-introduction.png)

Tại mục **Specify template**:

1. Chọn **Upload a template file**.
2. Tải lên tệp `foundation-import.yaml`.
3. Nhấn **Next**.

![Tải lên template import Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-02-foundation-import-template.png)

### Bước 3: Xác định 9 tài nguyên cần import

CloudFormation hiển thị **Resources to import (9)**.

Mỗi tài nguyên cần được cung cấp thông tin định danh để CloudFormation xác định đúng tài nguyên đang tồn tại trên AWS.

| Logical ID | Thuộc tính định danh | Giá trị cần nhập |
|---|---|---|
| ApiRepository | RepositoryName | Tên repository `tindy-api` hiện có |
| MediaBucketPolicy | Bucket | Tên media bucket hiện có |
| GitHubOAuthSecret | Id | ARN của GitHub OAuth secret |
| DatabaseConnectionSecret | Id | ARN của database connection secret |
| GoogleOAuthSecret | Id | ARN của Google OAuth secret |
| JwtSigningKeySecret | Id | ARN của JWT signing secret |
| MediaBucket | BucketName | Tên media bucket hiện có |
| FrontendBucket | BucketName | Tên frontend bucket hiện có |
| GitHubOidcProvider | Arn | ARN của GitHub OIDC provider hiện có |

Ban đầu, các trường định danh chưa được điền.

![Danh sách identifiers chưa nhập - phần trên](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03-foundation-identifiers-empty-top.png)

![Danh sách identifiers chưa nhập - phần dưới](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-03a-foundation-identifiers-empty-bottom.png)

Sử dụng các giá trị đã lưu trong `console-import-identifiers.csv` để nhập thông tin cho từng tài nguyên.

Khi nhập cần chú ý:

- Các tài nguyên Secrets Manager sử dụng ARN đầy đủ, không phải tên secret hoặc giá trị bí mật.
- `MediaBucketPolicy` và `MediaBucket` phải tham chiếu đến cùng một bucket.
- ARN của GitHub OIDC provider phải khớp với provider đã tồn tại.

Sau khi nhập, kiểm tra lại toàn bộ 9 identifiers với dữ liệu đã xuất trước khi dọn dẹp hạ tầng.

![Identifiers đã nhập - phần trên](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04-foundation-identifiers-filled-top.png)

![Identifiers đã nhập - phần dưới](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-04a-foundation-identifiers-filled-bottom.png)

### Bước 4: Cấu hình tham số cho quá trình import

Nhấn **Next** để chuyển đến trang **Specify stack details**.

Đặt tên stack:

`tindy-foundation`

Cấu hình các tham số như sau:

| Parameter | Giá trị |
|---|---|
| GitHubOidcProviderMode | Create |
| BackendDeployEnabled | false |
| InfraPlannerEnabled | false |
| LifecycleWorkflowsEnabled | false |
| BackendApiExecutionRoleName | Để trống |
| BackendApiTaskRoleName | Để trống |
| FrontendDeployDistributionId | Để trống |
| FrontendDeployOidcSubject | Để trống |

Ở giai đoạn import, các IAM role phục vụ triển khai được tạm thời vô hiệu hóa vì chưa có đầy đủ thông tin từ Runtime và Frontend.

GitHub OIDC provider hiện có vẫn được đưa vào quá trình import.

![Thông tin stack Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05-foundation-import-stack-details.png)

![Các tham số import Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-05a-foundation-import-parameters.png)

### Bước 5: Kiểm tra và xử lý lỗi template import

Trước khi thực hiện import, kiểm tra lại tên stack, các tham số và danh sách 9 tài nguyên trên trang Review.

![Kiểm tra danh sách tài nguyên Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06-foundation-import-review-resources.png)

![Kiểm tra tham số Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06a-foundation-import-review-parameters.png)

#### Lỗi Outputs trong quá trình import

Trong lần thực hiện đầu tiên, CloudFormation trả về thông báo:

```text
As part of the import operation, you cannot modify or add [Outputs].
```

![Lỗi Outputs khi import Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-06b-foundation-import-outputs-error.png)

Nguyên nhân là template import vẫn chứa phần `Outputs`, trong khi thao tác import này không cho phép thêm hoặc thay đổi Outputs.

Vì vậy, change set chưa được tạo thành công.

**Cách khắc phục:**

1. Xóa toàn bộ phần `Outputs` khỏi `foundation-import.yaml`.
2. Quay lại bước lựa chọn template.
3. Tải lên template đã chỉnh sửa.
4. Kiểm tra lại 9 resource identifiers.
5. Giữ nguyên các tham số đã cấu hình.

Sau khi chỉnh sửa, CloudFormation chấp nhận template mới.

![Kiểm tra tài nguyên sau khi sửa template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07-foundation-import-review-resources.png)

![Kiểm tra tham số sau khi sửa template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07a-foundation-import-review-parameters.png)

Change set được tạo thành công với **9 hành động Import**.

![Chín hành động import tài nguyên Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-07b-foundation-nine-import-actions.png)

### Bước 6: Thực hiện import tài nguyên

Nhấn **Import resources** để bắt đầu.

Theo dõi quá trình thực hiện trong CloudFormation cho đến khi stack `tindy-foundation` đạt trạng thái:

`IMPORT_COMPLETE`

Quá trình import đã hoàn tất thành công.

Tại tab **Resources**, CloudFormation hiển thị 9 tài nguyên đã được đưa vào stack.

![Các tài nguyên Foundation sau khi import](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09-foundation-imported-resources.png)

![Xác minh tài nguyên Foundation đã import](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-09a-foundation-imported-resources.png)

Ở thời điểm này, các tài nguyên đã được CloudFormation quản lý trở lại. Tuy nhiên, cần tiếp tục cập nhật bằng template chính để khôi phục Outputs và cấu hình IAM role.

### Bước 7: Khôi phục template Foundation chính

Mở stack `tindy-foundation` và chọn:

**Stack actions → Create a change set**

![Menu tạo change set cho Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10a-foundation-create-changeset-menu.png)

Chọn **Standard change set**, sau đó:

**Replace existing template → Upload a template file**

Tải lên `foundation.yaml` từ repository hạ tầng.

![Tải lên template Foundation chính](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-10-foundation-main-template.png)

Đặt tên change set:

`tindy-foundation-restore-template`

Cấu hình các tham số:

| Parameter | Giá trị |
|---|---|
| GitHubOidcProviderMode | Create |
| InfraPlannerEnabled | true |
| BackendDeployEnabled | false |
| LifecycleWorkflowsEnabled | false |
| BackendApiExecutionRoleName | Để trống |
| BackendApiTaskRoleName | Để trống |
| FrontendDeployDistributionId | Để trống |
| FrontendDeployOidcSubject | Để trống |

Điểm khác biệt so với giai đoạn import là `InfraPlannerEnabled` được chuyển thành `true`.

Tham số này cho phép tạo IAM role phục vụ GitHub Actions trong quá trình lập kế hoạch thay đổi hạ tầng bằng CloudFormation change set.

Các role liên quan đến backend deployment và lifecycle vẫn chưa được kích hoạt vì cần thêm thông tin từ Runtime và Edge.

![Tham số Foundation chính](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11-foundation-main-parameters.png)

![Tham số Foundation chính - phần dưới](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-11a-foundation-main-parameters-bottom.png)

Tiếp tục cấu hình:

1. Giữ Express mode ở trạng thái tắt.
2. Giữ rollback được bật.
3. Xác nhận các IAM capabilities cần thiết.
4. Tạo change set.

### Bước 8: Kiểm tra và thực thi change set

CloudFormation tạo change set thành công với trạng thái:

`CREATE_COMPLETE — AVAILABLE`

Danh sách thay đổi hiển thị một tài nguyên được bổ sung.

| Tài nguyên | Loại | Hành động |
|---|---|---|
| InfraPlannerRole | AWS::IAM::Role | Add |

Bên cạnh việc tạo `InfraPlannerRole`, quá trình cập nhật còn khôi phục phần `Outputs` từ template Foundation chính.

![Change set của Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12-foundation-main-changeset.png)

Sau khi kiểm tra, thực thi change set.

Tùy chọn **Use deletion policy** được giữ nguyên trong quá trình thực hiện.

![Thực thi change set Foundation](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-12a-foundation-main-execute.png)

Theo dõi Events cho đến khi stack đạt trạng thái:

`UPDATE_COMPLETE`

![Foundation cập nhật thành công](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-13-foundation-main-update-complete.png)

### Bước 9: Kiểm tra Outputs của Foundation

Sau khi cập nhật thành công, mở tab **Outputs** của stack `tindy-foundation`.

CloudFormation hiển thị **14 Outputs**, cung cấp các giá trị tham chiếu cho những stack và workflow sẽ được triển khai tiếp theo.

Các Outputs bao gồm:

- Thông tin tham chiếu của frontend bucket và media bucket.
- Thông tin repository ECR của backend.
- Các application secrets trong Secrets Manager.
- Thông tin GitHub OIDC provider.
- Đường dẫn tài nguyên Data Protection.
- `InfraPlannerRoleArn` phục vụ GitHub Actions.

![Foundation Outputs - phần trên](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14-foundation-main-outputs-top.png)

![Foundation Outputs - phần dưới](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.1/deploy-14a-foundation-main-outputs-bottom.png)

Các IAM role phục vụ backend deployment, frontend deployment và lifecycle sẽ tiếp tục được cấu hình sau khi có đầy đủ thông tin từ Runtime và Edge.

Trước khi chạy các GitHub Actions workflow, cần kiểm tra các biến IAM role trong repository và đối chiếu với Outputs mới của Foundation.

### Kết quả triển khai

Quá trình triển khai Foundation đã hoàn tất thành công.

| Nội dung kiểm tra | Kết quả |
|---|---|
| Import tài nguyên | 9 tài nguyên |
| Trạng thái sau import | `IMPORT_COMPLETE` |
| Khôi phục template chính | Thành công |
| Tạo InfraPlannerRole | Thành công |
| Trạng thái cuối cùng | `UPDATE_COMPLETE` |
| Số lượng Outputs | 14 |

Sau quá trình triển khai, 9 tài nguyên được giữ lại đã trở về dưới sự quản lý của CloudFormation. Template Foundation chính và các Outputs cũng được khôi phục thành công.

**Foundation đã sẵn sàng cho bước tiếp theo: triển khai Network.**
