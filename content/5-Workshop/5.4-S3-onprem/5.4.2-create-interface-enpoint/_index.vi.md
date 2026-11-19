
## 5.4.2 — Triển khai Integrations

### Tổng quan

Sau khi triển khai Database, bước tiếp theo là thiết lập các dịch vụ tích hợp để hỗ trợ quét mã độc PDF, xử lý kết quả quét và giám sát hệ thống Tindy.

Template `integrations.yaml` cấu hình các dịch vụ AWS sau:

- **Amazon GuardDuty Malware Protection:** Quét mã độc trong các tệp PDF được tải lên S3.
- **Amazon EventBridge:** Chuyển sự kiện kết quả quét đến hàng đợi xử lý.
- **Amazon SQS:** Lưu trữ thông báo kết quả quét để API worker tiếp nhận.
- **Dead-letter Queue (DLQ):** Lưu các thông báo không được xử lý thành công theo chính sách redrive.
- **Amazon SNS:** Gửi thông báo cảnh báo qua email.
- **Amazon CloudWatch:** Theo dõi các chỉ số vận hành và phát hiện sự cố.
- **IAM và Queue Policies:** Cấp quyền cần thiết cho các dịch vụ tích hợp hoạt động.

Quá trình triển khai gồm hai giai đoạn:

1. Tạo Integrations stack, kích hoạt quét mã độc và xác nhận SNS.
2. Cập nhật giám sát ALB/API sau khi Runtime và Edge được triển khai.

### Bước 1: Tải lên Integrations Template

Truy cập **AWS Management Console** và chọn khu vực **US East (N. Virginia) — us-east-1**.

Đi đến:

**CloudFormation → Stacks → Create stack → With new resources (standard)**

Tại mục **Specify template**:

1. Chọn **Choose an existing template**.
2. Chọn **Upload a template file**.
3. Tải lên tệp `integrations.yaml` từ repository hạ tầng.
4. Nhấn **Next**.

![Tải lên Integrations template](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-23-integrations-template.png)

### Bước 2: Cấu hình Integrations Stack

Tại trang **Specify stack details**, nhập tên stack:

`tindy-integrations`

Cấu hình các tham số:

| Tham số | Giá trị |
|---|---|
| `MediaBucketName` | Lấy từ Foundation Outputs |
| `ScanAlarmEmail` | Email nhận thông báo cảnh báo |
| `AlbMetricLoadBalancer` | Để trống |
| `AlbMetricTargetGroup` | Để trống |

`MediaBucketName` phải khớp với media bucket đã được tạo trong Foundation.

Tham số `ScanAlarmEmail` được sử dụng để thiết lập SNS email subscription. Giá trị email được template che bằng thuộc tính `NoEcho`.

Hai tham số ALB metric dimensions được để trống vì Runtime chưa tạo ALB và target group mới.

Sau khi Runtime được triển khai, các tham số này sẽ được cập nhật thông qua CloudFormation change set.

![Cấu hình tham số Integrations](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24-integrations-parameters.png)

### Bước 3: Cấu hình Stack Options

Tiếp tục đến trang cấu hình stack và thêm các tags:

| Khóa (Key) | Giá trị (Value) |
|---|---|
| Project | Tindy |
| Environment | tindy |
| Owner | TindyTeam |

Các tags giúp nhận diện và quản lý những tài nguyên AWS thuộc dự án Tindy.

![Cấu hình tags cho Integrations](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24a-integrations-tags.png)

Giữ nguyên các tùy chọn:

- **Express mode:** Tắt.
- **Rollback:** Bật.
- **Deletion policy:** Chọn Use deletion policy.

![Tùy chọn Integrations stack](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24b-integrations-options.png)

Do template tạo các tài nguyên liên quan đến IAM, cần xác nhận IAM capabilities trước khi triển khai.

![Xác nhận IAM capabilities](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-24c-integrations-iam-capabilities.png)

Kiểm tra lại template, tham số và cấu hình trước khi xác nhận tạo stack.

### Bước 4: Triển khai và kiểm tra Integrations

Xác nhận tạo stack trên CloudFormation.

