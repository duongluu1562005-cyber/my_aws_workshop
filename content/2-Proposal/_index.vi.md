---
title: "Đề xuất dự án"
date: 2026-19-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Tindy — Nền tảng AWS để tìm kiếm dự án và đồng đội

## 1. Tóm tắt tổng quan

Tindy là một ứng dụng web giúp sinh viên và những người mới bắt đầu sự nghiệp tìm kiếm các dự án phù hợp với kỹ năng và thời gian có thể tham gia của họ. Người phụ trách dự án đăng tuyển các vị trí cụ thể, theo dõi đơn ứng tuyển và gửi lời mời. Khi một ứng viên được chấp nhận, Tindy cập nhật danh sách thành viên dự án và số lượng chỗ đã được tuyển của vị trí để cả hai bên đều nhìn thấy cùng một trạng thái.

Bản demo được đề xuất chạy tại **US East (N. Virginia), `us-east-1`**. CloudFront phân phối frontend React từ S3 và chuyển các yêu cầu động đến API ASP.NET Core trên ECS Fargate. API lưu trữ dữ liệu nghiệp vụ trong RDS for PostgreSQL. S3 bucket media riêng tư lưu trữ tài liệu, trong khi GuardDuty quét các tệp PDF và Amazon Bedrock tạo bản nháp các đề xuất cho hồ sơ.

## 2. Vấn đề và phạm vi

### Vấn đề

Thông tin tuyển thành viên thường phân tán trong các nhóm trò chuyện, bài đăng và biểu mẫu. Người ứng tuyển gặp khó khăn trong việc xác định những vị trí nào đang mở, yêu cầu những kỹ năng gì và cần bao nhiêu giờ mỗi tuần. Người phụ trách phải liên tục hỏi lại cùng một thông tin và theo dõi đơn ứng tuyển, lời mời và số chỗ còn trống trên nhiều kênh khác nhau. Tindy tập hợp những thông tin này cho từng dự án và vị trí.

### Phạm vi demo

| Quy trình | Hành động của người dùng | Kết quả trong Tindy |
| :--- | :--- | :--- |
| Xây dựng hồ sơ | Thành viên hoàn thành quy trình onboarding và nhập kỹ năng, vai trò mong muốn, kinh nghiệm và thời gian có thể tham gia. Văn bản hoặc CV PDF đủ điều kiện có thể được sử dụng để tạo bản nháp. | Hồ sơ được cập nhật sau khi chủ sở hữu xem xét và lưu. |
| Ứng tuyển vào một vị trí | Người phụ trách đăng một vị trí đang tuyển. Thành viên tìm thấy dự án, chọn vị trí và xác nhận ứng tuyển. Người phụ trách xem xét đơn đã gửi. | Đơn ứng tuyển đã gửi có trạng thái Pending. Sau khi được chấp nhận, thành viên xuất hiện trong dự án và số chỗ đã tuyển của vị trí tăng lên. |
| Mời một đồng đội | Người phụ trách chọn dự án và vị trí, tìm một ứng viên đã đồng ý nhận lời mời và gửi lời mời. Ứng viên mở thông báo và phản hồi. | Lời mời có trạng thái Pending cho đến khi được xử lý. Khi ứng viên chấp nhận và API xác minh điều kiện hợp lệ, ứng dụng tạo tư cách thành viên và cập nhật số chỗ đã tuyển. |
| Giao tiếp | Các thành viên trao đổi tin nhắn trực tiếp hoặc tham gia cuộc trò chuyện của dự án khi có quyền truy cập. | Người dùng được cấp quyền nhìn thấy tin nhắn mới trong đúng cuộc trò chuyện. |
| Vận hành demo | Người vận hành phát hành frontend và API, kiểm tra tình trạng dịch vụ, sao lưu dữ liệu và xây dựng lại môi trường nếu cần. | Thông tin phát hành, kết quả xác minh và các tham chiếu sao lưu hỗ trợ quá trình khôi phục. |

### Diễn giải điểm phù hợp

API tính điểm cho mỗi vị trí bằng bốn trọng số cơ bản: **kỹ năng bắt buộc 40%, vai trò mong muốn 30%, thời gian có thể tham gia 20% và kỹ năng tùy chọn 10%**. Nếu một vị trí không có kỹ năng trong một danh mục hoặc không thể tính toán thời gian có thể tham gia, API sẽ chuẩn hóa các trọng số còn lại có thể áp dụng. Nếu thiếu vai trò mong muốn, tiêu chí đó được tính 0 điểm.

Ví dụ, một ứng viên đáp ứng tất cả kỹ năng bắt buộc và tùy chọn cũng như vai trò mong muốn, đồng thời có thể đóng góp 5 trong số 10 giờ mỗi tuần mà dự án yêu cầu, sẽ đạt **90/100** điểm. Điểm số thể hiện mức độ phù hợp. Người dùng đưa ra quyết định tuyển thành viên và API kiểm tra lại quyền truy cập, trạng thái dự án và sức chứa khi xử lý việc chấp nhận.

