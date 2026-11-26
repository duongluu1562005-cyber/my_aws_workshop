---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# AMAZON RDS POINT-IN-TIME RECOVERY – KHÔI PHỤC CƠ SỞ DỮ LIỆU KHI XẢY RA MẤT DỮ LIỆU NGOÀI Ý MUỐN

## Nội dung bài viết

Trong quá trình vận hành ứng dụng, việc vô tình xóa dữ liệu, cập nhật sai thông tin hoặc xảy ra lỗi ứng dụng có thể ảnh hưởng đến tính toàn vẹn và khả năng hoạt động của cơ sở dữ liệu. Đối với các hệ thống sử dụng Amazon RDS, việc xây dựng cơ chế sao lưu và khôi phục dữ liệu là rất quan trọng nhằm giảm thiểu tác động của những sự cố này.

**Amazon RDS Point-in-Time Recovery (PITR)** cho phép khôi phục cơ sở dữ liệu về một thời điểm cụ thể trong khoảng thời gian lưu giữ bản sao lưu. Tính năng này kết hợp **bản sao lưu tự động (Automated Backups)** và **nhật ký giao dịch (Transaction Logs)**, giúp người dùng khôi phục dữ liệu về trạng thái trước khi xảy ra sự cố.

Thay vì chỉ phụ thuộc vào các bản sao lưu thủ công, tổ chức có thể sử dụng PITR để linh hoạt lựa chọn thời điểm khôi phục phù hợp.

### Những điểm chính (Key Takeaways)

- **Automated Backups (Sao lưu tự động):** Amazon RDS tự động tạo bản sao lưu và lưu giữ các nhật ký giao dịch cần thiết để hỗ trợ khôi phục dữ liệu theo thời điểm.
- **Backup Retention (Thời gian lưu giữ bản sao lưu):** Đối với RDS DB Instance thông thường, thời gian lưu giữ bản sao lưu tự động có thể được cấu hình từ 1 đến 35 ngày. Giá trị 0 sẽ vô hiệu hóa tính năng sao lưu tự động.
- **Point-in-Time Recovery (Khôi phục theo thời điểm):** Cho phép lựa chọn thời điểm khôi phục trong khoảng thời gian khả dụng, không vượt quá thời điểm khôi phục mới nhất mà hệ thống hỗ trợ.
- **New Database Instance (Tạo cơ sở dữ liệu mới):** Quá trình PITR tạo một DB Instance mới thay vì ghi đè trực tiếp lên cơ sở dữ liệu đang hoạt động.
- **Manual Snapshots (Bản sao lưu thủ công):** Cung cấp thêm phương án sao lưu và khôi phục dữ liệu. Các bản snapshot thủ công được giữ lại cho đến khi người dùng chủ động xóa.
- **Recovery Verification (Xác minh dữ liệu khôi phục):** Sau khi khôi phục, cần kiểm tra tính toàn vẹn của dữ liệu, cấu hình kết nối và khả năng hoạt động của ứng dụng trước khi chuyển sang sử dụng cơ sở dữ liệu mới.

### Ứng dụng thực tế (Practical Applications)

Amazon RDS Point-in-Time Recovery phù hợp với các ứng dụng phụ thuộc vào cơ sở dữ liệu quan hệ, chẳng hạn như hệ thống thương mại điện tử, quản lý đơn hàng và quản lý thông tin doanh nghiệp.

Ví dụ, một ứng dụng quản lý đơn hàng vô tình xóa các bản ghi quan trọng vào lúc 10:00. Nếu hệ thống đã bật Automated Backups và thời điểm trước sự cố nằm trong khoảng có thể khôi phục, quản trị viên có thể sử dụng PITR để tạo một DB Instance mới tại thời điểm phù hợp trước khi dữ liệu bị xóa.

Sau khi khôi phục, nhóm vận hành có thể kiểm tra dữ liệu và cấu hình ứng dụng để quyết định có chuyển kết nối sang cơ sở dữ liệu mới hay không.

Tuy nhiên, PITR không thay thế hoàn toàn việc kiểm thử sao lưu, xây dựng chính sách lưu giữ dữ liệu và lập kế hoạch khôi phục sau thảm họa. Các tổ chức vẫn cần thường xuyên đánh giá khả năng khôi phục để đảm bảo dữ liệu quan trọng được bảo vệ.

## Sơ đồ kiến trúc (Architecture Diagram)

![Sơ đồ Amazon RDS Point-in-Time Recovery](/my_aws_workshop/images/3_Blog/blog3.png)

*Hình 3: Quy trình Amazon RDS Point-in-Time Recovery sử dụng bản sao lưu tự động và nhật ký giao dịch để khôi phục cơ sở dữ liệu.*

Quy trình khôi phục cơ sở dữ liệu có thể được mô tả như sau:

**Amazon RDS Database → Automated Backups & Transaction Logs → Chọn thời điểm khôi phục → Tạo DB Instance mới → Kiểm tra dữ liệu**

Quy trình này minh họa cách quản trị viên khôi phục dữ liệu về một thời điểm phù hợp mà không ghi đè trực tiếp lên cơ sở dữ liệu ban đầu.

## Tài liệu tham khảo (References)

- [AWS Documentation – Managing Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ManagingAutomatedBackups.html)
- [AWS Documentation – Restoring a DB Instance to a Specified Time](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html)
- [AWS Documentation – Backup Retention Period](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.BackupRetention.html)
- [AWS Documentation – Backing Up and Restoring Your Amazon RDS DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/gettingstartedguide/managing-backup-restore.html)