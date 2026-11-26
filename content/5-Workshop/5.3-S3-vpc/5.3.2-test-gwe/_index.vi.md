---
title: "TRIỂN KHAI NETWORK"
date: 2026-11-19
weight: 2
chapter: false
pre: "<b> 5.3.2 </b>"
---
`network.yaml` định nghĩa VPC `10.20.0.0/16`, một Internet Gateway, sáu subnet, các route table và security group cho ALB, ECS, migration và cơ sở dữ liệu. 26 tài nguyên và chín Outputs của template này hỗ trợ Database và Runtime tại `us-east-1`.

## 1. Tạo stack

1. Chọn **CloudFormation → Create stack → With new resources (standard)** tại **N. Virginia (`us-east-1`)**.
2. Chọn **Choose an existing template → Upload a template file**, tải lên `network.yaml` từ `tindy-infrastructure/cloudformation`.

**📸 Chụp ảnh tệp network.yaml đã chọn.**



[Tải lên network.yaml](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-15-network-template.png)

3. Nhấn Next và đặt tên stack là `tindy-network`. Template này không có phần Parameters.

**📸 Chụp ảnh tên stack.**

4. Thêm các tag `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Giữ Express mode ở trạng thái tắt và rollback ở trạng thái bật.
5. Xem xét stack và template, sau đó gửi yêu cầu tạo bằng nút trên Console.
6. Theo dõi Events cho đến khi `tindy-network — CREATE_COMPLETE`. Nếu xảy ra lỗi, kiểm tra Logical ID và Status reason trước khi tiếp tục.

**📸 Chụp ảnh CREATE_COMPLETE và tất cả Outputs.**

## 2. Outputs cho các stack tiếp theo

Outputs bao gồm `VpcId`, `PublicTaskSubnetIds` cho các tác vụ ECS, `PrivateAppSubnetIds` cho ALB nội bộ, `PrivateDbSubnetIds` cho RDS, `AlbSubnetCidrs` cho cấu hình trusted proxy, và `AlbSecurityGroupId`, `EcsSecurityGroupId`, `MigrationSecurityGroupId`, `DatabaseSecurityGroupId` cho các thành phần tương ứng.

## 3. Kết quả triển khai đã xác minh

Stack được đặt tên là `tindy-network` và Console xác nhận template không có tham số.



[Tên stack Network](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16-network-stack-name.png)



Các tag Project, Environment và Owner đã được cấu hình.



[Tags của Network](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16a-network-tags.png)



Stack đạt trạng thái **CREATE_COMPLETE** với **Outputs (9)**. Ảnh chụp màn hình ghi lại trạng thái stack đã hoàn tất và các tham chiếu mạng mới cho Database và Runtime. Sử dụng các ID của stack mới cho những lần triển khai tiếp theo.



[Network hoàn tất và Outputs](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-18-network-outputs.png)