### Lợi ích và cách đánh giá

Đối với **thành viên**, chức năng khám phá dự án cho biết vị trí đang tuyển, số giờ dự kiến mỗi tuần và các kỹ năng còn thiếu. Một hồ sơ có thể được sử dụng lại để ứng tuyển vào nhiều vị trí. Đối với **người phụ trách dự án**, đơn ứng tuyển và lời mời được tổ chức theo dự án và vị trí. Khi tuyển thành viên thành công, trạng thái đơn ứng tuyển, tư cách thành viên dự án và số chỗ đã tuyển có thể được kiểm tra dựa trên cùng các bản ghi dữ liệu nghiệp vụ.

Quá trình thử nghiệm sẽ đo lường giá trị của các quy trình này. Đối với tuyển thành viên, ghi lại các bước và thời gian đã trôi qua từ khi chọn một vị trí cho đến khi cả hai bên xác nhận kết quả. Đối với việc nhập hồ sơ, sử dụng cùng thông tin mẫu để so sánh việc nhập thủ công với quá trình tạo bản nháp, xem xét và chỉnh sửa các đề xuất của AI. Chi phí hạ tầng, thời gian thiết lập demo và công sức cần thiết để xem xét các đề xuất cũng được sử dụng trong đánh giá. Những phép đo này hỗ trợ việc đánh giá giá trị sau quá trình thử nghiệm.

## 3. Kiến trúc giải pháp

![Sơ đồ kiến trúc Tindy](./my_aws_workshop/images/2-Proposal/3.png)

*Sơ đồ kiến trúc Tindy cho bản đề xuất. Phần mô tả theo số thứ tự bên dưới phản ánh cấu hình repository và mã nguồn ứng dụng.*

| Bước | Thành phần | Luồng yêu cầu hoặc dữ liệu |
| :--- | :--- | :--- |
| 1 | Amazon CloudFront và S3 bucket frontend | Người dùng mở trang web qua HTTPS. CloudFront phân phối bản build React/Vite từ một S3 bucket riêng tư. Origin Access Control (OAC) giới hạn quyền đọc frontend cho distribution. |
| 2 | CloudFront VPC Origin và Application Load Balancer | Các đường dẫn động như `/api/*`, `/hubs/*` và các callback đăng nhập được chuyển đến ALB nội bộ. Ứng dụng web và API dùng chung một hostname, đồng thời caching bị vô hiệu hóa đối với các đường dẫn động này. CloudFront VPC origins |
| 3 | Amazon ECS, AWS Fargate và Amazon ECR | ECS chạy container API ASP.NET Core .NET 10 từ ECR. API xử lý xác thực, hồ sơ, khám phá dự án, tuyển thành viên và SignalR chat. Các health check của ALB theo dõi tình trạng task. |
| 4 | S3 bucket media, GuardDuty, EventBridge và SQS | Một bucket riêng tư lưu trữ media. Các tệp PDF chứng chỉ và PDF đầu vào AI đi qua quy trình quét. EventBridge chuyển kết quả quét của GuardDuty đến SQS để worker có thể cập nhật trạng thái tài liệu. Một dead-letter queue (DLQ) lưu giữ các thông điệp xử lý thất bại để điều tra. |
| 5 | Amazon Bedrock | Đối với các tệp PDF đầu vào AI, backend xác minh kết quả quét an toàn của S3 object tương ứng trước khi cho phép trích xuất. Người dùng cũng có thể gửi văn bản. Backend xác thực đầu ra và trả về các trường hồ sơ dạng bản nháp, kỹ năng, kinh nghiệm, chứng chỉ và cảnh báo để xem xét. |
| 6 | Amazon RDS for PostgreSQL | PostgreSQL 17 lưu trữ dữ liệu có cấu trúc như tài khoản, hồ sơ, dự án, vị trí, đơn ứng tuyển, lời mời và tin nhắn. RDS sử dụng private subnets, yêu cầu TLS và giới hạn quyền truy cập thông qua security groups. |

### Ba luồng hoạt động trong sơ đồ

**Truy cập ứng dụng và cập nhật dữ liệu (1 → 2 → 3 → 6).** Trình duyệt tải frontend từ S3 thông qua CloudFront. Khi người dùng mở dự án, ứng tuyển hoặc kết nối chat, CloudFront định tuyến yêu cầu động qua VPC Origin đến ALB nội bộ. ALB chọn một Fargate task có trạng thái hoạt động tốt. API xác minh JWT và quyền truy cập đối với dự án hoặc cuộc trò chuyện trước khi đọc hoặc ghi dữ liệu vào PostgreSQL. Web, API và chat dùng chung một hostname.

