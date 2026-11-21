---
title: "Đề xuất dự án"
date: 2026-19-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Tindy — Nền tảng AWS giúp tìm kiếm dự án và đồng đội

## 1. Tóm tắt điều hành

Tindy là một ứng dụng web giúp sinh viên và những người mới bắt đầu sự nghiệp tìm kiếm các dự án phù hợp với kỹ năng và thời gian có thể tham gia của mình. Trưởng dự án đăng các vị trí đang tuyển, theo dõi đơn ứng tuyển và gửi lời mời. Khi một ứng viên được chấp nhận, Tindy cập nhật thành viên dự án và số chỗ đã tuyển của vị trí để cả hai người dùng đều nhìn thấy cùng một trạng thái.

Bản demo dự kiến chạy tại **US East (N. Virginia), `us-east-1`**. CloudFront phân phối giao diện React từ S3 và chuyển các yêu cầu động tới API ASP.NET Core trên ECS Fargate. API lưu dữ liệu nghiệp vụ trong RDS for PostgreSQL. Bucket media riêng tư lưu tài liệu, trong khi GuardDuty quét các tệp PDF và Amazon Bedrock tạo bản nháp gợi ý hồ sơ.

## 2. Vấn đề và phạm vi

### Vấn đề

Thông tin tuyển thành viên thường phân tán trên các nhóm chat, bài đăng và biểu mẫu. Ứng viên khó xác định vị trí nào còn tuyển, yêu cầu những kỹ năng gì và cần bao nhiêu giờ mỗi tuần. Trưởng dự án phải hỏi lại cùng một thông tin và theo dõi đơn ứng tuyển, lời mời và số chỗ còn trống qua nhiều kênh. Tindy tập hợp các thông tin này theo từng dự án và vị trí.

### Phạm vi bản demo

| Quy trình | Hành động của người dùng | Kết quả trong Tindy |
| :--- | :--- | :--- |
| Tạo hồ sơ | Thành viên hoàn tất bước khởi tạo hồ sơ và nhập kỹ năng, vai trò mong muốn, kinh nghiệm và thời gian có thể tham gia. Nội dung văn bản hoặc CV PDF đủ điều kiện có thể được dùng để tạo bản nháp. | Hồ sơ được cập nhật sau khi chủ sở hữu xem lại và lưu. |
| Ứng tuyển vào vị trí | Trưởng dự án đăng một vị trí đang tuyển. Thành viên tìm dự án, chọn vị trí và xác nhận ứng tuyển. Trưởng dự án xem xét đơn. | Đơn đã gửi ở trạng thái Pending. Khi được chấp nhận, thành viên xuất hiện trong dự án và số chỗ đã tuyển của vị trí tăng lên. |
| Mời đồng đội | Trưởng dự án chọn dự án và vị trí, tìm ứng viên đã đồng ý cho phép được tìm thấy, rồi gửi lời mời. Ứng viên mở thông báo và phản hồi. | Lời mời ở trạng thái Pending cho đến khi được xử lý. Khi ứng viên chấp nhận và API xác minh đủ điều kiện, ứng dụng tạo tư cách thành viên và cập nhật số chỗ đã tuyển. |
| Giao tiếp | Thành viên trao đổi tin nhắn trực tiếp hoặc tham gia cuộc trò chuyện dự án khi có quyền. | Người dùng được cấp quyền nhìn thấy tin nhắn mới trong đúng cuộc trò chuyện. |
| Vận hành bản demo | Người vận hành phát hành giao diện và API, kiểm tra tình trạng dịch vụ, sao lưu dữ liệu và dựng lại môi trường khi cần. | Chi tiết phát hành, kết quả xác minh và thông tin tham chiếu bản sao lưu hỗ trợ khôi phục. |

### Cách hiểu điểm phù hợp

API tính điểm cho từng vị trí với bốn trọng số cơ bản: kỹ năng bắt buộc 40%, vai trò mong muốn 30%, thời gian có thể tham gia 20% và kỹ năng tùy chọn 10%. Nếu một vị trí không có kỹ năng trong một nhóm hoặc không thể tính thời gian tham gia, API chuẩn hóa các trọng số còn lại có thể áp dụng. Nếu không có vai trò mong muốn, tiêu chí đó đóng góp 0 điểm.

Ví dụ, một ứng viên đáp ứng toàn bộ kỹ năng bắt buộc và tùy chọn cùng vai trò mong muốn, đồng thời có thể đóng góp 5 trong số 10 giờ mỗi tuần mà dự án yêu cầu, sẽ đạt 90/100 điểm. Điểm số giải thích mức độ phù hợp. Người dùng đưa ra quyết định tuyển dụng và API kiểm tra lại quyền truy cập, trạng thái dự án và số chỗ còn lại khi xử lý chấp nhận.

