---
title: "TRIỂN KHAI INTEGRATIONS"
date: 2026-11-19
weight: 2
chapter: false
pre: "<b> 5.4.2 </b>"
---

`integrations.yaml` cấu hình GuardDuty Malware Protection để quét PDF trên S3, gửi kết quả qua EventBridge, SQS và một DLQ. Template này cũng định nghĩa các IAM policy và queue policy, cảnh báo CloudWatch và các tài nguyên thông báo qua email SNS tùy chọn.

## Tạo stack

1. Tại `us-east-1`, chọn **CloudFormation → Create stack → With new resources (standard)** và tải lên `integrations.yaml`.

**📸 Chụp ảnh tên tệp đã chọn.**

[Tải lên integrations.yaml](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-23-integrations-template.png)

2. Đặt tên stack là `tindy-integrations`. Đặt `MediaBucketName` từ Foundation Outputs và cung cấp người nhận cảnh báo trong `ScanAlarmEmail`. Để trống `AlbMetricLoadBalancer` và `AlbMetricTargetGroup` cho đến khi Runtime tạo ALB và target group mới, sau đó cập nhật cả hai dimension thông qua một change set.

**📸 Chụp ảnh tên stack và các tham số. Kiểm tra khả năng hiển thị email trước khi công bố.**

[Tham số Integrations](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24-integrations-parameters.png)

