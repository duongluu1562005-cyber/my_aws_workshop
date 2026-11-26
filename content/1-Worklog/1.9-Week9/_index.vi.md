---
title: "Tuần 9"
date: 2026-11-09
weight: 9
chapter: false
pre: "<b>1.9. </b>"
---

# Tìm hiểu triển khai và tự động hóa với AWS CloudFormation

### Mục tiêu trong tuần

- Tìm hiểu AWS CloudFormation và cách quản lý tài nguyên AWS.
- Làm quen với Infrastructure as Code (IaC).
- Học cách sử dụng CloudFormation Template và Stack.
- Tìm hiểu cách tạo, cập nhật và xóa tài nguyên bằng CloudFormation.

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu AWS CloudFormation là gì và dùng để làm gì.<br>- Học về Infrastructure as Code (IaC).<br>- Tìm hiểu lợi ích của việc tạo và quản lý nhiều tài nguyên AWS bằng một Template. | 09/11/2026 | 09/11/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/)<br>[AWS CloudFormation Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| 3 | - Tìm hiểu CloudFormation Template và Stack.<br>- Làm quen với cách viết Template bằng YAML hoặc JSON.<br>- Tìm hiểu các phần cơ bản trong Template:<br>&emsp; + Parameters.<br>&emsp; + Resources.<br>&emsp; + Outputs. | 10/11/2026 | 10/11/2026 | [CloudFormation Template Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/template-anatomy.html)<br>[AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| 4 | - Xem hướng dẫn sử dụng AWS CloudFormation:<br>&emsp; + Chuẩn bị Template.<br>&emsp; + Tạo Stack.<br>&emsp; + Kiểm tra trạng thái Stack.<br>&emsp; + Xem các tài nguyên được tạo.<br>- Tìm hiểu cách CloudFormation sử dụng Template để tạo tài nguyên AWS. | 11/11/2026 | 11/11/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/)<br>[CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| 5 | - Tìm hiểu cách cập nhật CloudFormation Stack khi thay đổi Template.<br>- Làm quen với Change Sets và cách xem các thay đổi trước khi cập nhật.<br>- Tìm hiểu cách kiểm tra trạng thái khi triển khai Stack. | 12/11/2026 | 12/11/2026 | [AWS CloudFormation Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |
| 6 | - Tìm hiểu cách xóa CloudFormation Stack khi không còn sử dụng.<br>- Xem cách CloudFormation quản lý và xóa các tài nguyên trong Stack. | 13/11/2026 | 13/11/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/)<br>[AWS CloudFormation Documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) |

---

### Kết quả đạt được

- Hiểu được AWS CloudFormation giúp tạo và quản lý tài nguyên AWS bằng các file cấu hình.
- Biết Infrastructure as Code (IaC) là cách quản lý hạ tầng thông qua mã cấu hình thay vì phải tạo từng tài nguyên bằng tay.
- Làm quen với các thành phần cơ bản của CloudFormation:
  - **Template:** File chứa cấu hình tài nguyên AWS.
  - **Stack:** Nhóm tài nguyên được CloudFormation quản lý.
  - **Parameters:** Các giá trị có thể nhập khi tạo Stack.
  - **Resources:** Những tài nguyên AWS cần tạo.
  - **Outputs:** Thông tin được xuất ra sau khi triển khai.
- Biết các bước chuẩn bị Template, tạo Stack và kiểm tra trạng thái theo hướng dẫn.
- Hiểu cách cập nhật Stack khi thay đổi Template và cách sử dụng Change Sets.
- Biết cách xóa Stack và kiểm tra tài nguyên sau khi không còn sử dụng.
- Hiểu được CloudFormation giúp việc triển khai và quản lý tài nguyên AWS dễ dàng, đồng nhất hơn.