### Lợi ích và cách đánh giá

Đối với thành viên, chức năng khám phá dự án hiển thị vị trí đang tuyển, số giờ dự kiến mỗi tuần và những kỹ năng còn thiếu. Một hồ sơ có thể được sử dụng cho nhiều vị trí. Đối với trưởng dự án, đơn ứng tuyển và lời mời được tổ chức theo dự án và vị trí. Khi tuyển dụng thành công, có thể đối chiếu trạng thái đơn, tư cách thành viên dự án và số chỗ đã tuyển với cùng các bản ghi nghiệp vụ.

Giai đoạn thử nghiệm sẽ đo lường giá trị của các quy trình này. Đối với tuyển dụng, ghi lại số bước và thời gian từ khi chọn vị trí cho đến khi hai bên xác nhận kết quả. Đối với nhập hồ sơ, sử dụng cùng một bộ thông tin mẫu để so sánh nhập thủ công với việc tạo bản nháp, xem xét và chỉnh sửa gợi ý AI. Chi phí hạ tầng, thời gian thiết lập demo và công sức cần thiết để xem xét gợi ý cũng được đưa vào đánh giá. Các phép đo này hỗ trợ đánh giá giá trị sau giai đoạn thử nghiệm.

## 3. Kiến trúc giải pháp

![Sơ đồ kiến trúc Tindy](/my_aws_workshop/images/2-Proposal/3.png)

*Sơ đồ kiến trúc Tindy cho bản đề xuất. Phần diễn giải theo số thứ tự dưới đây phản ánh cấu hình repository và mã nguồn ứng dụng.*

| Bước | Thành phần | Luồng yêu cầu hoặc dữ liệu |
| :--- | :--- | :--- |
| 1 | Amazon CloudFront và bucket S3 frontend | Người dùng truy cập trang web qua HTTPS. CloudFront phân phối bản build React/Vite từ bucket S3 riêng tư. Origin Access Control (OAC) giới hạn quyền đọc frontend cho distribution. |
| 2 | CloudFront VPC Origin và Application Load Balancer | Các đường dẫn động như `/api/*`, `/hubs/*` và callback đăng nhập đi tới ALB nội bộ. Ứng dụng web và API dùng chung hostname, đồng thời tắt bộ nhớ đệm cho những đường dẫn động này. [CloudFront VPC origins](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html) |
| 3 | Amazon ECS, AWS Fargate và Amazon ECR | ECS chạy container API ASP.NET Core .NET 10 từ ECR. API xử lý xác thực, hồ sơ, khám phá dự án, tuyển thành viên và chat SignalR. Health check của ALB theo dõi tình trạng task. |
| 4 | Bucket S3 media, GuardDuty, EventBridge và SQS | Bucket riêng tư lưu trữ media. Các tệp PDF chứng chỉ và PDF đầu vào AI đi qua quy trình quét. EventBridge chuyển kết quả quét của GuardDuty tới SQS để worker cập nhật trạng thái tài liệu. Hàng đợi thư lỗi (DLQ) lưu các thông điệp xử lý thất bại để kiểm tra. |
| 5 | Amazon Bedrock | Đối với PDF đầu vào AI, backend xác minh kết quả quét sạch của đúng đối tượng S3 trước khi cho phép trích xuất. Người dùng cũng có thể gửi văn bản. Backend kiểm tra đầu ra và trả về bản nháp các trường hồ sơ, kỹ năng, kinh nghiệm, chứng chỉ và cảnh báo để xem xét. |
| 6 | Amazon RDS for PostgreSQL | PostgreSQL 17 lưu dữ liệu có cấu trúc như tài khoản, hồ sơ, dự án, vị trí, đơn ứng tuyển, lời mời và tin nhắn. RDS dùng các subnet riêng tư, yêu cầu TLS và hạn chế truy cập bằng security group. |

### Ba luồng đi qua sơ đồ

**Truy cập ứng dụng và cập nhật dữ liệu (1 → 2 → 3 → 6).** Trình duyệt tải frontend từ S3 qua CloudFront. Khi người dùng mở dự án, ứng tuyển hoặc kết nối chat, CloudFront chuyển yêu cầu động qua VPC origin tới ALB nội bộ. ALB chọn một task Fargate đang hoạt động tốt. API xác minh JWT và quyền đối với dự án hoặc cuộc trò chuyện trước khi đọc hoặc ghi dữ liệu PostgreSQL. Web, API và chat dùng chung hostname.