**Tải lên chứng chỉ và kiểm tra PDF (3 → 4).** API nhận media và ghi tệp PDF vào vùng lưu trữ S3 riêng tư. GuardDuty quét tệp, EventBridge chuyển kết quả quét đến SQS và một worker đối chiếu kết quả với S3 object hoặc phiên bản tương ứng rồi cập nhật trạng thái tài liệu. API chặn các thao tác đọc hoặc trích xuất yêu cầu kết quả quét an toàn khi tài liệu đang chờ xử lý hoặc không đủ điều kiện. DLQ lưu giữ các thông điệp xử lý thất bại sau số lần thử lại đã cấu hình.

**Trích xuất hồ sơ (4 → 5 → 3 → 6).** Một tệp PDF đầu vào AI có thể tiếp tục được trích xuất sau khi có kết quả quét an toàn. Người dùng cũng có thể gửi văn bản trực tiếp. Backend gọi Bedrock, xác thực phản hồi và trả về các trường hồ sơ dạng bản nháp, kỹ năng, kinh nghiệm và chứng chỉ. API ghi các trường được chọn vào PostgreSQL sau khi người dùng xem xét, chỉnh sửa và xác nhận lưu.

**Mạng và tính sẵn sàng.** VPC trải rộng trên hai Availability Zones. ALB là ALB nội bộ. Trong cấu hình hiện tại, các Fargate task sử dụng public subnets và nhận địa chỉ public IPv4. Security groups giới hạn lưu lượng truy cập vào API chỉ từ ALB. Runbook demo sử dụng một Fargate task và template database cấu hình RDS Single-AZ. Hai vùng trong sơ đồ thể hiện vị trí đặt các subnet. Việc mở rộng quy mô yêu cầu xác minh SignalR giữa các task và quá trình xử lý sự kiện nền.

**Bảo mật và vận hành.** Tài nguyên frontend và media sử dụng các S3 bucket riêng biệt. Backend xác thực người dùng bằng JWT và thực thi quyền truy cập ở cấp tài nguyên. Secrets Manager lưu trữ thông tin kết nối và cấu hình nhạy cảm. GitHub Actions đảm nhận các vai trò triển khai thông qua OIDC. CloudWatch thu thập log và giám sát tình trạng target, các phản hồi HTTP 5xx và DLQ. SNS gửi cảnh báo khi các thông báo được cấu hình.

### Các thành phần và cơ sở lựa chọn thiết kế

| Tầng | Trách nhiệm trong demo | Lý do thiết kế này phù hợp |
| :--- | :--- | :--- |
| Web | React và Vite tạo các tài nguyên tĩnh. CloudFront đọc dữ liệu từ S3 thông qua Origin Access Control và phục vụ các tuyến của ứng dụng. | Frontend có thể được phát hành độc lập. Tài nguyên tĩnh và dữ liệu phiên động sử dụng các cơ chế cache riêng biệt. |
| API | ASP.NET Core chạy trong ECS Fargate. ECR lưu trữ các image phát hành. ALB kiểm tra trạng thái sẵn sàng và định tuyến lưu lượng đến các task hoạt động tốt. | Bản phát hành đang hoạt động có thể được truy vết đến image digest và một revision mới có thể được kiểm tra trong quá trình rollout. |
| Dữ liệu nghiệp vụ | RDS for PostgreSQL lưu trữ hồ sơ, các mối quan hệ trong dự án, trạng thái tuyển thành viên và lịch sử chat. | Các mối quan hệ giữa dự án, vị trí và thành viên có thể được kiểm tra khi ứng dụng ghi dữ liệu. Migrations quản lý các thay đổi schema cùng với các bản phát hành ứng dụng. |
| Tệp và sự kiện | S3 bucket media riêng tư lưu trữ tệp. GuardDuty quét PDF, EventBridge chuyển các sự kiện quét và SQS consumer cập nhật trạng thái tài liệu. | Việc tải lên và hoàn tất quét diễn ra tại những thời điểm khác nhau. Hàng đợi thể hiện công việc đang chờ xử lý và DLQ cung cấp nơi kiểm tra các lỗi. |
| AI | Khi được bật, backend gọi Bedrock và trả về một bản nháp có cấu trúc. | Người dùng có thể so sánh các đề xuất với tài liệu nguồn. Backend xác thực các trường hồ sơ và ánh xạ kỹ năng, vai trò trước khi xem xét. |

### Ranh giới dữ liệu, quyền truy cập và khả năng giám sát

