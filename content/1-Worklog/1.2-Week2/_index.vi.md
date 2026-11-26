---
title: "Tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: "<b>1.2. </b>"
---

# Tìm hiểu quản lý người dùng và phân quyền với AWS IAM

### Mục tiêu trong tuần

- Tìm hiểu AWS IAM và cách quản lý quyền truy cập trên AWS.
- Học cách sử dụng IAM Users, Groups, Policies và Roles.
- Làm quen với các bước thực hành quản lý người dùng và phân quyền.
- Tìm hiểu cách bảo vệ tài khoản và sử dụng AWS an toàn hơn.

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu AWS IAM là gì và dùng để làm gì.<br>- Học về IAM Users và IAM Groups.<br>- Tìm hiểu sự khác nhau giữa Root User và IAM User. | 21/09/2026 | 21/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/)<br>[IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) |
| 3 | - Tìm hiểu IAM Policies và cách cấp quyền truy cập.<br>- Làm quen với các loại quyền như ReadOnlyAccess và AdministratorAccess.<br>- Học cách kiểm tra quyền của người dùng. | 22/09/2026 | 22/09/2026 | [IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)<br>[AWS IAM Workshop](https://000002.awsfcaj.com/) |
| 4 | - Xem hướng dẫn thực hành trên AWS IAM:<br>&emsp; + Tạo IAM User.<br>&emsp; + Tạo IAM Group.<br>&emsp; + Thêm User vào Group.<br>&emsp; + Gán quyền cho Group.<br>- Tìm hiểu cách đăng nhập bằng tài khoản IAM. | 23/09/2026 | 23/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/)<br>[IAM Documentation](https://docs.aws.amazon.com/iam/) |
| 5 | - Tìm hiểu IAM Roles và cách sử dụng Role trên AWS.<br>- Xem hướng dẫn tạo IAM Role và chuyển đổi Role (Switch Role).<br>- Tìm hiểu vì sao EC2 hoặc các dịch vụ AWS cần IAM Role để truy cập tài nguyên khác. | 24/09/2026 | 24/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/)<br>[IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) |
| 6 | - Ôn lại cách quản lý User, Group, Policy và Role.<br>- Tìm hiểu nguyên tắc cấp quyền tối thiểu (Least Privilege).<br>- Xem lại cách sử dụng MFA để bảo vệ tài khoản.<br>- Ghi chép những kiến thức đã học trong tuần. | 25/09/2026 | 25/09/2026 | [IAM Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)<br>[AWS IAM Workshop](https://000002.awsfcaj.com/) |

---

### Kết quả đạt được

- Hiểu được AWS IAM dùng để quản lý người dùng và quyền truy cập vào các dịch vụ AWS.
- Biết được sự khác nhau giữa Root User và IAM User.
- Làm quen với các thành phần chính của IAM:
  - **IAM User:** Tài khoản người dùng.
  - **IAM Group:** Nhóm người dùng.
  - **IAM Policy:** Quy định những quyền được phép sử dụng.
  - **IAM Role:** Cho phép người dùng hoặc dịch vụ nhận quyền truy cập khi cần.
- Biết các bước tạo User, Group và gán quyền theo hướng dẫn của AWS.
- Hiểu cách IAM Role được sử dụng để cấp quyền cho các dịch vụ như EC2.
- Biết thêm về MFA và cách cấp quyền phù hợp để bảo vệ tài khoản AWS.