
# YÊU CẦU TRƯỚC KHI TRIỂN KHAI

Chuẩn bị quyền truy cập AWS, mã nguồn hạ tầng, image backend và các tham chiếu tài nguyên để triển khai Tindy tại N. Virginia (`us-east-1`), phù hợp với các ràng buộc của template.

## 1. Công cụ và quyền truy cập

- AWS Console để tải lên các template, xem xét change set, Events/Outputs và ảnh chụp màn hình.
- Một AWS CLI profile đã được xác thực. Phiên triển khai này sử dụng `tindy-admin`.
- PowerShell và `curl.exe` để kiểm tra CLI và HTTP.
- Quyền đối với các tài nguyên được định nghĩa trong các template, bao gồm CloudFormation, IAM, mạng EC2, S3, ECR, Secrets Manager, RDS, ECS, ELB, CloudFront, GuardDuty, EventBridge, SQS, SNS và CloudWatch. Xác nhận các IAM capabilities khi được yêu cầu.
- Quyền truy cập vào hộp thư nhận thông báo để xác nhận SNS. Việc xác minh OAuth yêu cầu quyền truy cập vào cài đặt callback của ứng dụng Google/GitHub cho hostname mới.

Xác minh phiên CLI trên máy triển khai:

```powershell
aws sts get-caller-identity --profile tindy-admin --region us-east-1 --no-cli-pager
```

📸 **Kiểm tra các mã định danh tài khoản trong ảnh chụp màn hình xác minh danh tính trước khi công bố.**

## 2. Mã nguồn và image phát hành

Sử dụng sáu template YAML trong `tindy-infrastructure/cloudformation`, được mô tả ở phần 5.1. Chuẩn bị image backend trong repository ECR `tindy-api` và cố định digest của image để thực thi API và các tác vụ cơ sở dữ liệu một cách nhất quán.

Runtime `ApiImage` sử dụng `<account-id>.dkr.ecr.us-east-1.amazonaws.com/tindy-api@sha256:<digest>`. Phiên triển khai này đã kiểm tra digest đối chiếu với ECR và tham số Runtime trước khi dọn dẹp. Việc triển khai frontend sử dụng quy trình phát hành của repository frontend.

## 3. Ánh xạ triển khai lại

Phiên thực hành bắt đầu với một môi trường hiện có. Trước khi xóa Foundation, các ánh xạ từ tài nguyên logic sang tài nguyên vật lý, parameters, Outputs và template đã triển khai được xuất vào `backup/tindy-foundation-before-cleanup` trong Worklog.

Chín tài nguyên được giữ lại bao gồm hai S3 bucket, media bucket policy, ECR, bốn application secret và GitHub OIDC. Sử dụng `foundation-import.yaml` đã chuẩn bị để khôi phục quyền quản lý tài nguyên của stack, sau đó cập nhật bằng `foundation.yaml` như được ghi lại trong phần 5.3.1.

## 4. Các lựa chọn cho phiên triển khai

| **Thiết lập** | **Lựa chọn** |
| :--- | :--- |
| Cơ sở dữ liệu | Fresh, để trống mã định danh snapshot, bootstrap/migrations/seed. |
| Region | `us-east-1`. |
| CloudFront | Distribution mới và các tham chiếu hostname được cập nhật. |
| Runtime ban đầu | `DesiredCount = 0` cho đến khi cơ sở dữ liệu và secrets sẵn sàng. |
| Lưu lượng truy cập | Edge ở chế độ bảo trì cho đến khi API và target group hoạt động ổn định. |
| Tags | `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. |

Điền các tham số từ Outputs của các stack mới. Cập nhật application connection secret được giữ lại cho cơ sở dữ liệu Fresh. Quản lý mật khẩu, JWT keys và OAuth client secrets trong AWS.

## 5. Ảnh chụp màn hình và video

Ảnh chụp màn hình được lưu trong `static/images/5-Workshop/cleanup` và `static/images/5-Workshop/deploy`. Các bước hướng dẫn xác định các thời điểm cần ghi lại parameters, change set, quá trình hoàn thành và Outputs.

Quay video sau khi seed dữ liệu và xác minh ứng dụng. Quy trình dự kiến bao gồm đăng nhập, khám phá dự án, ứng tuyển và đánh giá của trưởng nhóm. Mỗi trang triển khai phân biệt các kết quả đã được xác minh với các bước đang chờ thực hiện.