CloudFormation bắt đầu triển khai các tài nguyên GuardDuty, SQS, SNS, EventBridge và những thành phần liên quan.

Mở tab **Events** để theo dõi quá trình triển khai.

Stack `tindy-integrations` đã được tạo thành công với trạng thái:

`CREATE_COMPLETE`

![Integrations triển khai thành công](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-25-integrations-complete.png)

Sau khi hoàn tất, mở tab **Outputs**.

CloudFormation hiển thị **7 Outputs**, bao gồm:

| Output | Chức năng |
|---|---|
| `GuardDutyProtectionPlanArn` | ARN của GuardDuty Malware Protection plan. |
| Protection Plan ID | Mã định danh của protection plan. |
| `GuardDutyQueueUrl` | URL hàng đợi SQS tiếp nhận kết quả quét. |
| Queue ARN | ARN của hàng đợi xử lý kết quả quét. |
| DLQ URL | URL của dead-letter queue. |
| DLQ Alarm Name | Tên CloudWatch alarm giám sát DLQ. |
| SNS Topic ARN | ARN của SNS topic dùng để gửi cảnh báo. |

Hai Outputs quan trọng cho Runtime là:

- `GuardDutyProtectionPlanArn`
- `GuardDutyQueueUrl`

![Bảy Outputs của Integrations](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-26-integrations-outputs.png)

### Bước 5: Xác nhận SNS Email Subscription

Sau khi Integrations stack được tạo, Amazon SNS gửi email yêu cầu xác nhận subscription đến địa chỉ đã cấu hình.

Mở email có tiêu đề:

**AWS Notification - Subscription Confirmation**

Kiểm tra SNS topic:

`tindy-integrations-scan-alerts`

Sau đó nhấn **Confirm subscription**.

![Email xác nhận SNS subscription](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-27-integrations-sns-email.png)

Trang xác nhận SNS được mở trong trình duyệt.

Nhấn **Confirm Subscription** để hoàn tất.

![Trang xác nhận SNS subscription](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-27a-integrations-sns-confirm-page.png)

Sau khi xác nhận thành công, hệ thống hiển thị:

`Subscription confirmed!`

Subscription ARN cũng được cung cấp để xác minh trạng thái đăng ký.

![SNS subscription đã được xác nhận](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-27b-integrations-sns-confirmed.png)

SNS subscription đã được xác nhận thành công và sẵn sàng tiếp nhận các thông báo cảnh báo từ hệ thống.

### Bước 6: Kiểm tra GuardDuty Malware Protection

Sau khi tạo stack và xác nhận SNS, tiếp tục kiểm tra cấu hình GuardDuty.

Truy cập:

**Amazon GuardDuty → Malware Protection → S3 → View feature**

Tại trang tổng quan, GuardDuty hiển thị một S3 bucket đang được bảo vệ.

![Trang tổng quan GuardDuty Malware Protection](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-28a-guardduty-malware-protection.png)

Mở mục **Protected buckets** để kiểm tra media bucket được sử dụng bởi Tindy.

Danh sách cho thấy:

- Media bucket từ Foundation đã được đưa vào phạm vi bảo vệ.
- Protection plan ở trạng thái **Active**.
- Có **2 prefix** được cấu hình.

![Kiểm tra S3 bucket được GuardDuty bảo vệ](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-28b-guardduty-protected-bucket.png)

Mở chi tiết bucket và kiểm tra:

| Cấu hình | Kết quả |
|---|---|
| Protection Status | Active |
| Tag scanned objects | Yes |
| Số prefix được bảo vệ | 2 |

![GuardDuty Active và bật object tagging](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-28-integrations-guardduty-active.png)

AWS CLI cũng được sử dụng để xác minh cấu hình.

Kết quả xác nhận:

- Protection Plan ID: `9cd0f69716d5ca261abc`
- Protection plan status: `ACTIVE`
- Không có status reason báo lỗi.
- Object tagging: `ENABLED`.

Hai S3 prefixes được bảo vệ là:

- `certificates/pdf/`
- `ai-input/pdf/`

AWS CLI đồng thời xác nhận SNS email subscription đã được đăng ký thành công, với ARN khớp với trang xác nhận.