**Vị trí lưu trữ dữ liệu.** PostgreSQL lưu trữ thông tin định danh, hồ sơ, các mối quan hệ dự án và trạng thái quy trình nghiệp vụ. S3 bucket frontend chứa HTML và các gói tài nguyên tĩnh. S3 bucket media chứa nội dung tệp. Một recovery manifest cần có cả tham chiếu RDS snapshot và các tham chiếu S3 object liên quan để nhóm có thể xác minh rằng các bản ghi được khôi phục vẫn liên kết đúng với các tệp tương ứng.

**Quyền truy cập theo tầng.** Security groups kiểm soát quyền truy cập mạng giữa ALB, API tasks và database. Backend xác thực người dùng và cấp quyền truy cập đến dự án, hồ sơ hoặc cuộc trò chuyện tương ứng. ECS execution role hỗ trợ các thao tác khởi động như tải image, khởi tạo logging và truy xuất các secrets đã cấu hình. ECS task role bao gồm các hành động AWS mà mã ứng dụng cần thực hiện, chẳng hạn như truy cập media, xử lý kết quả quét và gọi AI khi được bật. Các quyền OIDC được GitHub Actions sử dụng để phát hành được quản lý độc lập với quyền runtime.

**Tín hiệu vận hành.** Tình trạng target và các phản hồi 5xx giúp thu hẹp phạm vi xác định sự cố nằm ở định tuyến hay ứng dụng. ECS events và CloudWatch Logs cho biết một task có thể khởi động, đã dừng hoặc trả về lỗi hay không. Đối với PDF, cần đối chiếu trạng thái tài liệu trong Tindy với kết quả GuardDuty và các thông điệp đang chờ trong SQS hoặc DLQ. Thông báo tuyển thành viên xuất hiện trong ứng dụng thông qua API polling. Chat sử dụng SignalR. SNS phục vụ cảnh báo vận hành khi được cấu hình.

### Các đánh đổi trong thiết kế demo và điều kiện để mở rộng

| Quyết định | Ưu tiên của demo | Bằng chứng cần có để thay đổi |
| :--- | :--- | :--- |
| Một Fargate task phía sau ALB | Giữ quy mô tài nguyên hoạt động liên tục ở mức nhỏ và đơn giản hóa chat thời gian thực cho demo. | Đo lưu lượng và độ trễ. Xác minh việc phân phối sự kiện SignalR giữa các task và kiểm tra tính nhất quán của xử lý sự kiện nền trước khi tăng số lượng task. |
| RDS Single-AZ | Giữ cấu hình ban đầu đơn giản và duy trì một quy trình khôi phục bằng snapshot đã được ghi lại. | Xác định thời gian ngừng hoạt động và mức mất dữ liệu có thể chấp nhận. Ước tính chi phí Multi-AZ và thực hành khôi phục trước khi quyết định sử dụng tính sẵn sàng cao hơn. |
| API tasks trong public subnets với lưu lượng đầu vào qua ALB nội bộ | Sử dụng đường kết nối ra ngoài hiện có cho các dịch vụ tích hợp. Security groups giới hạn quyền truy cập vào API. | Trước khi chuyển sang private subnets, lập danh sách các điểm đích AWS và bên ngoài, đồng thời so sánh các endpoint và đường kết nối ra ngoài về khả năng kết nối, chi phí và công sức vận hành. |
| Trích xuất AI theo yêu cầu | Xử lý dữ liệu đầu vào khi người dùng yêu cầu bản nháp và cho phép đo thời gian chạy cũng như chi phí cho mỗi lần thực hiện. | Đo các kết quả không hợp lệ, độ trễ và mức sử dụng token trước khi cân nhắc một hàng đợi xử lý nền. |

## 4. Triển khai và kiểm thử

### Chuẩn bị hạ tầng và dữ liệu

AWS CloudFormation định nghĩa sáu stack: **foundation, network, database, integrations, runtime và edge**. Foundation và network cung cấp các bucket, ECR, IAM và hạ tầng mạng. Database cung cấp PostgreSQL. Integrations xử lý kết quả quét PDF. Runtime triển khai API, ALB và VPC Origin. Edge sử dụng origin đó để định tuyến các yêu cầu động khi dịch vụ đã sẵn sàng.

Chọn **Fresh** để tạo một database, áp dụng migrations và thêm dữ liệu demo, hoặc **Restore** để khôi phục schema và dữ liệu từ snapshot. Đối với bản phát hành thay đổi schema, trước tiên cần tạo snapshot và chạy EF Core migration trong một ECS task riêng biệt. Xác minh rằng API đang chạy vẫn tương thích với schema đã được migration để API có thể quay lại revision trước nếu rollout thất bại.

### Phát hành ứng dụng

