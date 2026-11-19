
# Chuẩn bị trước khi triển khai

Trước khi triển khai Tindy, cần chuẩn bị quyền truy cập AWS, mã nguồn hạ tầng, container image của backend và các thông tin tham chiếu đến tài nguyên. Toàn bộ hệ thống được triển khai tại khu vực **N. Virginia (`us-east-1`)**, phù hợp với các yêu cầu trong CloudFormation template.

## 1. Công cụ và quyền truy cập

Các công cụ và quyền cần thiết cho quá trình triển khai gồm:

- **AWS Console:** Sử dụng để tải CloudFormation template, xem xét change set, theo dõi Events/Outputs và chụp ảnh minh chứng.
- **AWS CLI:** Cần có profile đã xác thực. Trong workshop này sử dụng profile `tindy-admin`.
- **PowerShell và `curl.exe`:** Dùng để thực hiện các lệnh CLI và kiểm tra HTTP.
- **Quyền truy cập AWS:** Bao gồm các dịch vụ được khai báo trong template như CloudFormation, IAM, EC2 Networking, S3, ECR, Secrets Manager, RDS, ECS, ELB, CloudFront, GuardDuty, EventBridge, SQS, SNS và CloudWatch. Khi được yêu cầu, cần xác nhận IAM capabilities.
- **Email và OAuth:** Cần truy cập hộp thư nhận thông báo để xác nhận SNS. Việc kiểm tra OAuth yêu cầu quyền truy cập cấu hình callback của ứng dụng Google/GitHub để cập nhật hostname mới.

Kiểm tra phiên đăng nhập AWS CLI trên máy triển khai:

```powershell
aws sts get-caller-identity --profile tindy-admin --region us-east-1 --no-cli-pager
```

**📸 Lưu ý:** Kiểm tra và che các thông tin định danh tài khoản AWS trong ảnh chụp màn hình trước khi công bố.

## 2. Mã nguồn và container image

Sử dụng sáu CloudFormation template định dạng YAML trong thư mục `tindy-infrastructure/cloudformation`, đã được giới thiệu tại mục 5.1.

Chuẩn bị container image của backend trong ECR repository `tindy-api` và cố định image digest để đảm bảo API và các tác vụ cơ sở dữ liệu sử dụng cùng một phiên bản image.

Tham số `ApiImage` của Runtime có định dạng:

```text
<account-id>.dkr.ecr.us-east-1.amazonaws.com/tindy-api@sha256:<digest>
```

Trong workshop này, image digest được đối chiếu với ECR và tham số Runtime trước khi dọn dẹp hạ tầng.

Đối với frontend, quá trình triển khai sử dụng release workflow từ frontend repository.

## 3. Thông tin phục vụ triển khai lại

Workshop bắt đầu từ một môi trường AWS đã được triển khai trước đó. Vì vậy, cần sao lưu các thông tin quan trọng trước khi xóa Foundation stack.

Các thông tin được xuất ra gồm:

- Ánh xạ giữa Logical ID và Physical ID của tài nguyên.
- Các tham số cấu hình (Parameters).
- Các giá trị Outputs của stack.
- CloudFormation template đang được triển khai.

Toàn bộ dữ liệu này được lưu trong thư mục `backup/tindy-foundation-before-cleanup` của Worklog.

Có **9 tài nguyên được giữ lại**, bao gồm:

- Hai S3 bucket.
- Media bucket policy.
- ECR repository.
- Bốn application secrets.
- GitHub OIDC.

Sử dụng file `foundation-import.yaml` đã chuẩn bị để đưa các tài nguyên này trở lại quyền quản lý của CloudFormation stack. Sau đó cập nhật bằng `foundation.yaml`.

Quy trình được trình bày chi tiết tại mục **5.3.1**.

## 4. Cấu hình cho phiên triển khai

Các lựa chọn cấu hình được sử dụng trong workshop:

| **Thiết lập** | **Cấu hình** |
| :--- | :--- |
| Database | Tạo cơ sở dữ liệu mới (Fresh), để trống snapshot identifier và thực hiện bootstrap, migrations, seed dữ liệu. |
| Region | `us-east-1`. |
| CloudFront | Tạo distribution mới và cập nhật các tham chiếu hostname. |
| Initial Runtime | Đặt `DesiredCount = 0` cho đến khi cơ sở dữ liệu và secrets sẵn sàng. |
| Traffic | Giữ Edge ở chế độ maintenance cho đến khi API và target group hoạt động ổn định (healthy). |
| Tags | `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. |

Các tham số triển khai được điền dựa trên Outputs của những stack mới tạo.

Đồng thời, cần cập nhật application connection secret được giữ lại để kết nối với cơ sở dữ liệu Fresh.

Các thông tin nhạy cảm như mật khẩu, JWT key và OAuth client secret được quản lý trực tiếp trên AWS.

## 5. Hình ảnh minh chứng và video

Ảnh chụp màn hình trong quá trình thực hiện được lưu tại hai thư mục:

- `static/images/5-Workshop/cleanup`: Hình ảnh quá trình dọn dẹp hạ tầng.
- `static/images/5-Workshop/deploy`: Hình ảnh quá trình triển khai hệ thống.

Các bước hướng dẫn tiếp theo sẽ chỉ rõ thời điểm cần chụp ảnh, bao gồm cấu hình Parameters, xem xét change set, trạng thái hoàn tất và Outputs của stack.

**Video demo** được thực hiện sau khi hoàn tất seed dữ liệu và kiểm tra ứng dụng.

Nội dung demo dự kiến gồm:

1. Đăng nhập vào ứng dụng.
2. Tìm kiếm và khám phá dự án.
3. Gửi đơn ứng tuyển vào dự án.
4. Project Leader xem xét đơn ứng tuyển.

Trong từng mục triển khai, kết quả đã kiểm tra thành công và các bước chưa hoàn thành sẽ được ghi nhận riêng để theo dõi tiến độ thực tế.