**Tải chứng chỉ lên và kiểm tra PDF (3 → 4).** API nhận media và ghi PDF vào bộ lưu trữ S3 riêng tư. GuardDuty quét tệp, EventBridge chuyển kết quả đến SQS và worker đối chiếu kết quả với đối tượng hoặc phiên bản S3 rồi cập nhật trạng thái tài liệu. API chặn các thao tác đọc hoặc trích xuất yêu cầu kết quả quét sạch khi tài liệu còn đang chờ hoặc không đủ điều kiện. DLQ giữ các thông điệp vẫn xử lý thất bại sau số lần thử lại đã cấu hình.

**Trích xuất hồ sơ (4 → 5 → 3 → 6).** PDF đầu vào AI có thể được trích xuất sau khi có kết quả quét sạch. Người dùng cũng có thể gửi văn bản trực tiếp. Backend gọi Bedrock, xác thực phản hồi và trả về bản nháp các trường hồ sơ, kỹ năng, kinh nghiệm và chứng chỉ. API ghi những trường được chọn vào PostgreSQL sau khi người dùng xem lại, chỉnh sửa và xác nhận lưu.

**Mạng và tính sẵn sàng.** VPC trải rộng trên hai Availability Zone. ALB là ALB nội bộ. Các task Fargate sử dụng subnet công khai và nhận địa chỉ IPv4 công khai trong cấu hình hiện tại. Security group giới hạn lưu lượng vào API chỉ từ ALB. Runbook demo sử dụng một task Fargate và template cơ sở dữ liệu cấu hình RDS Single-AZ. Hai vùng trong sơ đồ thể hiện vị trí đặt subnet. Trước khi mở rộng quy mô, cần xác minh SignalR hoạt động giữa nhiều task và quá trình xử lý sự kiện nền.

**Bảo mật và vận hành.** Tài nguyên frontend và media sử dụng hai bucket S3 riêng biệt. Backend xác thực người dùng bằng JWT và thực thi quyền truy cập ở cấp tài nguyên. Secrets Manager lưu thông tin kết nối và cấu hình nhạy cảm. GitHub Actions đảm nhận các vai trò triển khai thông qua OIDC. CloudWatch thu thập log và giám sát tình trạng target, phản hồi HTTP 5xx và DLQ. SNS gửi cảnh báo khi thông báo đã được cấu hình.

### Các thành phần và lý do lựa chọn thiết kế

| Lớp | Trách nhiệm trong bản demo | Lý do thiết kế này phù hợp |
| :--- | :--- | :--- |
| Web | React và Vite tạo các tài nguyên tĩnh. CloudFront đọc từ S3 thông qua Origin Access Control và phục vụ các route của ứng dụng. | Frontend có thể được phát hành độc lập. Tài nguyên tĩnh và dữ liệu phiên động sử dụng các cache behavior khác nhau. |
| API | ASP.NET Core chạy trên ECS Fargate. ECR lưu các image phát hành. ALB kiểm tra mức độ sẵn sàng và chuyển lưu lượng đến các task khỏe mạnh. | Có thể truy vết phiên bản đang chạy thông qua image digest và kiểm tra bản sửa đổi mới trong quá trình triển khai. |
| Dữ liệu nghiệp vụ | RDS for PostgreSQL lưu hồ sơ, quan hệ dự án, trạng thái tuyển dụng và lịch sử chat. | Có thể kiểm tra các quan hệ giữa dự án, vị trí và thành viên khi ứng dụng ghi dữ liệu. Migration quản lý thay đổi schema cùng các bản phát hành. |
| Tệp và sự kiện | Bucket media riêng tư lưu tệp. GuardDuty quét PDF, EventBridge gửi sự kiện quét và consumer SQS cập nhật trạng thái tài liệu. | Việc tải lên và hoàn tất quét diễn ra ở những thời điểm khác nhau. Hàng đợi cho thấy công việc đang chờ và DLQ là nơi kiểm tra lỗi. |
| AI | Khi được bật, backend gọi Bedrock và trả về bản nháp có cấu trúc. | Người dùng có thể đối chiếu gợi ý với tài liệu nguồn. Backend xác thực các trường hồ sơ và ánh xạ kỹ năng, vai trò trước khi người dùng xem xét. |

### Ranh giới dữ liệu, truy cập và khả năng quan sát