1. Sau khi CI hoàn tất thành công, chọn commit đã được phê duyệt. Build backend image, đẩy lên ECR và ghi lại image digest được sử dụng trong ECS task definition.
2. Xác minh ECS service ổn định và các ALB target ở trạng thái healthy. Khi edge ở chế độ `live`, kiểm tra rằng `/api/health/ready` trả về HTTP `200` với trạng thái `Healthy` thông qua CloudFront.
3. Build frontend bằng Vite, xuất bản lên S3 và thực hiện invalidation CloudFront cache. Kiểm tra trang chủ, trang đăng nhập và JavaScript bundle thông qua cùng một hostname.
4. Chạy các kịch bản nghiệm thu bằng tài khoản và PDF mẫu. Ghi lại commit, image digest, ECS revision, kết quả kiểm thử và snapshot ID khi áp dụng. Khi xây dựng lại môi trường demo, giữ edge ở chế độ maintenance cho đến khi API, database và các dịch vụ tích hợp cần thiết đã sẵn sàng.

### Yêu cầu kỹ thuật và phi chức năng

- **Môi trường phát hành.** Ứng dụng sử dụng React/Vite, ASP.NET Core .NET 10, EF Core và PostgreSQL. Build frontend để sử dụng các đường dẫn API cùng origin. Cung cấp các thiết lập môi trường và cấu hình nhạy cảm thông qua quy trình triển khai và AWS Secrets Manager.
- **Tính nhất quán.** Việc chấp nhận thành viên phải sử dụng trạng thái hiện tại của dự án và vị trí. Kiểm tra cả tư cách thành viên và số chỗ đã tuyển của vị trí sau khi chấp nhận và tải lại.
- **Quyền riêng tư.** Sử dụng tài khoản thử nghiệm và tài liệu mẫu cho các buổi demo. Xác minh quyền truy cập khi mở tài liệu, yêu cầu bản nháp AI hoặc truy cập cuộc trò chuyện. Giữ các tham chiếu và bằng chứng trạng thái trong hồ sơ kiểm thử. Không đưa giá trị secret và nội dung CV vào các hồ sơ đó.
- **Khả năng kiểm thử.** Chuẩn bị dữ liệu thử nghiệm cho các hành động thành công và các hành động bị từ chối. Bao gồm một vị trí không còn chỗ trống, một ứng viên chưa đồng ý nhận lời mời, một PDF đang chờ quét và một người dùng không có quyền truy cập cuộc trò chuyện.
- **Khả năng khôi phục.** Ghi lại snapshot và phiên bản liên quan trước khi thay đổi schema. Xác định một tập hợp các bản ghi ứng dụng và tệp media mẫu để đối chiếu sau khi Restore.

### Vòng đời giữa các lần demo

Sau một buổi demo, kiểm tra xem các thao tác ghi dữ liệu của ứng dụng hoặc công việc quét PDF có còn đang diễn ra hay không. Khi môi trường đã sẵn sàng để sao lưu, ghi lại snapshot ID, thời gian, trạng thái mã hóa, bản phát hành API và migration hiện tại. Xác nhận rằng các S3 object và ECR image cần thiết cho điểm khôi phục đó vẫn được giữ lại.

Nếu môi trường được tắt giữa các lần demo, chuyển CloudFront sang chế độ maintenance và xác minh rằng hostname của distribution vẫn được giữ lại. Runbook của dự án định nghĩa các bước kiểm tra trước khi loại bỏ runtime, database và integrations theo thứ tự phụ thuộc. Giữ lại các tài nguyên khôi phục đã thống nhất, bao gồm snapshot, dữ liệu S3, ECR image và các tham chiếu cấu hình. Trong lần demo tiếp theo, chọn Restore hoặc Fresh dựa trên mục tiêu, xác minh database endpoint và health checks, sau đó mở lại edge khi các dịch vụ cần thiết đã sẵn sàng.

## 5. Kế hoạch Sprint và các mốc triển khai

Quá trình phát triển Tindy bắt đầu vào **thứ Hai, ngày 5 tháng 10 năm 2026 (Tuần 4)**. Kế hoạch chia Tuần 4–9 thành ba sprint phát triển, mỗi sprint kéo dài hai tuần. **Tuần 10 (ngày 16–22 tháng 11 năm 2026)** được dành cho việc triển khai lên AWS, xác minh sau triển khai và hoàn thành demo.