Như vậy, cấu hình GuardDuty, kết quả Outputs và SNS subscription đã được kiểm tra.

Việc thử nghiệm tải PDF thông qua ứng dụng sẽ được thực hiện sau khi API và database schema sẵn sàng.

### Bước 7: Cập nhật Monitoring sau khi Runtime hoạt động

Sau khi Runtime và Edge được triển khai, Integrations cần được cập nhật để bổ sung giám sát ALB và API.

Trước khi cập nhật, kiểm tra stack `tindy-integrations`.

Stack vẫn ở trạng thái `CREATE_COMPLETE` và có 7 Outputs, bao gồm các tài nguyên GuardDuty, SQS, DLQ và SNS đã tạo trước đó.

![Integrations trước khi cập nhật Monitoring](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68a-integrations-before-monitoring.png)

Thông tin ALB và target group mới được lấy từ việc kiểm tra các tài nguyên CloudFormation của stack `tindy-runtime`.

Mở stack `tindy-integrations` và chọn:

**Stack actions → Create a change set**

Cấu hình:

- **Change set type:** Standard change set.
- **Template:** Use existing template.
- **Change set name:** `tindy-integrations-enable-alb-alarms`

Việc sử dụng template hiện tại cho phép kích hoạt các CloudWatch alarms có điều kiện bằng cách cập nhật tham số.

![Sử dụng template hiện tại để tạo change set](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68b-monitoring-existing-template.png)

### Bước 8: Cấu hình ALB và Target Group Dimensions

Tại phần tham số, cập nhật hai giá trị:

| Tham số | Giá trị |
|---|---|
| `AlbMetricLoadBalancer` | `app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4` |
| `AlbMetricTargetGroup` | `targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa` |

Các giá trị này được lấy từ ARN suffix của Internal ALB và API Target Group trong Runtime.

**Lưu ý:** Đây là các giá trị đã được xác minh cho lần triển khai này. Nếu triển khai lại Runtime và các ARN thay đổi, cần lấy giá trị mới từ tài nguyên thực tế.

#### Cấu hình ALB Metric

Nhập giá trị:

`app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4`

Tham số này giúp CloudWatch xác định đúng Application Load Balancer cần giám sát.

![Cấu hình ALB metric dimension](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68c-monitoring-alb-dimension.png)

#### Cấu hình Target Group Metric

Nhập giá trị:

`targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa`

Tham số này giúp CloudWatch theo dõi các API targets, bao gồm số lượng healthy targets và lỗi HTTP 5xx.

![Cấu hình Target Group metric dimension](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-68d-monitoring-target-group-dimension.png)

Giữ nguyên các tham số `MediaBucketName`, `ScanAlarmEmail` và những cấu hình khác.

SNS email subscription đã xác nhận trước đó tiếp tục được sử dụng để nhận thông báo.

### Bước 9: Kiểm tra Monitoring Change Set

Sau khi cập nhật tham số, tiếp tục qua các bước cấu hình và xác nhận IAM capabilities nếu được yêu cầu.

Tạo change set và chờ trạng thái:

`CREATE_COMPLETE — AVAILABLE`

Change set `tindy-integrations-enable-alb-alarms` được tạo thành công với **4 thay đổi**:

| Logical ID | Hành động | Loại tài nguyên |
|---|---|---|
| `AlbGenerated5xxAlarm` | Add | `AWS::CloudWatch::Alarm` |
| `AlbNoHealthyTargetsAlarm` | Add | `AWS::CloudWatch::Alarm` |
| `ApiTarget5xxAlarm` | Add | `AWS::CloudWatch::Alarm` |
| `ScanAlarmTopicPolicy` | Modify | `AWS::SNS::TopicPolicy` |

Ba CloudWatch alarms mới được bổ sung để theo dõi ALB và API.

SNS Topic Policy được cập nhật tại chỗ, không thay thế tài nguyên, để cho phép gửi thông báo từ các alarm mới.

![Kiểm tra Monitoring change set](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-69-monitoring-changeset.png)

Các alarm được cấu hình theo những điều kiện:

| Alarm | Điều kiện cảnh báo |
|---|---|
| `AlbNoHealthyTargetsAlarm` | Giá trị nhỏ nhất của HealthyHostCount dưới 1 trong 3 chu kỳ liên tiếp, mỗi chu kỳ 60 giây. Dữ liệu thiếu được xem là vi phạm ngưỡng. |
| `AlbGenerated5xxAlarm` | Tổng số phản hồi ELB HTTP 5xx từ 5 trở lên trong 300 giây. |
| `ApiTarget5xxAlarm` | Tổng số phản hồi HTTP 5xx từ API targets từ 5 trở lên trong 300 giây. |

### Bước 10: Thực thi Monitoring Change Set

Sau khi kiểm tra danh sách thay đổi, chọn:

**Execute change set**

Giữ nguyên cấu hình rollback và **Use deletion policy**, sau đó xác nhận thực thi.

![Xác nhận thực thi Monitoring change set](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-70a-monitoring-execute.png)

Theo dõi quá trình cập nhật CloudFormation.

Sau khi thực thi, stack `tindy-integrations` đạt trạng thái:

`UPDATE_COMPLETE`

![Integrations cập nhật Monitoring thành công](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-70b-monitoring-update-complete.png)

Điều này xác nhận quá trình bổ sung CloudWatch alarms và cập nhật SNS Topic Policy đã hoàn thành.

### Bước 11: Kiểm tra CloudWatch Alarms

Sau khi cập nhật, truy cập:

**CloudWatch → Alarms → All alarms**

Chọn khu vực **US East (N. Virginia)** và tìm kiếm:

`tindy-integrations`

CloudWatch hiển thị **4 alarms**:

- `ApiTarget5xxAlarm`
- `AlbGenerated5xxAlarm`
- `AlbNoHealthyTargetsAlarm`
- `ScanDeadLetterAlarm`

Tại thời điểm kiểm tra, cả 4 alarms đều ở trạng thái:

`OK`

Đồng thời, tất cả đều hiển thị:

`Actions enabled`

![Bốn CloudWatch alarms ở trạng thái OK](/my_aws_workshop/images/5-Workshop/5.4-database-integrations/5.4.2/deploy-71-monitoring-four-alarms-ok.png)

Kết quả này xác nhận các alarm đã được tạo và đang hoạt động theo trạng thái ghi nhận tại thời điểm chụp.

Tuy nhiên, việc chủ động tạo lỗi để kiểm tra khả năng gửi thông báo cảnh báo chưa được thực hiện.

### Kết quả triển khai

Quá trình triển khai và cập nhật Integrations đã hoàn tất thành công.

| Nội dung kiểm tra | Kết quả |
|---|---|
| Stack name | `tindy-integrations` |
| Trạng thái triển khai ban đầu | `CREATE_COMPLETE` |
| Số lượng Outputs | 7 |
| GuardDuty Protection Plan | `ACTIVE` |
| Object Tagging | `ENABLED` |
| PDF prefixes được bảo vệ | 2 |
| SNS Email Subscription | Đã xác nhận |
| Monitoring Change Set | `CREATE_COMPLETE — AVAILABLE` |
| Số thay đổi trong Change Set | 4 |
| Trạng thái sau cập nhật | `UPDATE_COMPLETE` |
| CloudWatch Alarms | 4 alarms |
| Trạng thái các alarm tại thời điểm kiểm tra | `OK` |
| Alarm Actions | Enabled |
| Kiểm thử gửi cảnh báo bằng cách tạo lỗi | Chưa thực hiện |

Sau khi triển khai, Integrations đã cung cấp các thành phần cần thiết để quét mã độc PDF, chuyển kết quả đến API worker và giám sát hoạt động của ALB, API cùng hàng đợi DLQ.

Các cấu hình GuardDuty, SNS và CloudWatch đã được xác minh. Việc kiểm thử tải PDF và xử lý kết quả quét qua ứng dụng sẽ được thực hiện sau khi API và database schema sẵn sàng.

**Integrations đã được triển khai thành công và hoàn tất cập nhật Monitoring sau khi Runtime hoạt động.**