**Vị trí lưu dữ liệu.** PostgreSQL lưu danh tính, hồ sơ, quan hệ dự án và trạng thái quy trình. Bucket S3 frontend chứa HTML và các bundle tĩnh. Bucket S3 media chứa nội dung tệp. Manifest khôi phục cần cả tham chiếu snapshot RDS và các tham chiếu đối tượng S3 liên quan để nhóm có thể xác minh rằng các bản ghi được khôi phục vẫn liên kết với đúng tệp.

**Truy cập theo lớp.** Security group kiểm soát truy cập mạng giữa ALB, task API và cơ sở dữ liệu. Backend xác thực người dùng và cấp quyền truy cập dự án, hồ sơ hoặc cuộc trò chuyện tương ứng. ECS execution role hỗ trợ hoạt động khởi chạy như kéo image, khởi tạo log và lấy secret đã cấu hình. ECS task role bao gồm những thao tác AWS mà mã ứng dụng cần, như truy cập media, xử lý kết quả quét và gọi AI khi được bật. Quyền OIDC mà GitHub Actions sử dụng để phát hành được quản lý độc lập với quyền lúc chạy.

**Tín hiệu vận hành.** Tình trạng target và phản hồi 5xx giúp khoanh vùng sự cố thuộc định tuyến hay ứng dụng. ECS events và CloudWatch Logs cho biết một task có khởi động được, bị dừng hay trả về lỗi. Đối với PDF, đối chiếu trạng thái tài liệu trong Tindy với kết quả GuardDuty và các thông điệp đang chờ trong SQS hoặc DLQ. Thông báo tuyển dụng xuất hiện trong ứng dụng thông qua API polling. Chat sử dụng SignalR. SNS cung cấp cảnh báo vận hành khi được cấu hình.

### Những đánh đổi trong bản demo và điều kiện để mở rộng

| Quyết định | Ưu tiên của bản demo | Bằng chứng cần có trước khi thay đổi |
| :--- | :--- | :--- |
| Một task Fargate phía sau ALB | Giữ quy mô tài nguyên chạy liên tục ở mức nhỏ và đơn giản hóa chat thời gian thực trong demo. | Đo lưu lượng và độ trễ. Xác minh phân phối sự kiện SignalR giữa các task và kiểm tra tính nhất quán của xử lý sự kiện nền trước khi tăng số task. |
| RDS Single-AZ | Giữ cấu hình ban đầu đơn giản và duy trì quy trình khôi phục bằng snapshot có tài liệu hướng dẫn. | Xác định thời gian ngừng hoạt động và mức mất dữ liệu chấp nhận được. Ước tính chi phí Multi-AZ và diễn tập khôi phục trước khi cam kết mức sẵn sàng cao hơn. |
| Task API trong subnet công khai, lưu lượng vào thông qua ALB nội bộ | Dùng đường kết nối ra ngoài hiện có cho các tích hợp. Security group hạn chế truy cập tới API. | Trước khi chuyển sang subnet riêng tư, lập danh sách các điểm đích AWS và bên ngoài rồi so sánh endpoint, đường kết nối ra ngoài về kết nối, chi phí và công sức vận hành. |
| Trích xuất AI theo yêu cầu | Xử lý đầu vào khi người dùng yêu cầu bản nháp và cho phép đo thời gian chạy cùng chi phí mỗi lần thử. | Đo số đầu ra không hợp lệ, độ trễ và lượng token sử dụng trước khi cân nhắc hàng đợi xử lý nền. |

## 4. Triển khai và kiểm thử

### Chuẩn bị hạ tầng và dữ liệu

AWS CloudFormation định nghĩa sáu stack: foundation, network, database, integrations, runtime và edge. Foundation và network cung cấp bucket, ECR, IAM và tài nguyên mạng. Database cung cấp PostgreSQL. Integrations xử lý kết quả quét PDF. Runtime triển khai API, ALB và VPC origin. Edge sử dụng origin đó để định tuyến các yêu cầu động khi dịch vụ đã sẵn sàng.

Chọn Fresh để tạo cơ sở dữ liệu, áp dụng migration và nạp dữ liệu demo, hoặc Restore để khôi phục schema và dữ liệu từ snapshot. Đối với bản phát hành thay đổi schema, trước tiên tạo snapshot và chạy EF Core migration trong một ECS task riêng. Xác minh API đang chạy vẫn tương thích với schema đã migration để có thể quay lại phiên bản API trước đó nếu triển khai thất bại.

### Phát hành ứng dụng

Sau khi CI thành công, chọn commit đã được phê duyệt. Build image backend, đẩy lên ECR và ghi lại image digest sử dụng trong ECS task definition.

Xác minh dịch vụ ECS ổn định và các target ALB khỏe mạnh. Khi edge ở chế độ live, kiểm tra `/api/health/ready` trả về HTTP 200 với trạng thái Healthy thông qua CloudFront.

