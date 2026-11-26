---
title: "Tuần 8"
date: 2026-11-02
weight: 8
chapter: false
pre: "<b>1.8. </b>"
---

# Tìm hiểu AWS Lambda và công nghệ Container

### Mục tiêu trong tuần

- Tìm hiểu AWS Lambda và cách chạy chương trình mà không cần tự quản lý máy chủ.
- Làm quen với Docker và công nghệ Container.
- Tìm hiểu Amazon ECS và cách triển khai ứng dụng bằng Container.
- Biết sự khác nhau giữa EC2, Lambda và ECS.

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu AWS Lambda là gì và dùng để làm gì.<br>- Học về Serverless Computing và cách Lambda chạy chương trình khi có sự kiện.<br>- Tìm hiểu Lambda Function và Trigger. | 02/11/2026 | 02/11/2026 | [AWS Lambda Workshop](https://000022.awsfcaj.com/)<br>[AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) |
| 3 | - Xem hướng dẫn sử dụng AWS Lambda:<br>&emsp; + Tạo Lambda Function.<br>&emsp; + Chọn ngôn ngữ lập trình.<br>&emsp; + Thực hành.<br>&emsp; + Chạy thử và xem kết quả.<br>- Tìm hiểu cách xem Logs của Lambda trên CloudWatch. | 03/11/2026 | 03/11/2026 | [AWS Lambda Workshop](https://000022.awsfcaj.com/)<br>[AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) |
| 4 | - Tìm hiểu Docker và công nghệ Container.<br>- Học về Docker Image và Docker Container.<br>- Tìm hiểu sự khác nhau giữa Container và máy ảo.<br>- Xem hướng dẫn sử dụng các lệnh Docker cơ bản. | 04/11/2026 | 04/11/2026 | [Docker Workshop](https://000015.awsfcaj.com/)<br>[Docker Documentation](https://docs.docker.com/get-started/) |
| 5 | - Tìm hiểu Amazon ECS và cách quản lý Container trên AWS.<br>- Làm quen với các thành phần của ECS:<br>&emsp; + Cluster.<br>&emsp; + Task Definition.<br>&emsp; + Service.<br>- Tìm hiểu AWS Fargate và cách chạy Container mà không cần tự quản lý máy chủ. | 05/11/2026 | 05/11/2026 | [Amazon ECS Workshop](https://000016.awsfcaj.com/)<br>[Amazon ECS Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) |
| 6 | - Xem hướng dẫn triển khai ứng dụng trên Amazon ECS.<br>- Tìm hiểu cách sử dụng Container Image và Task Definition.<br>- So sánh EC2, Lambda và ECS để hiểu khi nào nên sử dụng từng dịch vụ. | 06/11/2026 | 06/11/2026 | [Amazon ECS Workshop](https://000016.awsfcaj.com/)<br>[AWS Compute Services](https://aws.amazon.com/products/compute/) |

---

### Kết quả đạt được

- Hiểu được AWS Lambda dùng để chạy code mà không cần tự quản lý máy chủ.
- Làm quen với Lambda Function, Trigger và cách kiểm tra kết quả chạy chương trình.
- Biết Docker dùng để đóng gói ứng dụng và các thành phần cần thiết vào Container.
- Hiểu được sự khác nhau giữa Docker Image và Docker Container.
- Làm quen với các thành phần chính của Amazon ECS:
  - **Cluster:** Nơi quản lý các Task và Service.
  - **Task Definition:** Chứa cấu hình để chạy Container.
  - **Service:** Giúp duy trì số lượng Task cần chạy.
- Biết AWS Fargate giúp chạy Container mà không cần tự quản lý máy chủ EC2.
- Hiểu được điểm khác nhau cơ bản giữa EC2, Lambda và ECS, cũng như mục đích sử dụng của từng dịch vụ.