| Sprint | Thời gian | Công việc chính | Sản phẩm bàn giao cuối sprint |
| :--- | :--- | :--- | :--- |
| **Sprint 1 · Nền tảng ứng dụng** | **Tuần 4–5** · 5–18 tháng 10 năm 2026 | Bắt đầu phát triển. Thống nhất các luồng người dùng và mô hình dữ liệu. Thiết lập React/Vite, ASP.NET Core và PostgreSQL. Xây dựng chức năng đăng nhập, onboarding, hồ sơ và danh mục kỹ năng, vai trò. | Một người dùng mẫu có thể đăng nhập, hoàn thành onboarding và lưu hồ sơ thông qua API. |
| **Sprint 2 · Dự án và tuyển thành viên** | **Tuần 6–7** · 19 tháng 10–1 tháng 11 năm 2026 | Xây dựng chức năng quản lý dự án và vị trí, khám phá dự án, tính điểm phù hợp, ứng tuyển, tìm kiếm đồng đội và gửi lời mời. Kiểm tra lại điều kiện tuyển thành viên và số chỗ còn trống khi chấp nhận. | Dữ liệu mẫu thể hiện các luồng ứng tuyển và lời mời. Việc chấp nhận thành công cập nhật chính xác tư cách thành viên dự án và số chỗ đã tuyển. |
| **Sprint 3 · Tích hợp và chuẩn bị phát hành** | **Tuần 8–9** · 2–15 tháng 11 năm 2026 | Hoàn thiện SignalR chat và thông báo trong ứng dụng. Tích hợp lưu trữ tệp, quét PDF và các bản nháp hồ sơ từ Bedrock. Chuẩn bị CloudFormation templates, cấu hình CI/CD và dữ liệu demo. | Một release candidate, các kịch bản kiểm thử cho những quy trình cốt lõi và cấu hình AWS đã được xem xét. |
| **Sprint 4 · Triển khai và hoàn thành** | **Tuần 10** · 16–22 tháng 11 năm 2026 | Triển khai frontend và API lên AWS. Kiểm tra CloudFront, ALB, ECS và RDS. Chạy các kịch bản nghiệm thu thông qua hostname demo, giải quyết những vấn đề ảnh hưởng đến các quy trình cốt lõi và ghi lại thông tin phát hành. | Một bản demo đã triển khai trên AWS với API readiness trả về HTTP `200` và trạng thái `Healthy`, các kết quả nghiệm thu và hướng dẫn triển khai lại. |

Mốc hoàn thành dự án nằm trong **Tuần 10**. Từ Tuần 11 đến khi kết thúc kỳ thực tập vào **ngày 14 tháng 12 năm 2026**, công việc báo cáo bao gồm sơ đồ kiến trúc, các kịch bản demo, kết quả nghiệm thu, thông tin phát hành và các giả định chi phí.

## 6. Ước tính ngân sách

Kịch bản hoạt động liên tục chạy **730 giờ mỗi tháng** tại `us-east-1`. Các số liệu được lấy từ bản ước tính workload `Tindy-Always-on` xuất từ AWS Pricing Calculator vào **ngày 18 tháng 11 năm 2026**, sử dụng mức giá trước khi giảm giá và không bao gồm thuế và credits.

| Hạng mục chi phí | Cơ sở tính toán | Chi phí tham khảo |
| :--- | :--- | :--- |
| AWS Fargate | Một Linux/x86 task với 0.5 vCPU và 2 GiB RAM. Bản ước tính bao gồm 365 vCPU-giờ và 1.460 GB-giờ bộ nhớ. | 21.26 USD/tháng |
| Application Load Balancer | Một ALB nội bộ hoạt động trong 730 giờ. | 16.43 USD/tháng |
| Public IPv4 | Một địa chỉ public IPv4 đang được sử dụng cho task trong 730 giờ. | 3.65 USD/tháng |
| RDS for PostgreSQL | Một Single-AZ `db.t4g.micro` trong 730 giờ (11.68 USD) và 20 GiB gp3 (2.30 USD). | 13.98 USD/tháng |
| Edge, media, bảo mật và AI | CloudFront, S3, ECR, Secrets Manager, GuardDuty, EventBridge, SQS, CloudWatch và Bedrock. Chi phí thay đổi theo dung lượng lưu trữ, số lượng yêu cầu, log, lượt quét và token. | Tính theo mức sử dụng |

**Chi phí cơ sở cho kịch bản hoạt động liên tục là 55.32 USD mỗi tháng.** Chi phí cơ sở không bao gồm ALB LCUs, các khoản phí RDS CPU credit, dung lượng sao lưu hoặc snapshot bổ sung và các dịch vụ tính phí theo mức sử dụng được liệt kê ở trên. Cần tính cả các snapshot và tài nguyên khác được giữ lại khi ước tính kịch bản demo theo lịch. AWS Pricing Calculator

Bản ước tính công khai hiển thị **55.34 USD mỗi tháng**. So với bản xuất từ AWS Console, chi phí Fargate và ALB được hiển thị đều cao hơn 0.01 USD. Phép tính ALB công khai sử dụng lưu lượng giả định 0.001 GB/giờ vì biểu mẫu yêu cầu một giá trị LCU dương.

