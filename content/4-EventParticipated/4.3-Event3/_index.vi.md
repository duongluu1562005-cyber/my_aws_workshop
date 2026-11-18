---
title: "Bài Thu Hoạch 3"
date: 2026-11-14
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---
# First Cloud AI Journey – Weekly Meet Up

- **Thời gian:** Thứ Bảy, ngày 14/11/2026
- **Địa điểm:** Tầng 26, Bitexco Financial Tower, TP. Hồ Chí Minh
- **Đơn vị tổ chức:** First Cloud AI Journey (FCAJ)
- **Người chia sẻ:** Các anh chị trong đội ngũ First Cloud AI Journey (FCAJ)
- **Vai trò:** Người tham dự trực tiếp

### Mục Đích Của Sự Kiện

First Cloud AI Journey – Weekly Meet Up là buổi gặp gỡ và chia sẻ kiến thức dành cho những người quan tâm đến AWS, Cloud Computing và trí tuệ nhân tạo (AI).

Chương trình tập trung vào cách xây dựng và triển khai các hệ thống AI trong môi trường doanh nghiệp, từ việc tối ưu chi phí, quản lý tài nguyên AWS đến xây dựng hệ thống AI Agent có khả năng phối hợp và xử lý nhiều nhiệm vụ.

Thông qua những chia sẻ từ các anh chị trong cộng đồng FCAJ, người tham dự có cơ hội tiếp cận các kiến thức và kinh nghiệm thực tế về việc ứng dụng AI và AWS vào các dự án công nghệ.

### Nội Dung Kiến Thức Đã Học

**1. Sự khác biệt giữa AI trong học tập và AI trong doanh nghiệp**

Trong môi trường học tập, các mô hình AI thường được xây dựng và thử nghiệm với dữ liệu có sẵn, số lượng người dùng nhỏ. Tuy nhiên, khi triển khai trong doanh nghiệp, hệ thống cần đáp ứng lượng truy cập lớn, hoạt động ổn định và đảm bảo an toàn dữ liệu.

Một số dịch vụ AWS được giới thiệu gồm:

- **AWS Lambda:** Chạy chương trình mà không cần tự quản lý máy chủ.
- **Auto Scaling:** Tự động điều chỉnh tài nguyên theo lượng truy cập.
- **Load Balancer:** Phân phối yêu cầu đến nhiều máy chủ để hạn chế quá tải.
- **AWS KMS:** Hỗ trợ quản lý khóa mã hóa để bảo vệ dữ liệu.
- **AWS IAM:** Quản lý người dùng và phân quyền truy cập tài nguyên AWS.
- **Amazon Bedrock Guardrails:** Hỗ trợ kiểm soát nội dung và tăng tính an toàn khi sử dụng AI.

Những dịch vụ này giúp hệ thống AI có khả năng mở rộng, vận hành ổn định và đáp ứng tốt hơn nhu cầu thực tế của doanh nghiệp.

**2. Sử dụng AI hợp lý để tối ưu chi phí**

Một nội dung đáng chú ý của chương trình là cách lựa chọn mô hình AI phù hợp với từng công việc, thay vì sử dụng mô hình lớn cho tất cả yêu cầu.

Các anh chị đã minh họa cách tổ chức hệ thống AI thông qua mô hình nhà hàng:

- **Head Chef:** Mô hình AI lớn đóng vai trò điều phối, phân chia công việc cho các mô hình khác.
- **Main Course, Dessert, Cashier:** Các mô hình AI nhỏ đảm nhiệm những nhiệm vụ chuyên biệt với chi phí thấp hơn.
- **Caching:** Lưu lại kết quả của những câu hỏi thường gặp để tránh xử lý lặp lại nhiều lần.

Cách tổ chức này giúp giảm chi phí sử dụng AI, cải thiện tốc độ phản hồi và tận dụng tài nguyên hiệu quả hơn.

**3. Xây dựng hệ thống Multi-Agent với Strands Agents**

Chương trình giới thiệu cách kết hợp Strands Agents và FastAPI để xây dựng hệ thống gồm nhiều AI Agent, trong đó mỗi Agent được giao một nhiệm vụ cụ thể.

