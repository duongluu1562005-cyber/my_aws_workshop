---
title: "Tuần 6"
date: 2026-10-19
weight: 6
chapter: false
pre: "<b>1.6. </b>"
---

# Giám sát tài nguyên và quản lý chi phí với CloudWatch và AWS Budgets

### Mục tiêu trong tuần

- Tìm hiểu Amazon CloudWatch và cách theo dõi tài nguyên trên AWS.
- Làm quen với CloudWatch Metrics, Logs và Alarms.
- Tìm hiểu AWS Budgets và cách quản lý chi phí sử dụng AWS.
- Học cách theo dõi hoạt động của hệ thống và nhận cảnh báo khi cần.

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu Amazon CloudWatch là gì và dùng để làm gì.<br>- Học về CloudWatch Metrics và cách theo dõi tài nguyên AWS.<br>- Tìm hiểu các chỉ số như CPU Utilization và Network Traffic của EC2. | 19/10/2026 | 19/10/2026 | [Amazon CloudWatch Workshop](https://000008.awsfcaj.com/)<br>[CloudWatch Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |
| 3 | - Tìm hiểu CloudWatch Logs và cách xem nhật ký hoạt động của ứng dụng.<br>- Làm quen với CloudWatch Dashboards và cách xem các chỉ số trên biểu đồ.<br>- Xem hướng dẫn theo dõi tài nguyên EC2 bằng CloudWatch. | 20/10/2026 | 20/10/2026 | [Amazon CloudWatch Workshop](https://000008.awsfcaj.com/)<br>[CloudWatch Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) |
| 4 | - Tìm hiểu CloudWatch Alarms và cách tạo cảnh báo.<br>- Xem hướng dẫn thiết lập cảnh báo khi mức sử dụng CPU vượt ngưỡng đã chọn.<br>- Tìm hiểu cách CloudWatch gửi thông báo khi có cảnh báo. | 21/10/2026 | 21/10/2026 | [Amazon CloudWatch Workshop](https://000008.awsfcaj.com/)<br>[CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) |
| 5 | - Tìm hiểu AWS Budgets và cách theo dõi chi phí sử dụng AWS.<br>- Học về các loại ngân sách như Cost Budget và Usage Budget.<br>- Tìm hiểu cách đặt giới hạn ngân sách và nhận thông báo qua email. | 22/10/2026 | 22/10/2026 | [AWS Budgets Workshop](https://000007.awsfcaj.com/)<br>[AWS Budgets Documentation](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) |
| 6 | - Xem hướng dẫn tạo ngân sách trên AWS Budgets:<br>&emsp; + Chọn loại ngân sách.<br>&emsp; + Đặt số tiền dự kiến.<br>&emsp; + Thiết lập mức cảnh báo.<br>&emsp; + Thêm email nhận thông báo. | 23/10/2026 | 23/10/2026 | [AWS Budgets Workshop](https://000007.awsfcaj.com/)<br>[Creating a Budget](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-create.html) |

---

### Kết quả đạt được

- Hiểu được Amazon CloudWatch dùng để theo dõi hoạt động của các tài nguyên và ứng dụng trên AWS.
- Làm quen với những tính năng cơ bản của CloudWatch:
  - **Metrics:** Theo dõi các chỉ số như CPU và lưu lượng mạng.
  - **Logs:** Xem nhật ký hoạt động của hệ thống.
  - **Alarms:** Đặt cảnh báo khi chỉ số vượt mức đã chọn.
  - **Dashboards:** Xem các chỉ số qua biểu đồ.
- Biết cách xem các chỉ số hoạt động của EC2 trên CloudWatch theo hướng dẫn.
- Hiểu cách CloudWatch Alarms giúp phát hiện những thay đổi cần chú ý.
- Biết AWS Budgets dùng để theo dõi chi phí và mức sử dụng tài nguyên.
- Làm quen với các bước tạo ngân sách, đặt mức cảnh báo và nhận thông báo qua email.
- Hiểu rằng AWS Budgets giúp cảnh báo chi phí, nhưng không tự động ngăn mọi khoản phí vượt ngân sách.