**Với lưu lượng thử nghiệm, dự trù khoảng 58 USD mỗi tháng** cho hạ tầng hoạt động liên tục và các dịch vụ hỗ trợ đã được mô hình hóa. Bản xuất Cost Explorer từ ngày 1–17 tháng 11 năm 2026 cho thấy hoạt động hạ tầng cốt lõi tập trung vào ngày 11–17 tháng 11. Xem tuần đó là đại diện cho một tháng 30 ngày. Cộng thêm 1.60 USD cho bốn secrets được lưu trữ và quy đổi các mức sử dụng khác đã nhập vào AWS Pricing Calculator (0.1804 USD × 30/7, bao gồm các API call của Secrets Manager). Con số lập kế hoạch này không bao gồm ALB LCUs, các khoản phí EventBridge chưa được xác minh, RDS CPU credits, dung lượng sao lưu hoặc snapshot bổ sung và thuế.

### So sánh các kịch bản vận hành

| Yếu tố chi phí | Kịch bản hoạt động liên tục | Kịch bản demo theo lịch |
| :--- | :--- | :--- |
| Fargate và public IPv4 | Mô hình hóa thời gian chạy dựa trên tháng tham chiếu 730 giờ. | Tính số giờ task thực tế hoạt động, bao gồm thời gian thiết lập và xác minh trước mỗi buổi demo. |
| ALB và RDS | Tính số giờ ALB và database được cấp phát hoặc hoạt động, đồng thời cộng các khoản phí phụ thuộc vào cấu hình. | Ghi nhận riêng thời gian tồn tại của ALB, thời gian chạy database và dung lượng snapshot được giữ giữa các lần demo. |
| Lưu trữ và cấu hình được giữ lại | Đo dung lượng lưu trữ S3 và ECR, các bản sao lưu và các thành phần cấu hình được giữ lại. | Bao gồm các tài nguyên được giữ lại để khôi phục khi edge đang ở chế độ maintenance. |
| Lưu lượng, quét và AI | Sử dụng một mức cơ sở xác định cho các request, lượt tải xuống, lượt quét và token. | Đo hoạt động theo từng phiên và ghi lại việc xử lý lặp lại. Giữ nguyên các giả định mức sử dụng khi so sánh chi phí runtime. |

Sau khi thử nghiệm, so sánh chi phí của từng dịch vụ với khoảng thời gian các tài nguyên được duy trì. Phân loại các chênh lệch thành chi phí runtime, lưu trữ, lưu lượng hoặc xử lý tài liệu. AWS Budgets có thể cảnh báo tại ngưỡng đã thống nhất, trong khi Cost Explorer cung cấp bảng phân tích để điều tra nguồn gốc của chênh lệch.

## 7. Đánh giá rủi ro

| Rủi ro | Tác động | Biện pháp giảm thiểu |
| :--- | :--- | :--- |
| API hoặc database bị gián đoạn trong cấu hình demo | Người dùng tạm thời mất quyền truy cập vào các quy trình của ứng dụng. | Giám sát readiness, ECS logs và tình trạng ALB. Xác định quy trình khôi phục snapshot và thời gian ngừng hoạt động có thể chấp nhận. |
| PDF bị từ chối, quét thất bại hoặc xử lý chậm | Chứng chỉ hoặc CV chưa thể được sử dụng. | Hiển thị trạng thái quét, chặn việc đọc và trích xuất cho đến khi đáp ứng điều kiện, giám sát DLQ và xác minh S3 object tương ứng. |
| Các đề xuất AI không đầy đủ hoặc không chính xác | Thông tin không chính xác có thể được đưa vào hồ sơ nếu không được xem xét. | Xác thực cấu trúc đầu ra và ánh xạ danh mục kỹ năng, sau đó yêu cầu người dùng xem xét và cho phép chỉnh sửa trước khi lưu. |
| Migration hoặc bản phát hành không tương thích | Các phiên bản web, API và database schema có thể trở nên không tương thích. | Tạo snapshot trước khi thay đổi, phát hành theo thứ tự tương thích và ghi lại commit, digest và revision để phục vụ khôi phục. |
| Tài nguyên tiếp tục phát sinh chi phí sau demo | ALB, compute, database, logs hoặc snapshots có thể tiếp tục bị tính phí. | Theo dõi ngân sách, xác định các tài nguyên được giữ lại và xem xét chi phí sau khi cleanup. |

### Phân loại sự cố và khôi phục

