---
title: "Tuần 3"
date: 2026-09-28
weight: 3
chapter: false
pre: "<b>1.3. </b>"
---

# Tìm hiểu máy chủ ảo Amazon EC2

### Mục tiêu trong tuần

- Tìm hiểu Amazon EC2 và cách sử dụng máy chủ ảo trên AWS.
- Làm quen với các thành phần chính của EC2 như Instance, AMI, Key Pair và Security Group.
- Học cách tạo, kết nối và quản lý EC2 Instance.
- Tìm hiểu cách lưu trữ dữ liệu với Amazon Elastic Block Store (EBS).

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu Amazon EC2 là gì và dùng để làm gì.<br>- Học về các loại EC2 Instance và cách chọn cấu hình phù hợp.<br>- Tìm hiểu Amazon Machine Image (AMI) và Key Pair. | 28/09/2026 | 28/09/2026 | [Amazon EC2 Workshop](https://000004.awsfcaj.com/) |
| 3 | - Tìm hiểu các bước tạo EC2 Instance trên AWS Management Console.<br>- Học cách chọn AMI, Instance Type và Key Pair.<br>- Tìm hiểu Security Group và cách thiết lập quyền truy cập mạng cho EC2. | 29/09/2026 | 29/09/2026 | [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)<br>[Amazon EC2 Workshop](https://000004.awsfcaj.com/) |
| 4 | - Xem hướng dẫn kết nối EC2 bằng SSH.<br>- Tìm hiểu Public IP, Private IP và cách dùng Key Pair để xác thực khi kết nối.<br>- Làm quen với một số lệnh Linux cơ bản khi sử dụng EC2. | 30/09/2026 | 30/09/2026 | [Connect to EC2 using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-to-linux-instance.html) |
| 5 | - Tìm hiểu cách quản lý EC2 Instance:<br>&emsp;+ Start.<br>&emsp;+ Stop.<br>&emsp;+ Reboot.<br>&emsp;+ Terminate.<br>- Phân biệt giữa Stop và Terminate.<br>- Học cách kiểm tra trạng thái EC2 Instance. | 01/10/2026 | 01/10/2026 | [EC2 Instance Lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html) |
| 6 | - Tìm hiểu Amazon EBS và cách lưu trữ dữ liệu cho EC2.<br>- Xem hướng dẫn tạo và gắn EBS Volume vào EC2 Instance.<br>- Tìm hiểu EBS Snapshot và cách sử dụng để sao lưu dữ liệu.<br>- Ôn lại kiến thức về EC2 đã học trong tuần. | 02/10/2026 | 02/10/2026 | [Amazon EBS Documentation](https://docs.aws.amazon.com/ebs/)<br>[Amazon EC2 Workshop](https://000004.awsfcaj.com/) |

---

### Kết quả đạt được

- Hiểu được Amazon EC2 là dịch vụ dùng để tạo và quản lý máy chủ ảo trên AWS.
- Làm quen với các thành phần chính của EC2:
  - **Instance:** Máy chủ ảo chạy trên AWS.
  - **AMI:** Mẫu dùng để tạo EC2 Instance.
  - **Key Pair:** Cặp khóa dùng để xác thực khi kết nối với EC2.
  - **Security Group:** Tường lửa ảo giúp kiểm soát lưu lượng mạng ra vào EC2.
- Biết các bước tạo EC2 Instance và chọn cấu hình phù hợp thông qua tài liệu hướng dẫn AWS.
- Hiểu cách kết nối EC2 chạy Linux bằng SSH và làm quen với một số lệnh Linux cơ bản.
- Phân biệt được các thao tác Start, Stop, Reboot và Terminate.
- Hiểu Amazon EBS dùng để lưu trữ dữ liệu dạng khối cho EC2 và EBS Snapshot có thể dùng để sao lưu dữ liệu.