Một số Agent được đề cập gồm:

- **qa_agent:** Sử dụng FAISS để tìm kiếm thông tin trong tài liệu và hỗ trợ trả lời các câu hỏi về chính sách.
- **ticket_agent:** Hỗ trợ xử lý yêu cầu, đồng thời có cơ chế phê duyệt của con người đối với những thao tác quan trọng.
- **research_agent:** Sử dụng Tavily để tìm kiếm và tổng hợp thông tin từ Internet.

Thay vì để một AI Agent thực hiện tất cả công việc, hệ thống có thể phân chia yêu cầu cho các Agent chuyên biệt. Điều này giúp quy trình xử lý rõ ràng hơn, đồng thời dễ kiểm soát và mở rộng khi cần thiết.

**4. Quản lý bảo mật và bộ nhớ AI Agent bằng Amazon Bedrock AgentCore**

Một nội dung khác được chia sẻ là cách quản lý danh tính, quyền truy cập và bộ nhớ khi triển khai hệ thống AI Agent.

Các thành phần được giới thiệu gồm:

- **AgentCore Identity:** Hỗ trợ xác thực danh tính và quản lý quyền truy cập khi Agent làm việc với các dịch vụ bên ngoài.
- **AgentCore Gateway:** Cung cấp cách kết nối và quản lý các công cụ, API mà AI Agent có thể sử dụng.
- **OAuth2 và Amazon Cognito:** Hỗ trợ quá trình xác thực và cấp quyền truy cập an toàn.
- **Workload Access Token:** Được sử dụng trong quá trình xác thực và cấp quyền cho các tác vụ của hệ thống.
- **AgentCore Memory:** Hỗ trợ quản lý thông tin trong phiên làm việc và lưu trữ những thông tin cần thiết để sử dụng lâu dài.

Nội dung này cho thấy việc xây dựng AI Agent không chỉ tập trung vào khả năng trả lời câu hỏi mà còn cần quan tâm đến bảo mật, xác thực người dùng, quản lý dữ liệu và kiểm soát các hành động của Agent.

### Kết Quả Và Kiến Thức Thu Nhận Được

Buổi chia sẻ mang lại nhiều kiến thức và kinh nghiệm thực tế, bao gồm:

- Hiểu rõ hơn sự khác biệt giữa mô hình AI thử nghiệm trong môi trường học tập và hệ thống AI triển khai thực tế trong doanh nghiệp.
- Biết thêm cách tối ưu chi phí bằng việc kết hợp mô hình AI lớn, mô hình nhỏ và lưu trữ kết quả của những yêu cầu lặp lại.
- Nắm được cách tổ chức hệ thống Multi-Agent, phân chia nhiệm vụ và kiểm soát quá trình hoạt động của từng Agent.
- Có thêm kiến thức về xác thực, phân quyền, bảo mật và quản lý bộ nhớ trong hệ thống AI Agent.
- Hiểu thêm cách kết hợp các dịch vụ AWS để xây dựng hệ thống AI có khả năng mở rộng và hoạt động ổn định.
- Có cơ hội giao lưu, học hỏi kinh nghiệm từ các anh chị trong cộng đồng FCAJ và những người tham dự, đồng thời tiếp cận thêm các giải pháp AWS và AI có thể ứng dụng vào kiến trúc dự án thực tập.


### Một Số Hình Ảnh Tham Gia Sự Kiện

![Slide Use AI Wisely tại AWS First Cloud AI Journey](/my_aws_workshop/images/4-EventParticipated/event-3/anhslide.jpg)

*Hình 1. Slide "Use AI Wisely" giới thiệu cách sử dụng mô hình AI lớn để điều phối các mô hình nhỏ theo từng nhiệm vụ, kết hợp lưu trữ kết quả câu hỏi lặp lại nhằm tối ưu chi phí và hiệu quả xử lý.*

![Ảnh cá nhân tại AWS First Cloud AI Journey](/my_aws_workshop/images/4-EventParticipated/event-3/anhcanhan.jpg)

*Hình 2. Hình ảnh cá nhân tại sự kiện First Cloud AI Journey – Weekly Meet Up ngày 14/11/2026.*
