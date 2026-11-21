---
title: "KÍCH HOẠT API VÀ XÁC MINH RUNTIME"
date: 2026-11-22
weight: 5
chapter: false
pre: "<b> 5.5.5 </b>"
---

Bootstrap, migrations và seed đã hoàn tất với exit code 0 và các log hoàn tất. Giai đoạn này cấu hình nguồn lưu lượng ALB bằng CloudFront service security group và khởi chạy một API task. Edge vẫn ở chế độ maintenance cho đến khi trạng thái sức khỏe của Runtime được xác minh.

## 1. Xác định CloudFront service security group

Trong EC2 → Security Groups, ảnh chụp màn hình xác nhận `CloudFront-VPCOrigins-Service-SG` có ID **`sg-0ae54c77731609b33`** và thuộc VPC **`vpc-0209344341dce92fb`**. Đây là ID hiện tại trong môi trường được xây dựng lại.

[CloudFront service security group](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-53-cloudfront-service-security-group.png)

Ảnh chụp màn hình hiển thị không có mục quyền inbound nào và có một mục quyền outbound. Runtime sử dụng group này làm nguồn cho quy tắc ALB ingress. Bước này không yêu cầu chỉnh sửa trực tiếp group do CloudFront quản lý.

## 2. Tạo change set kích hoạt API

Mở **CloudFormation → tindy-runtime → Stack actions → Create a change set**. Chọn **Standard change set**, **Use existing template**, sau đó nhấn Next. Đặt tên là `tindy-runtime-start-api`.

| **Tham số** | **Giá trị** | **Mục đích** |
| :--- | :--- | :--- |
| `CloudFrontServiceSecurityGroupId` | `sg-0ae54c77731609b33` | Giới hạn lưu lượng ALB chỉ đến từ CloudFront VPC Origin service SG. |
| `DesiredCount` | `1` | Khởi chạy một API task sau khi chuẩn bị cơ sở dữ liệu. |

Giữ nguyên tất cả các tham số khác, bao gồm `AiPdfUploadsEnabled = false` và CloudFront origin prefix list hiện tại. Giữ nguyên các tùy chọn hiện tại, xác nhận IAM capability nếu được yêu cầu, sau đó tạo change set.

**📸 Chụp ảnh cả hai tham số đã thay đổi và Resource changes khi change set đạt CREATE_COMPLETE / AVAILABLE.** Khi chỉ thay đổi hai đầu vào này, dự kiến sẽ thay thế `AlbIngressFromCloudFront` và sửa đổi tại chỗ `ApiService`. Xem xét các thay đổi thực tế trước khi thực thi. Việc tạo change set và thực thi API vẫn chưa được xác minh tại mốc này.

## 3. Quá trình tạo và xem xét change set đã ghi lại

Runtime ở trạng thái UPDATE_COMPLETE trước khi kích hoạt API. Điều này ghi lại lần cập nhật trước và chưa xác nhận API đã được khởi chạy.

[Runtime trước khi kích hoạt API](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54a-runtime-before-activation.png)

Standard change set và Use existing template đã được chọn để giữ nguyên template và cập nhật các tham số.

[Sử dụng template hiện có](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54b-start-api-existing-template.png)

Cả hai đầu vào đã thay đổi đều khớp với hướng dẫn: `CloudFrontServiceSecurityGroupId = sg-0ae54c77731609b33` và `DesiredCount = 1`. Group cung cấp nguồn ALB ingress, trong khi DesiredCount yêu cầu ECS duy trì một API task.

[Tham số CloudFront service SG](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54c-start-api-cloudfront-sg.png)

[DesiredCount bằng một](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54d-start-api-desired-one.png)

IAM capability đã được xác nhận trong trình hướng dẫn. Phần xem xét thực tế bên dưới liệt kê hai tài nguyên được thay đổi.

[Xác nhận IAM capability](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-54e-start-api-iam.png)

Change set `tindy-runtime-start-api` đạt trạng thái **CREATE_COMPLETE**, Execution status **AVAILABLE**, với Express mode Disabled và Roll back all stack resources được cấu hình.

| **Logical ID** | **Action** | **Replacement** | **Ý nghĩa** |
| :--- | :--- | :--- | :--- |
| `AlbIngressFromCloudFront` | Modify | True | Thay thế tài nguyên quy tắc ingress để sử dụng CloudFront service SG. ReplaceAndDelete áp dụng cho quy tắc trước đó. |
| `ApiService` | Modify | False | Cập nhật ECS service tại chỗ với số lượng task mới. |

[Xem xét change set kích hoạt API](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-55-start-api-changeset.png)

Các thay đổi khớp với hai tham số đã cập nhật. Việc thực thi và trạng thái sức khỏe API vẫn chưa được xác minh tại mốc này.

## 4. Thực thi và xác minh

Chọn **Execute change set**, giữ nguyên cơ chế rollback đã cấu hình và chọn **Use deletion policy** nếu được yêu cầu, sau đó xác nhận thực thi. Theo dõi Events cho đến khi stack đạt **UPDATE_COMPLETE**. Nếu cập nhật thất bại, kiểm tra stack Events và ECS service events trước khi tiếp tục.