Build frontend bằng Vite, xuất bản lên S3 và vô hiệu hóa cache CloudFront. Kiểm tra trang chủ, trang đăng nhập và JavaScript bundle qua cùng hostname.

Chạy các kịch bản nghiệm thu với tài khoản và PDF mẫu. Ghi lại commit, image digest, phiên bản sửa đổi ECS, kết quả kiểm thử và snapshot ID nếu có. Khi dựng lại bản demo, duy trì edge ở chế độ maintenance cho đến khi API, cơ sở dữ liệu và các tích hợp bắt buộc đã sẵn sàng.

### Yêu cầu kỹ thuật và phi chức năng

**Môi trường phát hành.** Ứng dụng sử dụng React/Vite, ASP.NET Core .NET 10, EF Core và PostgreSQL. Build frontend để sử dụng các đường dẫn API cùng origin. Cung cấp thiết lập môi trường và cấu hình nhạy cảm thông qua quy trình triển khai và AWS Secrets Manager.

**Tính nhất quán.** Việc kết nạp thành viên phải dựa trên trạng thái hiện tại của dự án và vị trí. Kiểm tra cả tư cách thành viên và số chỗ đã tuyển của vị trí sau khi chấp nhận và tải lại.

**Quyền riêng tư.** Sử dụng tài khoản thử nghiệm và tài liệu mẫu cho các buổi demo. Xác minh quyền khi mở tài liệu, yêu cầu bản nháp AI hoặc truy cập cuộc trò chuyện. Giữ thông tin tham chiếu và bằng chứng trạng thái trong bản ghi kiểm thử. Không đưa giá trị secret hay nội dung CV vào các bản ghi đó.

**Khả năng kiểm thử.** Chuẩn bị dữ liệu kiểm thử cho các thao tác thành công và bị từ chối. Bao gồm một vị trí không còn chỗ, ứng viên chưa đồng ý cho phép được tìm thấy, PDF đang chờ quét và người dùng không có quyền truy cập cuộc trò chuyện.

**Khả năng khôi phục.** Ghi lại snapshot và phiên bản liên quan trước khi thay đổi schema. Xác định bộ bản ghi ứng dụng và tệp media mẫu để đối chiếu sau khi Restore.

### Vòng đời giữa các buổi demo

Sau một buổi demo, kiểm tra xem các thao tác ghi của ứng dụng hoặc việc quét PDF có còn đang diễn ra hay không. Khi môi trường sẵn sàng sao lưu, ghi lại snapshot ID, dấu thời gian, trạng thái mã hóa, bản phát hành API và migration hiện tại. Xác nhận các đối tượng S3 và image ECR cần cho điểm khôi phục đó vẫn được giữ lại.

Nếu môi trường bị tắt giữa các buổi demo, chuyển CloudFront sang chế độ maintenance và xác minh hostname của distribution vẫn được giữ. Runbook dự án quy định các bước kiểm tra trước khi gỡ runtime, database và integrations theo thứ tự phụ thuộc. Giữ lại các tài nguyên khôi phục đã thống nhất, gồm snapshot, dữ liệu S3, image ECR và các tham chiếu cấu hình. Ở buổi demo tiếp theo, chọn Restore hoặc Fresh tùy mục tiêu, xác minh endpoint cơ sở dữ liệu và các health check, rồi mở lại edge khi các dịch vụ cần thiết đã sẵn sàng.

## 5. Kế hoạch Sprint và các mốc triển khai

Quá trình phát triển Tindy bắt đầu vào thứ Hai, ngày 5 tháng 10 năm 2026 (Tuần 4). Kế hoạch chia Tuần 4–9 thành ba sprint phát triển, mỗi sprint kéo dài hai tuần. Tuần 10 (16–22 tháng 11 năm 2026) dành cho triển khai lên AWS, xác minh sau triển khai và hoàn thiện bản demo.

