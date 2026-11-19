
## 5.3.2 — Triển khai Network

### Tổng quan

Sau khi hoàn tất triển khai Foundation, bước tiếp theo là thiết lập hạ tầng mạng cho ứng dụng Tindy.

Template `network.yaml` tạo các tài nguyên mạng cần thiết để kết nối và bảo vệ các thành phần thuộc Database và Runtime.

Cấu hình mạng bao gồm:

- **VPC:** Sử dụng dải địa chỉ IP `10.20.0.0/16`.
- **Internet Gateway:** Cung cấp kết nối Internet cho các tài nguyên sử dụng tuyến đường công khai.
- **6 Subnets:** Phân chia mạng cho ECS tasks, Application Load Balancer (ALB) nội bộ và cơ sở dữ liệu.
- **Route Tables:** Quản lý đường đi của lưu lượng mạng giữa các subnet và các tuyến đã cấu hình.
- **Security Groups:** Kiểm soát truy cập mạng cho ALB, ECS, migration tasks và cơ sở dữ liệu.

Template định nghĩa tổng cộng **26 tài nguyên AWS** và **9 Outputs** tại khu vực `us-east-1`.

### Bước 1: Tải lên Network Template

Truy cập **AWS Management Console** và chọn khu vực **US East (N. Virginia) — us-east-1**.

Đi đến:

**CloudFormation → Stacks → Create stack → With new resources (standard)**

Tại mục **Specify template**, thực hiện:

1. Chọn **Choose an existing template**.
2. Chọn **Upload a template file**.
3. Tải lên `network.yaml` từ thư mục `tindy-infrastructure/cloudformation`.
4. Nhấn **Next** để tiếp tục.

![Tải lên Network template](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-15-network-template.png)

### Bước 2: Cấu hình Network Stack

Tại trang **Specify stack details**, nhập tên stack:

`tindy-network`

Template `network.yaml` không có phần Parameters nên không cần nhập thêm tham số cấu hình.

![Đặt tên Network stack](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16-network-stack-name.png)

Tiếp tục đến phần cấu hình stack và thêm các tags:

| Key | Value |
|---|---|
| Project | Tindy |
| Environment | tindy |
| Owner | TindyTeam |

Các tags giúp nhận diện và quản lý những tài nguyên AWS thuộc dự án Tindy.

![Cấu hình tags cho Network stack](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-16a-network-tags.png)

Giữ **Express mode ở trạng thái tắt** và **rollback được bật**.

Kiểm tra lại thông tin cấu hình và xác nhận tạo stack trên CloudFormation.

### Bước 3: Tạo và kiểm tra Network Stack

Sau khi xác nhận, CloudFormation bắt đầu triển khai các tài nguyên mạng được định nghĩa trong `network.yaml`.

Mở tab **Events** để theo dõi tiến trình triển khai.

Trạng thái cần đạt được là:

`CREATE_COMPLETE`

Nếu quá trình triển khai gặp lỗi, kiểm tra **Logical ID** và **Status reason** của tài nguyên bị lỗi trước khi tiếp tục.

Kết quả thực tế cho thấy stack `tindy-network` đã được tạo thành công với trạng thái **CREATE_COMPLETE** và có **9 Outputs**.

![Network triển khai thành công và hiển thị Outputs](/my_aws_workshop/images/5-Workshop/5.3-S3-vpc/5.3.2/deploy-18-network-outputs.png)

### Bước 4: Kiểm tra Network Outputs

Sau khi triển khai, Network stack cung cấp các thông tin tham chiếu để sử dụng trong những stack tiếp theo.

| Output | Chức năng |
|---|---|
| `VpcId` | Xác định VPC được sử dụng cho hạ tầng Tindy. |
| `PublicTaskSubnetIds` | Xác định các subnet dành cho ECS tasks. |
| `PrivateAppSubnetIds` | Xác định các subnet dành cho ALB nội bộ. |
| `PrivateDbSubnetIds` | Xác định các subnet riêng dành cho Amazon RDS. |
| `AlbSubnetCidrs` | Cung cấp dải CIDR của các subnet ALB để cấu hình trusted proxy. |
| `AlbSecurityGroupId` | Xác định Security Group của ALB. |
| `EcsSecurityGroupId` | Xác định Security Group của ECS. |
| `MigrationSecurityGroupId` | Xác định Security Group dành cho migration tasks. |
| `DatabaseSecurityGroupId` | Xác định Security Group của cơ sở dữ liệu. |

Nhờ các Outputs này, những CloudFormation stack tiếp theo có thể sử dụng đúng tài nguyên mạng mà không cần nhập thủ công các thông tin định danh.

### Kết quả triển khai

Network stack đã được tạo thành công.

| Nội dung kiểm tra | Kết quả |
|---|---|
| Tên stack | `tindy-network` |
| Khu vực AWS | `us-east-1` |
| VPC CIDR | `10.20.0.0/16` |
| Số tài nguyên định nghĩa | 26 |
| Tham số stack | Không có |
| Trạng thái stack | `CREATE_COMPLETE` |
| Số lượng Outputs | 9 |
| Resource tags | Đã cấu hình |

Sau khi triển khai, Tindy đã có hạ tầng mạng gồm VPC, các subnet, route tables và security groups cần thiết cho những thành phần tiếp theo.

**Network đã sẵn sàng cho bước tiếp theo: triển khai Database.**
