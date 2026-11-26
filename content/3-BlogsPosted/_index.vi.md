---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Phần này giới thiệu ba bài viết kỹ thuật được chuẩn bị để chia sẻ với cộng đồng AWS Study Group. Nội dung tập trung vào quản lý chi phí lưu trữ, bảo mật quyền truy cập và khôi phục cơ sở dữ liệu bằng các dịch vụ AWS.

### [Blog 1 – AMAZON S3 LIFECYCLE GIÚP TỰ ĐỘNG QUẢN LÝ CHI PHÍ LƯU TRỮ NHƯ THẾ NÀO?](/my_aws_workshop/3-blogsposted/3.1-blog1/)

Bài viết giới thiệu cách Amazon S3 Lifecycle giúp các tổ chức quản lý chi phí lưu trữ thông qua việc tự động chuyển các đối tượng đủ điều kiện giữa những lớp lưu trữ hoặc cho phép chúng hết hạn theo quy tắc đã cấu hình. Bài viết cũng đề cập đến chính sách vòng đời, quản lý phiên bản và những yếu tố chi phí quan trọng khi lưu trữ dữ liệu sao lưu, nhật ký ứng dụng và các tệp ít được truy cập.

### [Blog 2 – AWS IAM ACCESS ANALYZER: PHÁT HIỆN QUYỀN TRUY CẬP KHÔNG SỬ DỤNG ĐỂ TĂNG CƯỜNG BẢO MẬT ĐÁM MÂY](/my_aws_workshop/3-blogsposted/3.2-blog2/)

Bài viết giới thiệu AWS IAM Access Analyzer và vai trò của dịch vụ trong việc phát hiện các quyền truy cập không được sử dụng cũng như rà soát quyền truy cập vào tài nguyên AWS. Nội dung nhấn mạnh cách các kết quả phân tích hỗ trợ nguyên tắc đặc quyền tối thiểu, giúp quản trị viên đánh giá chính sách IAM, giảm bớt những quyền không cần thiết và cải thiện việc quản lý bảo mật.

### [Blog 3 – AMAZON RDS POINT-IN-TIME RECOVERY: KHÔI PHỤC CƠ SỞ DỮ LIỆU KHI XẢY RA MẤT DỮ LIỆU NGOÀI Ý MUỐN](/my_aws_workshop/3-blogsposted/3.3-blog3/)

Bài viết tìm hiểu Amazon RDS Point-in-Time Recovery (PITR), tính năng cho phép khôi phục cơ sở dữ liệu về một thời điểm cụ thể trong khoảng thời gian lưu giữ bản sao lưu khả dụng. Nội dung giải thích cách bản sao lưu tự động và nhật ký giao dịch hỗ trợ khôi phục cơ sở dữ liệu, đồng thời nhấn mạnh tầm quan trọng của việc kiểm tra dữ liệu sau khôi phục trước khi chuyển ứng dụng sang DB Instance mới.