| Sprint | Thời gian | Công việc chính | Sản phẩm bàn giao cuối sprint |
| :--- | :--- | :--- | :--- |
| Sprint 1 · Nền tảng ứng dụng | Tuần 4–5 · 5–18/10/2026 | Bắt đầu phát triển. Thống nhất luồng người dùng và mô hình dữ liệu. Thiết lập React/Vite, ASP.NET Core và PostgreSQL. Xây dựng đăng nhập, khởi tạo hồ sơ, hồ sơ và danh mục kỹ năng, vai trò. | Người dùng mẫu có thể đăng nhập, hoàn tất khởi tạo và lưu hồ sơ thông qua API. |
| Sprint 2 · Dự án và tuyển thành viên | Tuần 6–7 · 19/10–1/11/2026 | Xây dựng quản lý dự án và vị trí, khám phá dự án, chấm điểm phù hợp, đơn ứng tuyển, tìm đồng đội và lời mời. Kiểm tra lại điều kiện tuyển dụng và số chỗ khi chấp nhận. | Dữ liệu mẫu minh họa các luồng ứng tuyển và lời mời. Việc chấp nhận thành công cập nhật chính xác tư cách thành viên và số chỗ đã tuyển. |
| Sprint 3 · Tích hợp và chuẩn bị phát hành | Tuần 8–9 · 2–15/11/2026 | Hoàn thành chat SignalR và thông báo trong ứng dụng. Tích hợp lưu trữ tệp, quét PDF và bản nháp hồ sơ Bedrock. Chuẩn bị template CloudFormation, cấu hình CI/CD và dữ liệu demo. | Bản ứng viên phát hành, các kịch bản kiểm thử quy trình cốt lõi và cấu hình AWS đã được rà soát. |
| Sprint 4 · Triển khai và hoàn thiện | Tuần 10 · 16–22/11/2026 | Triển khai frontend và API lên AWS. Kiểm tra CloudFront, ALB, ECS và RDS. Chạy kịch bản nghiệm thu qua hostname demo, xử lý vấn đề ảnh hưởng đến quy trình cốt lõi và ghi lại thông tin phát hành. | Bản demo AWS đã triển khai với API readiness trả về HTTP 200 và trạng thái Healthy, kết quả nghiệm thu và hướng dẫn tái triển khai. |

Mốc hoàn thành dự án ở Tuần 10. Từ Tuần 11 đến khi kết thúc kỳ thực tập vào ngày 14 tháng 12 năm 2026, công việc báo cáo bao gồm sơ đồ kiến trúc, kịch bản demo, kết quả nghiệm thu, thông tin phát hành và các giả định chi phí.

## 6. Ước tính ngân sách

Kịch bản chạy liên tục sử dụng **730 giờ mỗi tháng** tại `us-east-1`. Các số liệu lấy từ bản ước tính workload `Tindy-Always-on` xuất từ AWS Pricing Calculator ngày 18 tháng 11 năm 2026, theo đơn giá trước chiết khấu và chưa bao gồm thuế hay credit.

| Hạng mục chi phí | Cơ sở tính | Chi phí tham khảo |
| :--- | :--- | :--- |
| AWS Fargate | Một task Linux/x86 với 0,5 vCPU và 2 GiB RAM. Ước tính gồm 365 vCPU-giờ và 1.460 GB-giờ bộ nhớ. | 21,26 USD/tháng |
| Application Load Balancer | Một ALB nội bộ trong 730 giờ. | 16,43 USD/tháng |
| IPv4 công khai | Một địa chỉ IPv4 công khai đang sử dụng trong 730 giờ. | 3,65 USD/tháng |
| RDS for PostgreSQL | Một `db.t4g.micro` Single-AZ trong 730 giờ (11,68 USD) và 20 GiB gp3 (2,30 USD). | 13,98 USD/tháng |
| Edge, media, bảo mật và AI | CloudFront, S3, ECR, Secrets Manager, GuardDuty, EventBridge, SQS, CloudWatch và Bedrock. Phí thay đổi theo dung lượng, yêu cầu, log, lượt quét và token. | Theo mức sử dụng |

