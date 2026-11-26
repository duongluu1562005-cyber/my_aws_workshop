---
title: "Sự Kiện 2"
date: 2026-10-24
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Trò chuyện cùng Tiến sĩ Werner: Định hướng tương lai của Điện toán đám mây và AI tại Việt Nam

- **Ngày:** Thứ Sáu, ngày 2 tháng 10 năm 2026
- **Địa điểm:** Tầng 26, Tòa nhà Bitexco Financial Tower, Thành phố Hồ Chí Minh, Việt Nam
- **Đơn vị tổ chức:** AWS Study Group / First Cloud AI Journey (FCAJ)
- **Diễn giả:** Tiến sĩ Werner Vogels – Giám đốc Công nghệ (CTO) của Amazon
- **Vai trò:** Người tham dự
- **Video sự kiện:** [Xem trên YouTube](https://www.youtube.com/watch?v=jWjOIfMA9qc)

### Mục tiêu sự kiện

Fireside Chat with Dr. Werner: Navigating the Future of Cloud & AI in Vietnam là một buổi chia sẻ kiến thức trực tuyến, tập trung vào sự phát triển của Điện toán đám mây (Cloud Computing), Trí tuệ nhân tạo (AI) và kỹ thuật phần mềm hiện đại.

Sự kiện cung cấp những góc nhìn về lịch sử hình thành AWS, các thách thức trong việc xây dựng hệ thống phân tán quy mô lớn và sự thay đổi vai trò của lập trình viên trong kỷ nguyên AI.

Thông qua phần trao đổi với Tiến sĩ Werner Vogels, người tham dự có cơ hội tìm hiểu về kiến trúc đám mây, độ tin cậy của hệ thống, phát triển phần mềm với sự hỗ trợ của AI và những kỹ năng kỹ thuật cần thiết đối với các kỹ sư trong tương lai.

### Các chủ đề chính và kiến thức tiếp thu

#### 1. Nguồn gốc của AWS và mô hình kinh tế Điện toán đám mây

Tiến sĩ Werner Vogels đã chia sẻ về những thách thức ban đầu của Amazon trong việc quản lý cơ sở hạ tầng quy mô lớn và cách những thách thức này góp phần hình thành AWS.

Khi hệ thống của Amazon ngày càng phát triển, các giải pháp cơ sở hạ tầng truyền thống trở nên khó mở rộng hơn, dẫn đến nhu cầu xây dựng những dịch vụ điện toán phân tán và linh hoạt.

Một số dịch vụ và khái niệm AWS quan trọng được đề cập:

- **Amazon S3:** Cung cấp dịch vụ lưu trữ đối tượng có khả năng mở rộng cho ứng dụng và dữ liệu.
- **Amazon EC2:** Cung cấp tài nguyên máy chủ ảo có thể được triển khai tùy theo yêu cầu của ứng dụng.
- **Amazon DynamoDB:** Là dịch vụ cơ sở dữ liệu NoSQL được quản lý, thiết kế để hỗ trợ các ứng dụng có khả năng mở rộng.
- **Mô hình thanh toán theo mức sử dụng (Pay-as-you-go):** Cho phép khách hàng thanh toán tài nguyên đám mây dựa trên mức sử dụng thực tế, thay vì phải đầu tư toàn bộ chi phí cơ sở hạ tầng ban đầu.

Những sự phát triển này cho thấy AWS đã hình thành từ việc giải quyết các thách thức thực tế về cơ sở hạ tầng và dần trở thành nền tảng cung cấp dịch vụ đám mây cho các tổ chức.

**Kiến thức em học được:**

Phần chia sẻ giúp em hiểu rằng các dịch vụ AWS được phát triển nhằm giải quyết những vấn đề thực tế liên quan đến khả năng mở rộng, quản lý cơ sở hạ tầng và hiệu quả vận hành.

em cũng hiểu rõ hơn cách Điện toán đám mây cho phép các tổ chức quản lý tài nguyên linh hoạt và kiểm soát chi phí cơ sở hạ tầng.

#### 2. Kỹ thuật hệ thống quy mô lớn và độ tin cậy của hệ thống

Một chủ đề quan trọng khác là thiết kế các hệ thống phân tán đáng tin cậy, có khả năng phục vụ số lượng lớn người dùng trong khi vẫn duy trì hiệu suất ổn định.

Tiến sĩ Werner Vogels nhấn mạnh rằng lỗi hệ thống là điều khó tránh khỏi, vì vậy các kỹ sư cần thiết kế kiến trúc có khả năng phục hồi khi xảy ra sự cố bất ngờ.

Một số khái niệm quan trọng được giới thiệu:

- **Tail Latency (Độ trễ ở nhóm yêu cầu chậm nhất):** Đo lường thời gian phản hồi mà người dùng phải trải nghiệm ở phần chậm nhất trong phân phối hiệu suất của hệ thống.
- **High Availability (Tính sẵn sàng cao):** Giúp ứng dụng và dịch vụ tiếp tục hoạt động ngay cả khi một số thành phần gặp sự cố.
- **Single Points of Failure (Điểm lỗi đơn):** Những thành phần mà khi gặp sự cố có thể làm gián đoạn toàn bộ hệ thống.
- **Game Days (Diễn tập sự cố):** Các tình huống sự cố được mô phỏng nhằm đánh giá khả năng chịu lỗi và quy trình khôi phục hệ thống.
- **Automated Failover (Tự động chuyển đổi khi gặp lỗi):** Cho phép hệ thống chuyển sang tài nguyên thay thế khi xảy ra sự cố.

Những phương pháp này giúp các tổ chức cải thiện độ tin cậy và khả năng phục hồi của những ứng dụng quy mô lớn.

**Kiến thức em học được:**

em nhận ra rằng việc xây dựng một hệ thống đám mây đáng tin cậy không chỉ đơn thuần là lựa chọn tài nguyên tính toán có hiệu năng cao.

Các kỹ sư còn phải xem xét những tình huống lỗi có thể xảy ra, giám sát hệ thống, tự động khôi phục và đưa ra các quyết định thiết kế kiến trúc nhằm giảm thiểu gián đoạn dịch vụ.

#### 3. Lập trình viên đa năng trong kỷ nguyên AI (Renaissance Developer)

Sự kiện cũng thảo luận về cách Trí tuệ nhân tạo đang thay đổi lĩnh vực phát triển phần mềm và những kỹ năng mà lập trình viên cần có để tiếp tục thích nghi.

Tiến sĩ Werner Vogels giới thiệu khái niệm **Renaissance Developer**, đề cập đến những kỹ sư kết hợp kiến thức kỹ thuật vững chắc với các kỹ năng giải quyết vấn đề và giao tiếp ở nhiều lĩnh vực.

Một số phẩm chất quan trọng được nhấn mạnh:

- **Continuous Learning (Học tập liên tục):** Liên tục cập nhật những thay đổi trong ngôn ngữ lập trình, công nghệ đám mây và công cụ phát triển AI.
- **Systems Thinking (Tư duy hệ thống):** Hiểu cách các thành phần khác nhau tương tác trong một hệ thống phần mềm hoàn chỉnh.
- **Communication Skills (Kỹ năng giao tiếp):** Xác định yêu cầu kinh doanh và giải thích rõ ràng những giải pháp kỹ thuật.
- **Problem-Solving Skills (Kỹ năng giải quyết vấn đề):** Đánh giá nhiều phương án khác nhau và lựa chọn giải pháp phù hợp.
- **Ownership (Tinh thần trách nhiệm):** Chịu trách nhiệm về chất lượng phần mềm, bảo mật và hoạt động của hệ thống, kể cả khi sử dụng mã nguồn do AI tạo ra.

Những phẩm chất này giúp lập trình viên thích nghi với môi trường công nghệ ngày càng phức tạp.

**Kiến thức em học được:**

Phần thảo luận giúp em hiểu rằng AI không làm giảm tầm quan trọng của những kiến thức nền tảng trong kỹ thuật phần mềm.

Ngược lại, lập trình viên cần tăng cường khả năng đánh giá giải pháp, hiểu kiến trúc hệ thống, giao tiếp hiệu quả và chịu trách nhiệm đối với các ứng dụng mà mình phát triển.

#### 4. Phát triển phần mềm với AI và kiểm tra mã nguồn có trách nhiệm

Một chủ đề khác được thảo luận là cách các công cụ AI hỗ trợ lập trình viên trong quá trình phát triển phần mềm.

AI có thể đẩy nhanh quá trình xây dựng nguyên mẫu, đề xuất mã nguồn và giúp lập trình viên khám phá những cách triển khai khác nhau.

Tuy nhiên, các kết quả do AI tạo ra vẫn cần được kiểm tra cẩn thận trước khi áp dụng vào ứng dụng thực tế.

Một số phương pháp quan trọng được đề cập:

- **Rapid Prototyping (Xây dựng nguyên mẫu nhanh):** Sử dụng công cụ AI để tạo các thiết kế ứng dụng ban đầu và thử nghiệm ý tưởng nhanh hơn.
- **Code Review (Kiểm tra mã nguồn):** Xem xét mã nguồn được tạo ra nhằm phát hiện những lỗi tiềm ẩn và vấn đề bảo mật.
- **Risk Assessment (Đánh giá rủi ro):** Đánh giá những thay đổi trong phần mềm dựa trên tác động tiềm ẩn về kỹ thuật và hoạt động kinh doanh.
- **Testing and Validation (Kiểm thử và xác thực):** Kiểm tra xem giải pháp do AI tạo ra có đáp ứng yêu cầu chức năng và độ tin cậy hay không.
- **Engineering Responsibility (Trách nhiệm của kỹ sư):** Đảm bảo lập trình viên vẫn chịu trách nhiệm đối với chất lượng phần mềm mà họ phát triển và triển khai.

**Kiến thức em học được:**

em nhận ra rằng các công cụ AI có thể cải thiện hiệu quả phát triển phần mềm, nhưng không nên chấp nhận kết quả do AI tạo ra mà không kiểm chứng.

Lập trình viên vẫn cần xem xét, kiểm thử và đánh giá mã nguồn được tạo ra để đảm bảo ứng dụng an toàn, đáng tin cậy và phù hợp với mục đích sử dụng.

### Kết quả học tập và những bài học quan trọng

Buổi trò chuyện đã mang đến nhiều kiến thức và góc nhìn hữu ích về Điện toán đám mây, hệ thống phân tán và tương lai của kỹ thuật phần mềm trong kỷ nguyên AI.

**Những bài học chính em tiếp thu được bao gồm:**

- Hiểu cách AWS được phát triển từ những thách thức thực tế về khả năng mở rộng cơ sở hạ tầng và hiệu suất hệ thống.
- Tìm hiểu lợi ích kinh tế của Điện toán đám mây và cách quản lý tài nguyên theo mức sử dụng.
- Nhận thức được tầm quan trọng của tính sẵn sàng cao, kiểm thử khả năng phục hồi và tự động khôi phục trong thiết kế kiến trúc đám mây.
- Hiểu khái niệm Renaissance Developer và vai trò của việc học tập liên tục, kỹ năng giao tiếp và tư duy hệ thống.
- Tìm hiểu cách các công cụ AI hỗ trợ xây dựng nguyên mẫu và phát triển phần mềm, đồng thời vẫn yêu cầu kiểm tra và kiểm thử mã nguồn.
- Nhận thức rằng lập trình viên vẫn chịu trách nhiệm về chất lượng, bảo mật và độ tin cậy của phần mềm khi sử dụng các giải pháp do AI tạo ra.

Nhìn chung, sự kiện giúp em mở rộng hiểu biết về kiến trúc đám mây và trách nhiệm kỹ thuật của các kỹ sư phần mềm khi làm việc với AWS và công nghệ AI.

Sự kiện cũng khuyến khích em tiếp tục nâng cao kiến thức về Điện toán đám mây, đồng thời phát triển khả năng đánh giá và ứng dụng các công nghệ mới vào những dự án phần mềm trong tương lai.

### Hình ảnh tham dự sự kiện

Hình ảnh sau ghi lại sự kiện **Fireside Chat with Dr. Werner: Navigating the Future of Cloud & AI in Vietnam** vào ngày 2 tháng 10 năm 2026.

![Hình ảnh cá nhân tham dự sự kiện First Cloud AI Journey](/my_aws_workshop/images/4-EventParticipated/event-4/event4.jpg)

*Hình 1. Hình ảnh tham dự trực tiếp buổi trò chuyện cùng Tiến sĩ Werner Vogels về tương lai của Điện toán đám mây và Trí tuệ nhân tạo tại Việt Nam.*