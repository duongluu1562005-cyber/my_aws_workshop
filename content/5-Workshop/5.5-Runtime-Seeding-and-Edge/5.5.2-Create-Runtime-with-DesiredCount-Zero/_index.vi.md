---
title: "TẠO RUNTIME VỚI DESIREDCOUNT BẰNG 0"
date: 2026-11-22
weight: 2
chapter: false
pre: "<b> 5.3.2 </b>"
---

`runtime.yaml` định nghĩa ECS cluster, API task definition và service, IAM roles, log group, ALB nội bộ, listener, target group, ingress và CloudFront VPC Origin. Các task definition tùy chọn cho bootstrap, migration và seed được kích hoạt thông qua các tham số.

## 1. Chuẩn bị đầu vào

AWS CLI đọc các Outputs đã được xác minh từ Foundation, Network, Database, Integrations và Edge. Hostname mới là `d1vcn4g2ifl3k8.cloudfront.net`; chế độ maintenance trả về HTTP 503.

ECR vẫn chứa release image `tindy-api@sha256:1505b06199c2965fd800deab9956cbf47601ce76bc42848b8085dfae5b7f69ea`, khớp với ApiImage đã được ghi lại trước khi dọn dẹp. Cố định digest một cách nhất quán cho API và các tác vụ DB.

Tạo Runtime với `DesiredCount = 0` trong khi chuẩn bị secrets, DB bootstrap, migrations và seeding. Giữ `AiPdfUploadsEnabled = false` trong quá trình chuẩn bị schema.

## 2. Tải lên template

1. Tại N. Virginia, chọn **CloudFormation → Create stack → With new resources (standard)**.
2. Tải lên `runtime.yaml` từ infrastructure repository.

**📸 Chụp ảnh tên tệp đã chọn.**

3. Nhấn Next và đặt tên stack là `tindy-runtime`. Điền các tham số từ Outputs của các stack mới trong các bước hướng dẫn tiếp theo.

**📸 Chụp ảnh các phần tham số trước khi đến Review.**

Việc tải lên template và nhập tham số được ghi lại bên dưới. Quá trình tạo Runtime mới đã hoàn tất; xem bằng chứng bên dưới.

## 3. Tham số Runtime ban đầu

AWS CLI đã xác minh các Outputs hiện tại, ECR image digest và CloudFront prefix list. Môi trường trước đó đã bật OAuth, media, Bedrock text và AI PDF. Quá trình tạo ban đầu giữ lại OAuth, media và Bedrock text, trong khi AI PDF vẫn ở trạng thái false cho đến khi schema sẵn sàng. Để trống bốn đầu vào one-off task cuối cùng trong lần tạo ban đầu. Sau khi service với số lượng task bằng 0 tồn tại, chuẩn bị secrets và cập nhật stack để kích hoạt các định nghĩa bootstrap/migration/seed.

Các giá trị đầy đủ cũng được lưu trong `backup/tindy-runtime-fresh/runtime-parameters.csv` và tệp JSON tương ứng của nó trong Worklog.

| **Tham số** | **Giá trị phiên triển khai** |
| :--- | :--- |
| `ApiImage` | `572454213668.dkr.ecr.us-east-1.amazonaws.com/tindy-api@sha256:1505b06199c2965fd800deab9956cbf47601ce76bc42848b8085dfae5b7f69ea` |
| `DesiredCount` | `0` |
| `VpcId` | `vpc-0209344341dce92fb` |
| `PublicTaskSubnetIds` | `subnet-01748b133fa7297ac,subnet-0a7df83a7449da864` |
| `PrivateAppSubnetIds` | `subnet-0faa17a03a1392748,subnet-050d30a2b0a6274a3` |
| `AlbSubnetCidrs` | `10.20.20.0/24,10.20.21.0/24` |
| `AlbSecurityGroupId` | `sg-08b240832188ed241` |
| `EcsSecurityGroupId` | `sg-0b9a2bfcc5ff842f5` |
| `CloudFrontOriginPrefixListId` | `pl-3b927c52` |
| `CloudFrontServiceSecurityGroupId` | Để trống |
| `CloudFrontPublicHost` | `d1vcn4g2ifl3k8.cloudfront.net` |
| `DatabaseConnectionSecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/database/api-connection-UleOrh` |
| `JwtSigningKeySecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/auth/jwt-signing-key-b4L7JF` |
| `OAuthEnabled` | `true` |
| `GoogleOAuthSecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/auth/google-oauth-OHiwct` |
| `GitHubOAuthSecretArn` | `arn:aws:secretsmanager:us-east-1:572454213668:secret:tindy/auth/github-oauth-QqFH0k` |
| `AiPdfUploadsEnabled` | `false` |
| `BedrockTextEnabled` | `true` |
| `MediaEnabled` | `true` |
| `MediaBucketName` | `tindy-foundation-mediabucket-5kz0y8tsm0qo` |
| `GuardDutyProtectionPlanArn` | `arn:aws:guardduty:us-east-1:572454213668:malware-protection-plan/9cd0f69716d5ca261abc` |
| `GuardDutyQueueUrl` | `https://sqs.us-east-1.amazonaws.com/572454213668/tindy-integrations-pdf-scan` |
| `DataProtectionParameterPath` | `/tindy/data-protection` |
| `DBProvisionMode` | `Fresh` |
| `MigrationConnectionSecretArn` | Để trống |
| `SeedPasswordSecretArn` | Để trống |
| `DBEndpointAddress` | Để trống |
| `DBMasterSecretArn` | Để trống |