Chi phí nền tảng khi chạy liên tục là **55,32 USD mỗi tháng**. Con số này chưa bao gồm ALB LCU, phí CPU credit RDS nếu có, dung lượng sao lưu hoặc snapshot bổ sung và các dịch vụ tính theo mức sử dụng được liệt kê ở trên. Khi ước tính kịch bản demo theo lịch, cần tính cả snapshot và các tài nguyên được giữ lại. [AWS Pricing Calculator]([https://calculator.aws/#/estimate](https://calculator.aws/#/estimate?id=9f501b7b4cfa90d4c3e108ec22f54c361374f883))

Bản ước tính công khai hiển thị **55,34 USD mỗi tháng**. So với bản xuất từ AWS Console, chi phí Fargate và ALB hiển thị đều cao hơn 0,01 USD. Phép tính ALB công khai sử dụng mức lưu lượng giả định 0,001 GB/giờ vì biểu mẫu yêu cầu một giá trị LCU dương.

Với lưu lượng thử nghiệm, dự trù khoảng **58 USD mỗi tháng** cho hạ tầng chạy liên tục và các dịch vụ hỗ trợ đã được mô hình hóa. Bản xuất Cost Explorer từ ngày 1–17 tháng 11 năm 2026 cho thấy hoạt động hạ tầng cốt lõi tập trung trong ngày 11–17 tháng 11. Xem tuần đó là đại diện cho tháng 30 ngày. Cộng 1,60 USD cho bốn secret được lưu và quy đổi phần chi phí sử dụng khác đã nhập trong AWS Pricing Calculator (0,1804 USD × 30/7, bao gồm các lệnh gọi API Secrets Manager). Con số lập kế hoạch này không bao gồm ALB LCU, phí EventBridge chưa xác minh, CPU credit RDS, dung lượng sao lưu hoặc snapshot bổ sung và thuế.

### So sánh các kịch bản vận hành

| Đầu vào chi phí | Kịch bản chạy liên tục | Kịch bản demo theo lịch |
| :--- | :--- | :--- |
| Fargate và IPv4 công khai | Mô hình hóa runtime theo tháng tham chiếu 730 giờ. | Tính số giờ task chạy thực tế, gồm cả thiết lập và xác minh trước mỗi buổi demo. |
| ALB và RDS | Tính số giờ ALB và cơ sở dữ liệu được cấp phát hoặc chạy, cộng phí phụ thuộc cấu hình. | Ghi riêng thời gian tồn tại của ALB, thời gian chạy cơ sở dữ liệu và dung lượng snapshot được giữ giữa các buổi demo. |
| Lưu trữ và cấu hình được giữ lại | Đo dung lượng S3 và ECR, bản sao lưu và các thành phần cấu hình được giữ lại. | Bao gồm tài nguyên được giữ để khôi phục khi edge ở chế độ maintenance. |
| Lưu lượng, quét và AI | Sử dụng mức cơ sở xác định cho yêu cầu, tải xuống, quét và token. | Đo hoạt động mỗi phiên và ghi nhận xử lý lặp. Giữ nguyên các giả định sử dụng khi so sánh chi phí runtime. |

Sau thử nghiệm, so sánh chi phí thực tế theo dịch vụ với thời gian tài nguyên được cấp phát. Phân loại chênh lệch theo runtime, lưu trữ, lưu lượng hoặc xử lý tài liệu. AWS Budgets có thể cảnh báo tại ngưỡng đã thống nhất, còn Cost Explorer cung cấp phân tích chi tiết để tìm nguyên nhân chênh lệch.

## 7. Đánh giá rủi ro

| Rủi ro | Tác động | Biện pháp giảm thiểu |
| :--- | :--- | :--- |
| API hoặc cơ sở dữ liệu gián đoạn trong cấu hình demo | Người dùng tạm thời không thể truy cập các quy trình ứng dụng. | Theo dõi readiness, log ECS và tình trạng ALB. Xác định quy trình khôi phục snapshot và thời gian ngừng hoạt động chấp nhận được. |
| PDF bị từ chối, quét thất bại hoặc xử lý chậm | Chứng chỉ hoặc CV chưa thể sử dụng. | Hiển thị trạng thái quét, chặn đọc và trích xuất cho đến khi đủ điều kiện, theo dõi DLQ và xác minh đối tượng S3 tương ứng. |
| Gợi ý AI không đầy đủ hoặc không chính xác | Thông tin sai có thể được đưa vào hồ sơ nếu không được xem lại. | Xác thực cấu trúc đầu ra và ánh xạ danh mục kỹ năng, sau đó yêu cầu người dùng xem lại và cho phép chỉnh sửa trước khi lưu. |
| Migration hoặc bản phát hành không tương thích | Các phiên bản web, API và schema cơ sở dữ liệu có thể không tương thích. | Tạo snapshot trước khi thay đổi, phát hành theo thứ tự tương thích và ghi lại commit, digest và revision để khôi phục. |
| Tài nguyên tiếp tục phát sinh phí sau demo | ALB, compute, cơ sở dữ liệu, log hoặc snapshot có thể tiếp tục bị tính phí. | Theo dõi ngân sách, xác định tài nguyên được giữ lại và kiểm tra hóa đơn sau khi dọn dẹp. |

### Phân loại sự cố và khôi phục

**Xác định đường đi bị ảnh hưởng.** Kiểm tra API readiness, tình trạng target ALB và ECS trước tiên. Nếu web và API vẫn truy cập được nhưng chỉ xử lý PDF bị lỗi, kiểm tra riêng trạng thái tài liệu, kết quả quét và hàng đợi.

**Giữ bằng chứng và phương án khôi phục.** Ghi lại thời gian, revision, image digest và thay đổi gần nhất. Nếu nghi ngờ lỗi triển khai, kiểm tra khả năng tương thích schema trước khi thử quay về phiên bản API trước đó.

**Xác nhận khôi phục bằng dữ liệu mẫu.** Khi khôi phục RDS, chọn snapshot dự kiến và xác minh kết nối TLS, quyền cơ sở dữ liệu, bản ghi mẫu và media S3 được liên kết. So sánh thời điểm snapshot với lần cập nhật hợp lệ cuối cùng để nhận diện các thay đổi cần đối chiếu.

**Mở lại khi kiểm tra đạt yêu cầu.** Dùng readiness và các kiểm thử quy trình cốt lõi để quyết định khi nào chuyển CloudFront về `live`. Ghi lại nguyên nhân, cách phát hiện vấn đề và mọi bước runbook cần cập nhật.

## 8. Kết quả kỳ vọng và tiêu chí nghiệm thu

| Kịch bản nghiệm thu | Cách kiểm thử | Bằng chứng kỳ vọng |
| :--- | :--- | :--- |
| Thành viên gửi đơn ứng tuyển | Sử dụng một vị trí đang tuyển còn chỗ. Gửi đơn với tư cách thành viên và chấp nhận đơn với tư cách trưởng dự án. | Đơn chuyển từ Pending sang Accepted. Thành viên xuất hiện trong dự án, số chỗ đã tuyển tăng đúng một lần và dữ liệu vẫn nhất quán sau khi tải lại. |
| Trưởng dự án mời đồng đội | Sử dụng dữ liệu thử nghiệm riêng. Ứng viên đồng ý cho phép được tìm thấy, trưởng dự án gửi lời mời và ứng viên mở thông báo rồi chấp nhận. | Lời mời xác định đúng dự án và vị trí. Số chỗ không thay đổi khi lời mời đang Pending và cập nhật sau khi chấp nhận thành công. |
| Chat qua CloudFront | Mở hai phiên đăng nhập có quyền truy cập cùng cuộc trò chuyện và gửi tin nhắn từ một phiên. | Phiên còn lại nhận tin nhắn trong đúng cuộc trò chuyện. DevTools ghi nhận việc nâng cấp WebSocket với trạng thái 101. |
| Quét PDF | Tải chứng chỉ mẫu hoặc PDF đầu vào AI lên. Theo dõi trạng thái quét từ chờ xử lý đến khi có kết quả. | Những thao tác yêu cầu kết quả quét sạch tiếp tục bị chặn khi đang chờ quét. Kết quả sạch của đúng đối tượng hoặc phiên bản S3 khiến tài liệu đủ điều kiện dùng đúng mục đích. |
| Bản nháp hồ sơ AI | Gửi văn bản hoặc PDF đã quét và đủ điều kiện, xem lại các trường được gợi ý, chỉnh sửa rồi xác nhận lưu. | Gợi ý có cấu trúc và cảnh báo chỉ ra dữ liệu thiếu hoặc mâu thuẫn. Hồ sơ đã lưu phản ánh đúng các trường người dùng xác nhận. |
| Phát hành và khôi phục | Mở trang web qua CloudFront, kiểm tra API readiness và các quy trình cốt lõi, sau đó dựng lại môi trường thử nghiệm từ snapshot được chọn bằng runbook. | Web và API dùng chung hostname, readiness trả về `200` và `Healthy`, dữ liệu mẫu khớp với điểm khôi phục. Thông tin phát hành, snapshot ID và kết quả đối chiếu được ghi lại. |

### Bằng chứng bàn giao

Với mỗi lượt nghiệm thu, ghi lại môi trường và phiên bản, hành động đã thực hiện, kết quả kỳ vọng và kết quả quan sát được. Dùng tài khoản mẫu cho tuyển dụng và ghi nhận trạng thái trước, sau. Đối với bản phát hành, lưu commit, image digest, kết quả readiness và tham chiếu snapshot. Đối với kiểm tra PDF và AI, dùng tài liệu mẫu và giữ trạng thái quét cùng một ví dụ bản nháp đã được xem xét. Nhờ vậy, người đọc có thể xác minh kết quả mà không cần truy cập tài khoản hoặc dữ liệu cá nhân của bất kỳ ai.

**Cơ sở kỹ thuật.** Xem các template trong `tindy-infrastructure/cloudformation`, các runbook phát hành và tái triển khai Tindy, `DISCOVERY_PROJECT_FLOW.md`, `TEAMMATE_FLOW.md`, `AI_PROFILE_EXTRACTION.md` và tài liệu chat backend.
