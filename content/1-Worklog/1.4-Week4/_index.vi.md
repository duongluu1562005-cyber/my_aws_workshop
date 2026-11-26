---
title: "Tuần 4"
date: 2026-10-05
weight: 4
chapter: false
pre: "<b>1.4. </b>"
---

# Tìm hiểu mạng đám mây với Amazon VPC

### Mục tiêu trong tuần

- Tìm hiểu Amazon VPC và cách quản lý mạng trên AWS.
- Làm quen với các thành phần như Subnet, Route Table và Internet Gateway.
- Học cách sử dụng Security Group và Network ACL để bảo vệ tài nguyên.
- Tìm hiểu cách tạo VPC và kết nối EC2 trong mạng AWS.

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu Amazon VPC là gì và dùng để làm gì.<br>- Học về CIDR và cách chia địa chỉ IP trong VPC.<br>- Tìm hiểu sự khác nhau giữa Public Subnet và Private Subnet. | 05/10/2026 | 05/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/)<br>[Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) |
| 3 | - Tìm hiểu Route Table và cách định tuyến lưu lượng mạng.<br>- Học về Internet Gateway và NAT Gateway.<br>- Xem cách các tài nguyên trong VPC kết nối với Internet. | 06/10/2026 | 06/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| 4 | - Tìm hiểu Security Group và Network ACL.<br>- Học cách thiết lập Inbound Rules và Outbound Rules.<br>- So sánh cách Security Group và Network ACL kiểm soát lưu lượng mạng. | 07/10/2026 | 07/10/2026 | [AWS VPC Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)<br>[Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| 5 | - Xem hướng dẫn thực hành trên AWS VPC:<br>&emsp; + Tạo VPC.<br>&emsp; + Tạo Public Subnet và Private Subnet.<br>&emsp; + Tạo Internet Gateway.<br>&emsp; + Cấu hình Route Table.<br>&emsp; + Tạo Security Group. | 08/10/2026 | 08/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| 6 | - Tìm hiểu cách tạo EC2 Instance trong VPC.<br>- Xem hướng dẫn kiểm tra kết nối đến EC2.<br>- Tìm hiểu cách xóa các tài nguyên sau khi thực hành để tránh phát sinh chi phí.<br>- Ôn lại kiến thức về VPC đã học trong tuần. | 09/10/2026 | 09/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |

---

### Kết quả đạt được

- Hiểu được Amazon VPC dùng để tạo và quản lý mạng riêng trên AWS.
- Làm quen với các thành phần cơ bản của VPC:
  - **Subnet:** Chia mạng VPC thành những mạng nhỏ hơn.
  - **Route Table:** Xác định đường đi của lưu lượng mạng.
  - **Internet Gateway:** Giúp tài nguyên trong VPC có thể kết nối với Internet khi được cấu hình phù hợp.
  - **NAT Gateway:** Cho phép tài nguyên trong Private Subnet kết nối ra ngoài Internet mà không cần nhận kết nối trực tiếp từ Internet.
- Biết được sự khác nhau giữa Public Subnet và Private Subnet.
- Hiểu cách Security Group và Network ACL giúp kiểm soát truy cập mạng.
- Biết các bước tạo VPC, Subnet, Internet Gateway và Route Table theo hướng dẫn.
- Hiểu cách EC2 kết nối với các tài nguyên trong VPC.