**📸 Chụp ảnh Runtime UPDATE_COMPLETE sau lần cập nhật này.** Mở **ECS → Clusters → tindy-runtime → Services → tindy-api** và xác minh Desired 1, Running 1, Pending 0 và deployment đã hoàn tất. **📸 Chụp ảnh trạng thái service**. Sau đó xem xét trạng thái sức khỏe của target group và API logs. Giữ Edge ở chế độ maintenance trong quá trình xác minh.

## 5. Kết quả kích hoạt API đã xác minh

Hộp thoại Execute giữ nguyên Roll back all stack resources và Use deletion policy. Change set đã được thực thi.

[Thực thi kích hoạt API](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56a-start-api-execute.png)

Events xác nhận Runtime **UPDATE_COMPLETE** lúc 14:19:50 UTC+07 ngày 18 tháng 11 năm 2026. Sự kiện DELETE_COMPLETE của `AlbIngressFromCloudFront` trong quá trình thay thế quy tắc khớp với thay đổi Replacement True đã được xem xét.

[Cập nhật Runtime API hoàn tất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56b-runtime-api-update-complete.png)

Cluster báo cáo **1 running / 0 pending**. Service `tindy-api` ở trạng thái Active với **1/1 Tasks running** và Last deployment Completed.

[Cluster API đang chạy](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56c-cluster-api-running.png)

[Deployment API service hoàn tất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-56d-api-service-completed.png)

Tổng quan service xác nhận Desired **1**, Running **1**, Pending **0**, task definition `tindy-runtime-api:13`, và Deployment status **Success**. Trạng thái sức khỏe target của load balancer cho container `api:8080` báo cáo **1 Healthy / 0 Unhealthy**. Những kết quả này xác nhận Runtime đã sẵn sàng và các ALB health check đạt yêu cầu. Các yêu cầu API live thông qua CloudFront vẫn chưa được xác minh.

[API service và ALB target Healthy](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-57-api-target-healthy.png)

## Bước tiếp theo

Sao chép `AlbDnsName` và `VpcOriginId` trực tiếp từ **CloudFormation → tindy-runtime → Outputs** để chuẩn bị change set `tindy-edge`. Việc chuyển `EdgeMode` sang `live` được thực hiện trong giai đoạn tiếp theo. Edge vẫn ở chế độ maintenance. Các alarm dimension của Integrations và việc kích hoạt AI PDF vẫn đang chờ các lần cập nhật riêng biệt.

### Edge trước khi kích hoạt live

Ảnh chụp Outputs của `tindy-edge` xác nhận CREATE_COMPLETE, distribution `E39MIF3N8DR3D6`, hostname `d1vcn4g2ifl3k8.cloudfront.net`, và `EdgeMode = maintenance`. Các Runtime API target Healthy không xác nhận việc định tuyến Edge live. Để chuẩn bị các đầu vào live, chọn stack `tindy-runtime` và mở Outputs để sao chép `AlbDnsName` và `VpcOriginId`.

[Edge vẫn ở chế độ maintenance trước khi kích hoạt live](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-58-edge-before-live.png)

## Kích hoạt AI PDF sau khi chuẩn bị schema và monitoring

Migration đã ghi lại `20261112124706_Media_AllowAiPdfQuarantine`, GuardDuty protection plan ở trạng thái Active, queue đã được cấu hình và cả bốn monitoring alarm đều ở trạng thái OK. Tiếp theo, tạo một Standard change set trên `tindy-runtime`, chọn Use existing template, đặt tên là `tindy-runtime-enable-ai-pdf`. Chỉ thay đổi `AiPdfUploadsEnabled` từ false sang true. Giữ DesiredCount 1 và tất cả các tham số khác.

Tham số này ánh xạ đến biến môi trường `GuardDuty__AiPdfUploadsEnabled` của API task definition. Template dự kiến tạo một task definition revision mới và cập nhật ECS service để triển khai revision đó. **📸 Chụp ảnh tham số và Resource changes tại CREATE_COMPLETE / AVAILABLE** trước khi thực thi. Việc kích hoạt AI PDF và tải lên/quét thành công vẫn chưa được xác minh tại mốc này.

### Xem xét AI PDF change set

Trình hướng dẫn đã chọn Standard change set và Use existing template trên `tindy-runtime`. Tham số `AiPdfUploadsEnabled` được đặt thành **true**.

[AI PDF sử dụng template hiện có](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-72a-ai-pdf-existing-template.png)

[Tham số AI PDF đã bật](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-72b-ai-pdf-enabled-parameter.png)

Change set `tindy-runtime-enable-ai-pdf` đạt trạng thái **CREATE_COMPLETE / AVAILABLE** với:

| **Logical ID** | **Action** | **Replacement** | **Ý nghĩa** |
| :--- | :--- | :--- | :--- |
| `ApiService` | Modify | False | Cập nhật service hiện có để triển khai task definition mới. |
| `ApiTaskDefinition` | Modify | True | Đăng ký một task definition revision mới với AI PDF environment flag được bật. ReplaceAndDelete áp dụng cho tài nguyên task definition trước đó. |