1. **Xác định luồng bị ảnh hưởng.** Trước tiên kiểm tra API readiness, tình trạng ALB target và ECS. Nếu web và API vẫn truy cập được nhưng chỉ có quá trình xử lý PDF thất bại, hãy kiểm tra riêng trạng thái tài liệu, kết quả quét và hàng đợi.
2. **Lưu giữ bằng chứng và các phương án khôi phục.** Ghi lại thời gian, revision, image digest và thay đổi gần nhất. Đối với trường hợp nghi ngờ rollout thất bại, xác minh tính tương thích của schema trước khi cố gắng quay lại revision API trước đó.
3. **Xác nhận khôi phục bằng dữ liệu mẫu.** Khi khôi phục RDS, chọn snapshot dự kiến và xác minh kết nối TLS, quyền truy cập database, các bản ghi mẫu và media S3 được liên kết. So sánh thời điểm snapshot với lần cập nhật hợp lệ cuối cùng để xác định các thay đổi cần đối chiếu.
4. **Mở lại khi các bước kiểm tra đạt yêu cầu.** Sử dụng readiness và các bài kiểm thử quy trình cốt lõi để quyết định khi nào chuyển CloudFront trở lại chế độ `live`. Ghi lại nguyên nhân, cách phát hiện sự cố và bất kỳ bước runbook nào cần cập nhật.

## 8. Kết quả kỳ vọng và tiêu chí nghiệm thu

| Kịch bản nghiệm thu | Cách kiểm thử | Bằng chứng kỳ vọng |
| :--- | :--- | :--- |
| Thành viên gửi đơn ứng tuyển | Sử dụng một vị trí đang tuyển còn chỗ trống. Gửi đơn ứng tuyển với vai trò thành viên và chấp nhận đơn với vai trò người phụ trách dự án. | Đơn ứng tuyển chuyển từ **Pending** sang **Accepted**. Thành viên xuất hiện trong dự án, số chỗ đã tuyển của vị trí tăng một lần và dữ liệu vẫn nhất quán sau khi tải lại. |
| Người phụ trách dự án mời một đồng đội | Sử dụng dữ liệu thử nghiệm riêng biệt. Ứng viên đồng ý nhận lời mời, người phụ trách gửi lời mời, sau đó ứng viên mở thông báo và chấp nhận. | Lời mời xác định đúng dự án và vị trí. Số chỗ đã tuyển không thay đổi khi lời mời đang ở trạng thái Pending và được cập nhật sau khi chấp nhận thành công. |
| Chat thông qua CloudFront | Mở hai phiên đã đăng nhập có quyền truy cập vào cùng một cuộc trò chuyện và gửi tin nhắn từ một phiên. | Phiên còn lại nhận được tin nhắn trong đúng cuộc trò chuyện. DevTools ghi nhận quá trình WebSocket upgrade với trạng thái `101`. |
| Quét PDF | Tải lên một chứng chỉ mẫu hoặc PDF đầu vào AI. Theo dõi trạng thái quét từ đang chờ đến khi có kết quả. | Các thao tác yêu cầu kết quả quét an toàn tiếp tục bị chặn khi quá trình quét đang chờ. Kết quả quét an toàn cho đúng S3 object hoặc phiên bản tương ứng khiến tài liệu đủ điều kiện để sử dụng theo mục đích dự kiến. |
| Bản nháp hồ sơ AI | Gửi văn bản hoặc PDF đã được quét và đủ điều kiện, xem xét các trường được đề xuất, chỉnh sửa và xác nhận lưu. | Các đề xuất có cấu trúc và cảnh báo xác định dữ liệu bị thiếu hoặc mâu thuẫn. Hồ sơ đã lưu phản ánh các trường mà người dùng xác nhận. |
| Phát hành và khôi phục | Mở trang web thông qua CloudFront, kiểm tra API readiness và các quy trình cốt lõi, sau đó xây dựng lại môi trường thử nghiệm từ snapshot đã chọn bằng runbook. | Web và API dùng chung một hostname, readiness trả về `200` và `Healthy`, đồng thời dữ liệu mẫu khớp với điểm khôi phục. Thông tin phát hành, snapshot ID và kết quả đối chiếu được ghi lại. |

### Bằng chứng bàn giao

Đối với mỗi lần chạy nghiệm thu, ghi lại môi trường và phiên bản, hành động đã thực hiện, kết quả mong đợi và những gì quan sát được. Sử dụng tài khoản mẫu cho việc tuyển thành viên và ghi lại trạng thái trước và sau. Đối với các bản phát hành, lưu lại commit, image digest, kết quả readiness và tham chiếu snapshot. Đối với các bước kiểm tra PDF và AI, sử dụng tài liệu mẫu và lưu lại trạng thái quét cùng một ví dụ về bản nháp đã được xem xét. Điều này cho phép người đọc xác minh kết quả mà không cần truy cập vào tài khoản hoặc dữ liệu cá nhân của bất kỳ ai.

**Cơ sở kỹ thuật.** Xem các template trong `tindy-infrastructure/cloudformation`, các runbook phát hành và triển khai lại Tindy, `DISCOVERY_PROJECT_FLOW.md`, `TEAMMATE_FLOW.md`, `AI_PROFILE_EXTRACTION.md` và tài liệu backend chat.