3. Thêm các tag `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Giữ Express mode ở trạng thái tắt và rollback ở trạng thái bật, xác nhận IAM capabilities, xem xét và tạo stack.
4. Chờ `CREATE_COMPLETE` và ghi lại `GuardDutyProtectionPlanArn` và `GuardDutyQueueUrl` từ Outputs cho Runtime.
5. Trong **GuardDuty → Malware Protection for S3**, xác minh protection plan mới ở trạng thái **Active**, việc gắn tag được bật và các prefix bao gồm `certificates/pdf/` và `ai-input/pdf/`.
6. Đối với email đã cấu hình, xác nhận SNS subscription từ email nhận được và xác minh trạng thái đã xác nhận trong SNS.

**📸 Chụp ảnh trạng thái stack hoàn tất, Outputs, protection plan ở trạng thái Active và subscription đã xác nhận.**

## Tiến độ đã xác minh

Template và tên stack chính xác đã được chọn. Media bucket khớp với Foundation và cả hai ALB metric dimension đều để trống. Một địa chỉ email đã được nhập và được che bởi thiết lập NoEcho của template.

Các tag Project, Environment và Owner đã được cấu hình. Express mode được tắt, rollback và Use deletion policy được chọn, và IAM capabilities đã được xác nhận.

[Tags của Integrations](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24a-integrations-tags.png)

[Tùy chọn Integrations](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24b-integrations-options.png)

[Xác nhận IAM của Integrations](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-24c-integrations-iam-capabilities.png)

## Kết quả tạo stack và xác nhận SNS

Stack `tindy-integrations` đạt trạng thái **CREATE_COMPLETE** với **Outputs (7)**.

[Integrations hoàn tất](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-25-integrations-complete.png)

[Bảy Outputs của Integrations](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-26-integrations-outputs.png)

Runtime sẽ sử dụng `GuardDutyProtectionPlanArn` và `GuardDutyQueueUrl`. Các Outputs khác cung cấp plan ID, queue ARN, DLQ URL, tên DLQ alarm và SNS topic ARN.

1. Mở **AWS Notification - Subscription Confirmation**, xác minh topic `tindy-integrations-scan-alerts` và nhấn **Confirm subscription**.

[Email xác nhận SNS](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-27-integrations-sns-email.png)

2. Trên trang SNS, nhấn **Confirm Subscription**.

[Trang xác nhận SNS](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-27a-integrations-sns-confirm-page.png)

3. Xác minh **Subscription confirmed!** và subscription ARN.

[SNS đã xác nhận](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-27b-integrations-sns-confirmed.png)

Bằng chứng xác nhận SNS đã hoàn tất. Xác minh trạng thái protection plan **Active**, việc gắn tag và cả hai PDF prefix để hoàn tất xác minh Integrations.

## Xác minh bằng AWS CLI

Một lần kiểm tra đọc dữ liệu tiếp theo xác nhận protection plan `9cd0f69716d5ca261abc` báo cáo trạng thái `ACTIVE`, không có status reason, tagging `ENABLED`, và bảo vệ các prefix `certificates/pdf/` và `ai-input/pdf/` của media bucket. SNS trả về ARN của email subscription đã xác nhận, khớp với trang xác nhận.

Cấu hình tài nguyên của phần 5.4.2 đã được xác minh. Các ảnh chụp chi tiết GuardDuty bên dưới xác nhận trạng thái trên Console. Các cảnh báo ALB/API sẽ được cấu hình sau khi Runtime cung cấp ALB và target group mới.

## Xác minh GuardDuty trên Console

1. Mở **GuardDuty → Malware Protection → S3 → View feature**. Một bucket được bảo vệ.

[Tổng quan Malware Protection](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-28a-guardduty-malware-protection.png)

2. Trong **Protected buckets**, xác minh Foundation media bucket và protection plan mới ở trạng thái **Active**. Danh sách hiển thị **2 prefix**.

[Bucket được bảo vệ](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-28b-guardduty-protected-bucket.png)

3. Mở chi tiết bucket. Xác nhận **Status = Active** và **Tag scanned objects = Yes**. CLI đã xác minh riêng `certificates/pdf/` và `ai-input/pdf/`.

[Protection plan Active và tagging](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-28-integrations-guardduty-active.png)

Việc xác minh phần 5.4.2 đã hoàn tất, bao gồm hoàn tất stack, Outputs, SNS subscription đã xác nhận và GuardDuty protection plan ở trạng thái Active. Việc kiểm thử tải lên PDF được thực hiện sau khi API và schema sẵn sàng.

## Bổ sung monitoring sau khi Runtime trở nên Healthy

Sau khi Runtime và Edge hoạt động, một lần kiểm tra chỉ đọc các tài nguyên CloudFormation của `tindy-runtime` cung cấp các ARN hiện tại. Các hướng dẫn cập nhật sau đây đang chờ bằng chứng thực thi.

Tạo một Standard change set trên `tindy-integrations`, chọn Use existing template và đặt tên là `tindy-integrations-enable-alb-alarms`. Chỉ cập nhật:

| **Tham số** | **Giá trị** |
| :--- | :--- |
| `AlbMetricLoadBalancer` | `app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4` |
| `AlbMetricTargetGroup` | `targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa` |

Các hậu tố ARN này cung cấp các CloudWatch dimension. Giữ nguyên MediaBucketName và ScanAlarmEmail hiện có. SNS email subscription đã được xác nhận trước đó tiếp tục nhận thông báo.

| **Cảnh báo** | **Điều kiện của template** |
| :--- | :--- |
| `AlbNoHealthyTargetsAlarm` | Giá trị tối thiểu của HealthyHostCount dưới 1 trong ba khoảng thời gian 60 giây, với dữ liệu thiếu được xem là vi phạm ngưỡng. |
| `AlbGenerated5xxAlarm` | Tổng ít nhất năm phản hồi ELB 5xx trong 300 giây. |
| `ApiTarget5xxAlarm` | Tổng ít nhất năm phản hồi target 5xx trong 300 giây. |

Dự kiến có ba thao tác Add cảnh báo và một thao tác Modify đối với `ScanAlarmTopicPolicy` để cho phép thông báo từ các cảnh báo mới. **📸 Chụp ảnh Parameters và Resource changes tại CREATE_COMPLETE / AVAILABLE** trước khi thực thi. Sau khi cập nhật, kiểm tra các cảnh báo CloudWatch và dimension, đồng thời ghi lại trạng thái thực tế của chúng.

### Cấu hình monitoring đã ghi nhận

Ảnh chụp trước khi cập nhật ghi lại `tindy-integrations` ở trạng thái CREATE_COMPLETE với bảy Outputs, bao gồm queue, DLQ, GuardDuty protection plan và scan alarm. Các tài nguyên tích hợp này tồn tại trước khi thêm các cảnh báo ALB.

[Integrations trước khi cập nhật monitoring](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68a-integrations-before-monitoring.png)

Trình hướng dẫn chọn **Standard change set** và **Use existing template** cho `tindy-integrations`. Cách này sử dụng template hiện tại để kích hoạt các cảnh báo có điều kiện thông qua các tham số.

[Monitoring sử dụng template hiện có](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68b-monitoring-existing-template.png)

`AlbMetricLoadBalancer` được đặt thành `app/tindy--Inter-KlwCTvMOHRHo/e543836fc18c01a4`, khớp với hậu tố ARN của InternalAlb hiện tại. CloudWatch sử dụng dimension này để xác định load balancer được giám sát.

[ALB metric dimension](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68c-monitoring-alb-dimension.png)

`AlbMetricTargetGroup` được đặt thành `targetgroup/tindy-ApiTa-VBQVMEGFV6ZL/9ba7ccbfe0bd71fa`, khớp với hậu tố ARN của ApiTargetGroup hiện tại. Dimension này xác định các API target cho cảnh báo HealthyHostCount và target 5xx.

[Target group metric dimension](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-68d-monitoring-target-group-dimension.png)

Cả hai đầu vào khớp với kết quả kiểm tra tài nguyên AWS chỉ đọc. Tiếp tục với Next, giữ nguyên các đầu vào và tùy chọn khác, xác nhận IAM capability nếu được yêu cầu và tạo change set. **📸 Chụp ảnh Resource changes tại CREATE_COMPLETE / AVAILABLE.** Việc tạo change set và thực thi cập nhật monitoring vẫn chưa được xác minh tại mốc này.

### Xem xét change set monitoring

Change set `tindy-integrations-enable-alb-alarms` đạt trạng thái **CREATE_COMPLETE**, Execution status **AVAILABLE**, với chính xác bốn thay đổi:

| **Logical ID** | **Action** | **Loại tài nguyên** | **Replacement** |
| :--- | :--- | :--- | :--- |
| `AlbGenerated5xxAlarm` | Add | `AWS::CloudWatch::Alarm` | — |
| `AlbNoHealthyTargetsAlarm` | Add | `AWS::CloudWatch::Alarm` | — |
| `ApiTarget5xxAlarm` | Add | `AWS::CloudWatch::Alarm` | — |
| `ScanAlarmTopicPolicy` | Modify | `AWS::SNS::TopicPolicy` | False |

Ba cảnh báo được thêm vào để giám sát ALB/API. SNS policy được cập nhật tại chỗ để cho phép thông báo từ các cảnh báo mới. Phạm vi thay đổi khớp với hướng dẫn. Việc thực thi vẫn chưa được xác minh tại mốc này.

[Change set monitoring đã được xem xét](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-69-monitoring-changeset.png)

Chọn **Execute change set**, giữ nguyên cơ chế rollback hiện tại và Use deletion policy nếu được nhắc, sau đó xác nhận. Chờ stack đạt **UPDATE_COMPLETE**. **📸 Chụp ảnh stack đã cập nhật.** Mở **CloudWatch → Alarms → All alarms**, Region N. Virginia, và tìm kiếm `tindy-integrations`. **📸 Chụp ảnh cả bốn cảnh báo**, bao gồm ba cảnh báo mới được thêm và ScanDeadLetterAlarm hiện có, cùng với trạng thái thực tế của chúng. Nếu các cảnh báo mới vẫn ở trạng thái INSUFFICIENT_DATA, làm mới sau vài phút. Đối với ALARM, kiểm tra State reason và metrics trước khi đưa ra kết luận.

### Kết quả monitoring đã xác minh

Quá trình thực thi giữ nguyên rollback và Use deletion policy. Sau khi thực thi, `tindy-integrations` đạt trạng thái **UPDATE_COMPLETE**.

[Xác nhận thực thi monitoring](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-70a-monitoring-execute.png)

[Cập nhật monitoring của Integrations hoàn tất](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-70b-monitoring-update-complete.png)

CloudWatch hiển thị cả bốn cảnh báo ở trạng thái **OK** với **Actions enabled**: ApiTarget5xxAlarm, AlbGenerated5xxAlarm, AlbNoHealthyTargetsAlarm và ScanDeadLetterAlarm. Điều này ghi nhận trạng thái của chúng tại thời điểm chụp ảnh. Việc tạo lỗi có chủ đích để xác minh khả năng gửi thông báo cảnh báo chưa được thực hiện.

[Bốn cảnh báo OK với actions enabled](/aws_my_workshop/images/5-Workshop/5.4-S3-onprem/5.4.2/deploy-71-monitoring-four-alarms-ok.png)