[Xem xét AI PDF change set](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-73-ai-pdf-changeset.png)

Phạm vi thay đổi khớp với thay đổi tham số. Việc thực thi và tải lên/quét PDF thành công vẫn chưa được xác minh tại mốc này.

Thực thi change set, giữ nguyên rollback và Use deletion policy nếu được yêu cầu. Chờ Runtime đạt **UPDATE_COMPLETE**, sau đó xác minh ECS `tindy-api` Desired 1 / Running 1 / Pending 0, deployment Success và ALB target Healthy. Một rolling update có thể tạm thời chạy nhiều hơn một task. **📸 Chụp ảnh trạng thái stack và service ổn định**, ghi lại revision mới thực tế. Kiểm tra lại CloudFront readiness với HTTP 200 Healthy trước khi kiểm thử tải lên PDF.

### Kết quả triển khai AI PDF revision

Runtime đạt **UPDATE_COMPLETE** sau AI PDF change set. ECS service `tindy-api` ở trạng thái Active với Desired 1 / Running 1 / Pending 0, Deployment status **Success**, và task definition **`tindy-runtime-api:14`**.

[Cập nhật AI PDF Runtime hoàn tất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-74-ai-pdf-runtime-update-complete.png)

[AI PDF service revision 14](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-75-ai-pdf-service-revision14.png)

Trạng thái sức khỏe target vẫn đang tải trong ảnh chụp màn hình, vì vậy bằng chứng Healthy trực tiếp cho revision mới vẫn đang chờ xác minh. Kiểm tra lại CloudFront readiness trước khi kiểm thử tải lên PDF.

### Xác minh upload → scan → extract

Sau khi readiness trả về HTTP 200 Healthy, mở **AI Studio** trong frontend. Trong **CV (optional)**, chọn một tệp PDF demo dạng văn bản không rỗng, tối đa **2 MiB và 10 trang**. Việc chọn tệp bắt đầu quá trình tải lên và UI hiển thị tiến độ quét. **📸 Chụp ảnh Scanning và kết quả quét**. Khi tệp đã sạch, chọn **Extract suggestions**, sau đó **📸 chụp ảnh Review suggestions** hoặc lỗi thực tế. Việc tải lên, quét và trích xuất thành công vẫn chưa được xác minh tại mốc này.

### Readiness và quá trình quét/trích xuất PDF đã xác minh

Sau khi triển khai revision 14, các yêu cầu HTTPS qua CloudFront đến `/api/health/ready` vẫn trả về **HTTP 200 OK** với `{"status":"Healthy","service":"Tindy API"}`.

[Readiness sau khi kích hoạt AI PDF](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-76-ai-pdf-readiness-200.png)

AI Studio hiển thị `CV_Backend.pdf` với trạng thái **Scan complete. Ready to extract.**. Văn bản hồ sơ để trống (0/20,000 ký tự), trong khi Review suggestions chứa các kỹ năng bao gồm ASP.NET Core, C#, Docker, Entity Framework Core, PostgreSQL và REST API. Experience and certificates và Extraction notes (3) vẫn đang được thu gọn.

[Quét PDF hoàn tất và các đề xuất đã trích xuất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-77-ai-pdf-scan-extract-success.png)

Bằng chứng UI xác nhận luồng tải lên PDF → hoàn tất quét → trích xuất đề xuất. Các trường riêng lẻ trong những panel đang thu gọn và việc lưu đề xuất vào hồ sơ vẫn chưa được xác minh. Các lần tải lên ảnh và PDF chứng chỉ riêng biệt cũng chưa có bằng chứng.

Tiếp theo, chọn một kỹ năng được đề xuất chưa có trong hồ sơ, nhấn **Save selected suggestions**, xác minh thông báo thành công, sau đó mở hồ sơ và tải lại để xác nhận dữ liệu được lưu. **📸 Chụp ảnh thông báo lưu và kỹ năng đã được lưu trong hồ sơ.** Thao tác này thay đổi dữ liệu demo và nên được thực hiện bằng tài khoản demo trong quá trình acceptance testing.

### Đề xuất đã được lưu vào hồ sơ

Sau khi thực hiện hướng dẫn chọn một kỹ năng, lưu và tải lại hồ sơ, người vận hành đã xác nhận hoàn tất. Ảnh chụp hồ sơ hiển thị **Docker** trong các Technical skills. Ảnh này ghi lại hồ sơ sau khi cập nhật và không bao gồm thông báo lưu. Việc hoàn tất các bước lưu và tải lại đã được người vận hành xác nhận.

[Đề xuất AI đã được lưu trong hồ sơ](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.5/deploy-78-ai-suggestion-profile-saved.png)

Luồng kiểm thử chấp nhận PDF được ghi lại hiện bao gồm tải lên → hoàn tất quét → trích xuất đề xuất → lưu kỹ năng → kỹ năng hiển thị trong hồ sơ. Tải lên ảnh và PDF chứng chỉ là các luồng media riêng biệt và không được xác nhận bởi những ảnh chụp màn hình này.
