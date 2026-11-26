---
title: "Tuần 7"
date: 2026-10-26
weight: 7
chapter: false
pre: "<b>1.7. </b>"
---

# Tìm hiểu sao lưu dữ liệu và bảo mật ứng dụng trên AWS

### Mục tiêu trong tuần

- Tìm hiểu AWS Backup và cách sao lưu dữ liệu trên AWS.
- Làm quen với Backup Vault, Backup Plan và cách khôi phục dữ liệu.
- Tìm hiểu AWS WAF và cách bảo vệ ứng dụng web.
- Học cách quản lý các bản sao lưu và kiểm soát truy cập vào ứng dụng.

---

### Công việc thực hiện trong tuần

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|---|---|---|---|---|
| 2 | - Tìm hiểu AWS Backup là gì và dùng để làm gì.<br>- Học về Backup Vault, Backup Plan và Recovery Point.<br>- Tìm hiểu những tài nguyên có thể sao lưu như EC2, EBS và RDS. | 26/10/2026 | 26/10/2026 | [AWS Backup Workshop](https://000013.awsfcaj.com/)<br>[AWS Backup Documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) |
| 3 | - Xem hướng dẫn sử dụng AWS Backup:<br>&emsp; + Tạo Backup Vault.<br>&emsp; + Tạo Backup Plan.<br>&emsp; + Chọn tài nguyên cần sao lưu.<br>&emsp; + Thiết lập lịch sao lưu.<br>- Tìm hiểu cách theo dõi trạng thái sao lưu. | 27/10/2026 | 27/10/2026 | [AWS Backup Workshop](https://000013.awsfcaj.com/) |
| 4 | - Tìm hiểu cách khôi phục dữ liệu từ bản sao lưu.<br>- Làm quen với Recovery Point và Restore Job.<br>- Tìm hiểu cách kiểm tra bản sao lưu và quản lý thời gian lưu giữ dữ liệu. | 28/10/2026 | 28/10/2026 | [AWS Backup Documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)<br>[AWS Backup Workshop](https://000013.awsfcaj.com/) |
| 5 | - Tìm hiểu AWS WAF là gì và dùng để làm gì.<br>- Học về Web ACL và các quy tắc bảo vệ ứng dụng web.<br>- Tìm hiểu cách AWS WAF kiểm tra các yêu cầu HTTP và HTTPS. | 29/10/2026 | 29/10/2026 | [AWS WAF Workshop](https://000026.awsfcaj.com/)<br>[AWS WAF Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html) |
| 6 | - Xem hướng dẫn tạo Web ACL và sử dụng các quy tắc bảo mật.<br>- Tìm hiểu các hành động của AWS WAF:<br>&emsp; + Allow.<br>&emsp; + Block.<br>&emsp; + Count.<br>- Tìm hiểu cách theo dõi các yêu cầu bị chặn.<br>- Ghi chép lại kiến thức đã học trong tuần. | 30/10/2026 | 30/10/2026 | [AWS WAF Workshop](https://000026.awsfcaj.com/)<br>[AWS WAF Documentation](https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html) |

---

### Kết quả đạt được

- Hiểu được AWS Backup dùng để sao lưu và khôi phục dữ liệu trên AWS.
- Làm quen với các thành phần chính của AWS Backup:
  - **Backup Vault:** Nơi lưu các bản sao lưu.
  - **Backup Plan:** Thiết lập lịch và quy tắc sao lưu.
  - **Recovery Point:** Bản sao lưu được tạo tại một thời điểm.
- Biết các bước tạo Backup Plan, chọn tài nguyên và theo dõi quá trình sao lưu theo hướng dẫn.
- Hiểu cách khôi phục dữ liệu từ bản sao lưu khi cần thiết.
- Biết AWS WAF giúp bảo vệ ứng dụng web bằng cách kiểm tra và lọc các yêu cầu truy cập.
- Làm quen với Web ACL và các hành động Allow, Block, Count.
- Hiểu thêm cách sao lưu dữ liệu và kiểm soát truy cập giúp bảo vệ hệ thống trên AWS.