Các giá trị này đã được kiểm tra đối chiếu với ảnh chụp màn hình tham số. Việc tạo Runtime mới được xác minh bên dưới. **📸 Chụp ảnh tên stack và các phần tham số trước khi đến Review.**

## 4. Xác minh các tham số đã nhập

Đã tải lên `runtime.yaml` và đặt tên stack là `tindy-runtime`.

[Template Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-34-runtime-template.png)

[Tên stack Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35-runtime-stack-name.png)

Image digest, Edge hostname và prefix list, các tham chiếu secret/GuardDuty, feature flags và các networking ID mới khớp với các giá trị đã chuẩn bị. `DesiredCount = 0`, `DBProvisionMode = Fresh` và `AiPdfUploadsEnabled = false`. Bốn đầu vào one-off task vẫn để trống cho lần tạo ban đầu.

[Tham số image và Edge](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35a-runtime-parameters-image-edge.png)

[Tham số database và số lượng task](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35b-runtime-parameters-database.png)

[Tham chiếu integration và secret](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35c-runtime-parameters-integrations.png)

[Tham số subnet và VPC](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-35d-runtime-parameters-subnets.png)

## 5. Tạo stack và thu thập Outputs

Nhấn Next, thêm các tag `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Giữ Express mode ở trạng thái tắt, rollback ở trạng thái bật và Use deletion policy. Xác nhận IAM capabilities, xem xét và gửi yêu cầu tạo.

Chờ `tindy-runtime — CREATE_COMPLETE`, sau đó ghi lại `AlbDnsName`, `VpcOriginId`, `ApiTargetGroupArn` và các tham chiếu ECS từ Outputs. Với DesiredCount bằng 0, service không có API task đang chạy ở giai đoạn này.

**📸 Chụp ảnh Options/IAM, CREATE_COMPLETE và tất cả Outputs.**

Đã xác minh các tag, Express mode ở trạng thái tắt, Roll back all resources, Use deletion policy và việc xác nhận IAM capability.

[Tags của Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36-runtime-tags.png)

[Tùy chọn Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36a-runtime-options.png)

[Xác nhận IAM của Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36b-runtime-iam-capabilities.png)

Trang Review hiển thị nút Submit để bắt đầu tạo stack.

[Review Runtime trước khi Submit](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-36c-runtime-review-submit.png)

Runtime đạt trạng thái **CREATE_COMPLETE** với **Outputs (6)**.

[Runtime hoàn tất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-37-runtime-complete.png)

[Sáu Outputs của Runtime](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.2/deploy-38-runtime-outputs.png)

`AlbDnsName` và `VpcOriginId` cấu hình Edge ở chế độ live. `ApiTargetGroupArn` hỗ trợ xác minh sức khỏe và alarm dimensions. `ApiTaskDefinitionArn`, `ApiServiceName` và `ClusterName` xác định cấu hình API và các tài nguyên ECS.

Các định nghĩa bootstrap/migration/seed xuất hiện sau khi cung cấp các tham số tương ứng. Tiếp theo, xác minh các số lượng Desired/Running/Pending đều bằng 0 trước khi chuẩn bị Fresh secrets.
