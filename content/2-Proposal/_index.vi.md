---
title: "Bản đề xuất"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---
# Tindy — Nền tảng tìm kiếm dự án và đồng đội trên AWS

## 1. Tóm tắt dự án

Tindy là nền tảng web được thiết kế để kết nối sinh viên và những người mới bắt đầu sự nghiệp với các dự án phù hợp với kỹ năng và thời gian họ có thể tham gia. Trưởng nhóm dự án có thể đăng tuyển cho những vị trí cụ thể, theo dõi đơn ứng tuyển và mời các ứng viên tiềm năng tham gia. Khi ứng viên được chấp nhận, Tindy ghi nhận tư cách thành viên mới trong dự án và cập nhật số lượng chỗ đã tuyển của vị trí tương ứng, bảo đảm thông tin nhất quán cho cả hai bên.

Môi trường trình diễn (demo) dự kiến được triển khai tại **US East (N. Virginia), `us-east-1`**. Giao diện React được Amazon CloudFront phân phối từ nội dung lưu trên Amazon S3, còn các yêu cầu cần xử lý phía máy chủ được chuyển đến API ASP.NET Core triển khai bằng Amazon ECS trên AWS Fargate. Amazon RDS for PostgreSQL lưu trữ dữ liệu nghiệp vụ của ứng dụng. Một bucket S3 riêng tư lưu trữ tài liệu; GuardDuty được dùng để quét các tệp PDF và Amazon Bedrock hỗ trợ tạo nội dung hồ sơ gợi ý.

## 2. Vấn đề và phạm vi

### Vấn đề đặt ra

Thông tin về các dự án đang tuyển thành viên thường nằm rải rác trong nhóm chat, bài đăng mạng xã hội và biểu mẫu trực tuyến. Điều này khiến ứng viên khó xác định vị trí nào còn tuyển, từng vị trí yêu cầu kỹ năng gì và cần dành bao nhiêu thời gian mỗi tuần. Đồng thời, trưởng nhóm thường phải hỏi lặp lại cùng một thông tin và quản lý đơn ứng tuyển, lời mời cùng số chỗ trống trên nhiều kênh. Tindy tập trung các thông tin này theo từng dự án và vị trí.

### Phạm vi demo

| **Quy trình** | **Hành động của người dùng** | **Kết quả trên Tindy** |
| :-- | :-- | :-- |
| Tạo hồ sơ | Trong quá trình thiết lập tài khoản ban đầu, thành viên nhập kỹ năng, vai trò mong muốn, kinh nghiệm và số giờ có thể tham gia. Khi phù hợp, văn bản hoặc CV định dạng PDF được cung cấp có thể dùng để tạo các gợi ý. | Hồ sơ chỉ được cập nhật sau khi thành viên xem lại bản nháp và lưu thông tin. |
| Ứng tuyển vào vị trí | Trưởng nhóm đăng một vị trí đang tuyển. Thành viên xem dự án, chọn vị trí, xác nhận gửi đơn và chờ trưởng nhóm xét duyệt. | Đơn ban đầu có trạng thái **Pending (Đang chờ)**. Khi được chấp nhận, ứng viên trở thành thành viên dự án và số chỗ đã tuyển của vị trí tăng lên. |
| Mời đồng đội | Trưởng nhóm tìm ứng viên đã đồng ý cho phép người khác tìm thấy hồ sơ của mình, rồi gửi lời mời gắn với dự án và vai trò. Người nhận mở thông báo và phản hồi. | Lời mời giữ trạng thái **Pending (Đang chờ)** cho đến khi có quyết định. Nếu người nhận chấp nhận và API xác nhận đủ điều kiện, hệ thống tạo tư cách thành viên và cập nhật số chỗ đã tuyển. |
| Trao đổi | Người dùng gửi tin nhắn riêng hoặc tham gia các cuộc trò chuyện của dự án mà họ có quyền truy cập. | Tin nhắn mới chỉ hiển thị trong đúng cuộc trò chuyện và cho những người tham gia được cấp quyền. |
| Vận hành demo | Người vận hành phát hành phiên bản mới của frontend và API, kiểm tra dịch vụ, tạo bản sao lưu và khôi phục môi trường khi cần. | Thông tin phát hành, bằng chứng kiểm thử và mã nhận dạng bản sao lưu làm cơ sở kiểm chứng và khôi phục hệ thống. |

### Cách hiểu điểm phù hợp (Match Score)

Với mỗi vị trí đang tuyển, API tính điểm phù hợp dựa trên bốn tiêu chí: **kỹ năng bắt buộc (40%), vai trò mong muốn (30%), thời gian có thể tham gia (20%) và kỹ năng bổ sung (10%)**. Khi vị trí không quy định một nhóm kỹ năng cụ thể hoặc không thể đánh giá thời gian tham gia, trọng số của các tiêu chí còn áp dụng sẽ được phân bổ lại theo tỷ lệ. Nếu ứng viên chưa nhập vai trò mong muốn, tiêu chí này nhận 0 điểm.

Ví dụ, một ứng viên đáp ứng toàn bộ kỹ năng bắt buộc và bổ sung, phù hợp với vai trò mong muốn nhưng chỉ có thể tham gia 5 trong số 10 giờ yêu cầu mỗi tuần sẽ đạt **90/100**. Điểm số có vai trò giải thích mức độ phù hợp, không tự động quyết định tuyển dụng. Người dùng vẫn là bên đưa ra quyết định; khi việc chấp nhận được gửi lên, API kiểm tra lại quyền truy cập, trạng thái dự án và số chỗ còn lại.

### Lợi ích và cách đánh giá

Đối với **thành viên**, Tindy hiển thị rõ các vị trí đang tuyển, thời gian cần cam kết mỗi tuần và những kỹ năng còn thiếu trong quá trình tìm dự án. Một hồ sơ hoàn chỉnh cũng có thể dùng để ứng tuyển nhiều vị trí. Đối với **trưởng nhóm dự án**, hoạt động tuyển thành viên được tổ chức theo dự án và vị trí, giúp theo dõi đơn ứng tuyển lẫn lời mời dễ dàng hơn. Sau khi ứng viên tham gia, trạng thái đơn, bản ghi thành viên và số chỗ đã tuyển đều có thể đối chiếu với cùng dữ liệu nền tảng.

Buổi demo sẽ đánh giá những lợi ích này thông qua các thao tác có thể quan sát và đo lường. Các bài kiểm thử tuyển dụng ghi lại số bước và thời gian từ lúc chọn vị trí đến khi cả hai bên xác nhận kết quả cuối cùng. Bài kiểm thử hoàn thiện hồ sơ sử dụng cùng bộ dữ liệu mẫu để so sánh việc nhập thủ công với quy trình có AI hỗ trợ, bao gồm tạo bản nháp, xem xét và chỉnh sửa gợi ý. Việc đánh giá cũng tính đến chi phí AWS, thời gian chuẩn bị môi trường demo và công sức người dùng bỏ ra để kiểm tra thông tin hồ sơ được đề xuất. Các phép đo này làm cơ sở đánh giá mức độ hữu ích của nền tảng sau thử nghiệm.

## 3. Kiến trúc giải pháp

![Sơ đồ kiến trúc Tindy](/my_aws_workshop/images/2-Proposal/3.png)
*Sơ đồ kiến trúc Tindy dùng trong proposal. Phần giải thích theo số thứ tự dưới đây tương ứng với cấu hình kho mã nguồn và cách ứng dụng hiện được triển khai.*

| **Bước** | **Thành phần** | **Luồng yêu cầu hoặc dữ liệu** |
| :-- | :-- | :-- |
| 1 | Amazon CloudFront và bucket S3 của frontend | Người dùng truy cập ứng dụng qua HTTPS. Bucket S3 riêng tư cung cấp giao diện React/Vite thông qua CloudFront. Origin Access Control (OAC) chỉ cho phép CloudFront distribution, thay vì công chúng, đọc các đối tượng frontend. |
| 2 | CloudFront VPC Origin và Application Load Balancer | Các yêu cầu đến đường dẫn động, bao gồm `/api/*`, `/hubs/*` và các đường dẫn gọi lại sau đăng nhập, được chuyển đến ALB nội bộ. Frontend và API cùng dùng một hostname; những yêu cầu động này không được lưu cache. [CloudFront VPC origins](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html) |
| 3 | Amazon ECS, AWS Fargate và Amazon ECR | Image API ASP.NET Core .NET 10 lưu tại ECR được triển khai dưới dạng container ECS. Backend xử lý đăng nhập, hồ sơ, tìm kiếm dự án, quy trình tuyển dụng và nhắn tin qua SignalR. Tình trạng sẵn sàng của tác vụ được giám sát bằng health check của ALB. |
| 4 | Bucket S3 lưu media, GuardDuty, EventBridge và SQS | Tệp media tải lên được lưu trong bucket S3 không công khai. Chứng chỉ PDF và tài liệu đầu vào cho AI đi qua quy trình quét mã độc. GuardDuty tạo kết quả quét, EventBridge chuyển kết quả tới SQS để worker xử lý và cập nhật trạng thái tệp. Các thông điệp không xử lý được được giữ trong hàng đợi thư lỗi (dead-letter queue, DLQ) để điều tra. |
| 5 | Amazon Bedrock | Trước khi trích xuất thông tin từ PDF làm đầu vào AI, máy chủ kiểm tra rằng đối tượng S3 tương ứng đã có kết quả quét sạch. Hệ thống cũng hỗ trợ nhập văn bản trực tiếp. Sau khi xác thực phản hồi, backend đưa ra các giá trị hồ sơ, kỹ năng, kinh nghiệm, chứng chỉ và cảnh báo được đề xuất để người dùng xem xét. |
| 6 | Amazon RDS for PostgreSQL | PostgreSQL 17 lưu dữ liệu ứng dụng có cấu trúc như tài khoản, hồ sơ, dự án, vị trí, đơn ứng tuyển, lời mời và tin nhắn. RDS được đặt trong các private subnet, bắt buộc sử dụng TLS và giới hạn kết nối bằng security group. |

### Ba luồng xử lý qua sơ đồ

**Truy cập ứng dụng và cập nhật dữ liệu (1 → 2 → 3 → 6).** Trình duyệt tải các tài nguyên tĩnh từ S3 thông qua CloudFront. Khi mở dự án, gửi đơn ứng tuyển hoặc bắt đầu chat, trình duyệt gửi yêu cầu động qua VPC origin của CloudFront đến ALB nội bộ. Load balancer chuyển yêu cầu tới một Fargate task đang hoạt động tốt. Trước khi đọc hoặc ghi PostgreSQL, API kiểm tra JWT và quyền của người gọi đối với dự án hoặc cuộc trò chuyện tương ứng. Trang frontend, điểm cuối API và lưu lượng chat dùng chung một hostname.

**Tải chứng chỉ và kiểm tra PDF (3 → 4).** PDF được tải qua API và lưu vào bucket media riêng tư. GuardDuty quét tệp; EventBridge chuyển kết quả đến SQS; worker liên kết kết quả với đúng đối tượng hoặc phiên bản S3 rồi cập nhật trạng thái tài liệu. Các thao tác cần kết quả quét sạch, bao gồm một số thao tác đọc và trích xuất, vẫn bị chặn khi tệp đang chờ quét hoặc không đủ điều kiện. Nếu mọi lần thử lại theo cấu hình đều thất bại, thông điệp được đưa vào DLQ.

**Trích xuất thông tin hồ sơ (4 → 5 → 3 → 6).** Việc trích xuất từ tệp PDF đầu vào AI chỉ được cho phép sau khi có kết quả quét sạch. Người dùng cũng có thể nhập văn bản trực tiếp mà không cần tải PDF. Máy chủ yêu cầu Bedrock xử lý, kiểm tra cấu trúc phản hồi và tạo các thông tin hồ sơ được gợi ý như kỹ năng, kinh nghiệm và chứng chỉ. API chỉ lưu vào PostgreSQL những giá trị người dùng đã xem xét, có thể chỉnh sửa và xác nhận.

**Mạng và tính sẵn sàng.** Các tài nguyên mạng được phân bố trên hai Availability Zone. Load balancer không trực tiếp mở ra Internet. Trong cấu hình hiện tại, các Fargate task nằm trong public subnet và được cấp địa chỉ IPv4 công khai. Quy tắc security group chỉ cho phép lưu lượng truy cập API đi qua ALB. Hướng dẫn demo quy định **một Fargate task** và template cơ sở dữ liệu triển khai **RDS Single-AZ**. Hai vùng trong sơ đồ thể hiện vị trí các subnet, không có nghĩa là cơ sở dữ liệu chạy Multi-AZ. Trước khi tăng số task, cần kiểm chứng việc truyền sự kiện SignalR giữa các task và xử lý sự kiện nền.

**Bảo mật và vận hành.** Nội dung web tĩnh và media của người dùng được lưu trong hai bucket S3 riêng. Backend thực hiện xác thực JWT và kiểm tra quyền đối với từng tài nguyên. Thông tin kết nối cơ sở dữ liệu và cấu hình nhạy cảm được giữ trong Secrets Manager. Quy trình triển khai dùng GitHub Actions với cơ chế nhận quyền OIDC. CloudWatch cung cấp log ứng dụng và các tín hiệu về sức khỏe target, lỗi HTTP 5xx và hoạt động DLQ. Khi bật cảnh báo, SNS gửi thông báo vận hành.

### Các thành phần và lý do lựa chọn thiết kế

| **Lớp hệ thống** | **Trách nhiệm trong demo** | **Lý do thiết kế phù hợp** |
| :-- | :-- | :-- |
| Web | React và Vite tạo các tài nguyên tĩnh. CloudFront đọc từ S3 qua Origin Access Control và phân phối các đường dẫn ứng dụng. | Frontend có thể phát hành độc lập mà không cần xây dựng lại backend. Nội dung tĩnh và các yêu cầu phiên động áp dụng quy tắc cache khác nhau. |
| API | ASP.NET Core chạy trên ECS Fargate. ECR lưu image của các bản phát hành. ALB kiểm tra trạng thái sẵn sàng và định tuyến đến task khỏe mạnh. | Image digest giúp xác định chính xác bản đang chạy; quy trình rollout cũng cho phép kiểm tra phiên bản thay thế. |
| Dữ liệu nghiệp vụ | RDS for PostgreSQL lưu hồ sơ, quan hệ dự án, trạng thái tuyển dụng và lịch sử chat. | Các thao tác ghi có thể thực thi ràng buộc quan hệ giữa dự án, vị trí tuyển và thành viên. Database migration đồng bộ thay đổi schema với các bản triển khai phần mềm. |
| Tệp và sự kiện | Bucket media riêng tư lưu tệp. GuardDuty quét PDF, EventBridge phân phối sự kiện quét và consumer SQS cập nhật trạng thái tài liệu. | Việc tải tệp và hoàn tất quét diễn ra bất đồng bộ. SQS giúp nhìn thấy công việc đang chờ, còn DLQ giữ thông điệp lỗi để xem xét. |
| AI | Khi được bật, backend gọi Bedrock và trả về bản nháp có cấu trúc. | Nội dung gợi ý có thể đối chiếu với văn bản hoặc tài liệu gốc. Máy chủ kiểm tra các trường đề xuất và ánh xạ sang kỹ năng, vai trò hợp lệ trước khi người dùng duyệt. |

### Ranh giới dữ liệu, quyền truy cập và khả năng quan sát

**Vị trí lưu trữ dữ liệu.** Dữ liệu định danh, hồ sơ người dùng, quan hệ dự án và trạng thái quy trình được ghi vào PostgreSQL. Tệp HTML và tài nguyên frontend đã biên dịch nằm trong bucket website. Nội dung tài liệu tải lên nằm trong bucket media. Để kiểm tra việc khôi phục, manifest phải xác định cả snapshot RDS lẫn các đối tượng S3 liên quan, nhờ đó nhóm có thể xác nhận bản ghi cơ sở dữ liệu được phục hồi vẫn tham chiếu đến tài liệu còn tồn tại.

**Quyền truy cập theo từng lớp.** Kết nối giữa ALB, các container backend và cơ sở dữ liệu được kiểm soát bằng security group. Với mỗi yêu cầu, backend xác minh danh tính và quyền đối với dự án, hồ sơ hoặc cuộc chat được truy cập. ECS execution role được dùng khi khởi động task để tải container image, khởi tạo log và lấy các secret đã cấu hình. Các yêu cầu AWS từ ứng dụng đang chạy—như thao tác media, xử lý sự kiện quét và suy luận AI tùy chọn—được cấp qua ECS task role. Quyền triển khai của GitHub Actions thông qua OIDC tách biệt với quyền khi chạy ứng dụng.

**Tín hiệu vận hành.** Trạng thái target của ALB và tỷ lệ lỗi HTTP 5xx có thể giúp xác định sự cố nằm ở khâu định tuyến hay xử lý ứng dụng. ECS events và CloudWatch Logs cung cấp bằng chứng về quá trình khởi động, dừng task và lỗi ứng dụng. Khi điều tra PDF, cần đối chiếu trạng thái tài liệu trên Tindy với kết quả GuardDuty và các thông điệp đang chờ trong SQS hoặc DLQ. Thông báo tuyển dụng trong ứng dụng được lấy bằng cách thăm dò API định kỳ (polling). SignalR hỗ trợ nhắn tin thời gian thực. SNS dùng để gửi cảnh báo vận hành khi được cấu hình.

### Các đánh đổi trong demo và điều kiện cần để mở rộng

| **Quyết định** | **Ưu tiên của demo** | **Bằng chứng cần có trước khi thay đổi** |
| :-- | :-- | :-- |
| Một Fargate task phía sau ALB | Giới hạn tài nguyên phải chạy liên tục và giảm độ phức tạp của chat trong demo. | Thu thập số liệu lưu lượng yêu cầu và thời gian phản hồi. Kiểm chứng việc truyền sự kiện SignalR giữa các task và tính nhất quán của xử lý sự kiện nền trước khi tăng số lượng task. |
| RDS Single-AZ | Giữ cấu hình ban đầu đơn giản, đồng thời có quy trình khôi phục snapshot được ghi chép. | Xác định giới hạn thời gian ngừng hoạt động và mức mất dữ liệu có thể chấp nhận. Ước tính chi phí Multi-AZ và diễn tập khôi phục trước khi chọn mức sẵn sàng cao hơn. |
| API task trong public subnet, nhận lưu lượng qua ALB nội bộ | Giữ đường kết nối ra ngoài hiện có cho các tích hợp. Security group hạn chế quyền truy cập API. | Trước khi chuyển vào private subnet, kiểm kê các đích AWS và bên ngoài, so sánh endpoint và đường truy cập ra ngoài về kết nối, chi phí, công sức vận hành. |
| Trích xuất AI theo yêu cầu | Chỉ thực hiện trích xuất khi có yêu cầu và ghi nhận độ trễ cùng chi phí trên mỗi lần xử lý. | Đo tỷ lệ đầu ra không hợp lệ, độ trễ và lượng token trước khi cân nhắc hàng đợi xử lý nền. |

## 4. Triển khai và kiểm thử

### Chuẩn bị hạ tầng và dữ liệu

Hạ tầng được tổ chức bằng AWS CloudFormation thành sáu stack: **foundation, network, database, integrations, runtime và edge**. Các stack foundation và network cung cấp bucket S3, ECR, tài nguyên IAM và thành phần mạng. Stack database thiết lập PostgreSQL. Stack integrations quản lý kết quả quét PDF. API, load balancer và VPC origin thuộc stack runtime. Sau khi xác nhận runtime sẵn sàng, stack edge chuyển lưu lượng động qua VPC origin.

Tùy chọn **Fresh** tạo cơ sở dữ liệu mới, áp dụng migration và thêm dữ liệu mẫu; **Restore** khôi phục schema và bản ghi từ snapshot. Khi một bản phát hành thay đổi schema, cần tạo snapshot trước và chạy EF Core migration bằng một ECS task độc lập. Xác nhận API hiện hành tương thích với schema đã cập nhật để vẫn có thể quay lại phiên bản ứng dụng trước nếu rollout thất bại.

### Phát hành ứng dụng

1. Sau khi quy trình CI thành công, chọn commit được phê duyệt để phát hành. Xây dựng backend image, đẩy lên ECR và ghi lại image digest được dùng trong ECS task definition.
2. Kiểm tra dịch vụ ECS ổn định và các target của ALB đều khỏe mạnh. Khi edge ở chế độ `live`, xác nhận `/api/health/ready` trả về HTTP `200` với trạng thái `Healthy` qua CloudFront.
3. Biên dịch frontend bằng Vite, tải lên S3 và thực hiện vô hiệu hóa cache (invalidation) của CloudFront. Kiểm tra trang chủ, trang đăng nhập và JavaScript bundle qua cùng hostname.
4. Chạy kiểm thử nghiệm thu bằng tài khoản demo và PDF mẫu. Ghi nhận commit, image digest, phiên bản ECS, kết quả kiểm thử và snapshot ID nếu có. Khi xây dựng lại môi trường, giữ edge ở chế độ bảo trì cho tới khi backend, PostgreSQL và các tích hợp thiết yếu vượt qua kiểm tra sẵn sàng.

### Yêu cầu kỹ thuật và phi chức năng

- **Môi trường phát hành.** Ứng dụng sử dụng React/Vite, ASP.NET Core .NET 10, EF Core và PostgreSQL. Frontend được biên dịch để dùng đường dẫn API cùng origin. Các thông số môi trường và cấu hình nhạy cảm được cung cấp qua quy trình triển khai và AWS Secrets Manager.
- **Tính nhất quán.** Việc thêm thành viên phải dựa trên trạng thái dự án và số chỗ còn trống mới nhất. Sau khi chấp nhận và tải lại, cần kiểm tra cả tư cách thành viên lẫn số chỗ đã tuyển của vị trí.
- **Quyền riêng tư.** Demo nên sử dụng tài khoản giả lập và tệp mẫu không nhạy cảm. Kiểm tra quyền khi mở tài liệu, yêu cầu bản nháp AI hoặc vào cuộc trò chuyện. Lưu các mã tham chiếu và bằng chứng trạng thái trong hồ sơ kiểm thử, không lưu secret hay nội dung CV vào những bản ghi này.
- **Khả năng kiểm thử.** Cần có các trường hợp dự kiến thành công và các trường hợp phải bị từ chối. Bao gồm vị trí đã hết chỗ, ứng viên chưa đồng ý cho người khác tìm thấy, PDF còn chờ quét và người dùng không có quyền truy cập cuộc trò chuyện.
- **Khả năng khôi phục.** Ghi nhận snapshot cơ sở dữ liệu và phiên bản phần mềm trước khi thay đổi schema. Xác định một tập bản ghi ứng dụng và tệp media mẫu để đối chiếu sau khi Restore.

### Vòng đời giữa các buổi demo

Sau mỗi buổi demo, xác nhận xem còn thao tác ghi dữ liệu hoặc tác vụ quét PDF nào đang chạy hay không. Khi môi trường đã sẵn sàng sao lưu, ghi lại snapshot ID, thời gian, trạng thái mã hóa, bản phát hành API và migration hiện tại. Xác nhận các đối tượng S3 và ECR image cần cho điểm khôi phục đó vẫn được lưu giữ.

Khi tắt môi trường demo, bật chế độ bảo trì CloudFront nhưng giữ nguyên hostname của distribution. Runbook của dự án quy định những kiểm tra cần làm trước khi gỡ các thành phần runtime, database và integrations theo thứ tự phụ thuộc. Giữ lại tài nguyên khôi phục đã thống nhất, bao gồm snapshot, dữ liệu S3, ECR image và tham chiếu cấu hình. Ở buổi demo tiếp theo, chọn Restore hoặc Fresh tùy mục tiêu, kiểm tra database endpoint và health check, rồi mở lại edge khi các dịch vụ cần thiết đã sẵn sàng.

## 5. Kế hoạch sprint và các mốc triển khai

Việc phát triển Tindy bắt đầu vào **Thứ Hai, ngày 5/10/2026 (Tuần 4)**. Tuần 4–9 được chia thành ba sprint phát triển, mỗi sprint kéo dài hai tuần. **Tuần 10 (16–22/11/2026)** dành cho triển khai AWS, xác minh sau triển khai và hoàn thành demo.

| **Sprint** | **Thời gian** | **Công việc chính** | **Sản phẩm bàn giao cuối sprint** |
| :-- | :-- | :-- | :-- |
| **Sprint 1 · Nền tảng ứng dụng** | **Tuần 4–5** · 5–18/10/2026 | Bắt đầu phát triển. Thống nhất các luồng người dùng và mô hình dữ liệu. Thiết lập React/Vite, ASP.NET Core và PostgreSQL. Xây dựng đăng nhập, onboarding, hồ sơ và danh mục kỹ năng, vai trò. | Người dùng mẫu có thể đăng nhập, hoàn tất onboarding và lưu hồ sơ qua API. |
| **Sprint 2 · Dự án và tuyển thành viên** | **Tuần 6–7** · 19/10–1/11/2026 | Triển khai quản lý dự án và vị trí, tìm kiếm dự án, chấm điểm phù hợp, ứng tuyển, tìm đồng đội và gửi lời mời. Kiểm tra lại điều kiện tuyển dụng và sức chứa khi chấp nhận. | Dữ liệu mẫu thể hiện được luồng ứng tuyển và mời tham gia. Khi chấp nhận thành công, tư cách thành viên và số chỗ đã tuyển được cập nhật chính xác. |
| **Sprint 3 · Tích hợp và chuẩn bị phát hành** | **Tuần 8–9** · 2–15/11/2026 | Hoàn thiện chat SignalR và thông báo trong ứng dụng. Tích hợp lưu tệp, quét PDF và tạo bản nháp hồ sơ bằng Bedrock. Chuẩn bị CloudFormation template, cấu hình CI/CD và dữ liệu demo. | Bản ứng dụng chuẩn bị phát hành, các kịch bản kiểm thử quy trình chính và cấu hình AWS đã được xem xét. |
| **Sprint 4 · Triển khai và hoàn thiện** | **Tuần 10** · 16–22/11/2026 | Đưa frontend và API lên AWS. Kiểm tra CloudFront, ALB, ECS và RDS. Thực hiện kiểm thử nghiệm thu qua hostname demo, xử lý lỗi ảnh hưởng luồng chính và ghi nhận thông tin phát hành. | Demo AWS đã triển khai, điểm kiểm tra sẵn sàng API trả về HTTP `200` và `Healthy`, có kết quả nghiệm thu và hướng dẫn triển khai lại. |

Mốc hoàn thành dự án nằm ở **Tuần 10**. Từ Tuần 11 cho tới khi kết thúc kỳ thực tập vào **14/12/2026**, công việc báo cáo bao gồm sơ đồ kiến trúc, các kịch bản demo, kết quả nghiệm thu, thông tin phát hành và giả định chi phí.

## 6. Ước tính ngân sách

Mô hình chi phí hoạt động liên tục giả định **730 giờ mỗi tháng** tại `us-east-1`. Đơn giá lấy từ bản ước tính workload `Tindy-Always-on` xuất từ AWS Pricing Calculator vào **18/11/2026**, theo giá chưa chiết khấu, chưa bao gồm thuế hoặc tín dụng AWS.

| **Hạng mục chi phí** | **Cơ sở tính toán** | **Chi phí tham chiếu** |
| :-- | :-- | --: |
| AWS Fargate | Một container task Linux/x86 với 0,5 vCPU và 2 GiB RAM. Phép tính gồm 365 vCPU-giờ và 1.460 GB-giờ bộ nhớ. | 21,26 USD/tháng |
| Application Load Balancer | Một ALB nội bộ hoạt động 730 giờ. | 16,43 USD/tháng |
| Public IPv4 | Một địa chỉ IPv4 công khai được cấp cho task và sử dụng trong 730 giờ. | 3,65 USD/tháng |
| RDS for PostgreSQL | Một instance Single-AZ `db.t4g.micro` chạy 730 giờ (11,68 USD) và 20 GiB gp3 (2,30 USD). | 13,98 USD/tháng |
| Edge, media, bảo mật và AI | CloudFront, S3, ECR, Secrets Manager, GuardDuty, EventBridge, SQS, CloudWatch và Bedrock. Phí thực tế tùy dung lượng lưu, yêu cầu, log, số lần quét tệp và token sử dụng. | Tính theo mức sử dụng |

**Chi phí nền cho kịch bản chạy liên tục là 55,32 USD/tháng.** Mức này chưa bao gồm ALB LCU, phí RDS CPU credit (nếu phát sinh), dung lượng sao lưu hoặc snapshot bổ sung và các dịch vụ tính theo mức sử dụng được liệt kê ở trên. Khi ước tính kịch bản demo theo lịch, cần tính cả snapshot và tài nguyên khác được giữ lại. [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=75ccee07f5778dcee28c50a2bdf393694e87a6de)

Bản ước tính chia sẻ công khai hiển thị **55,34 USD/tháng**. So với phép tính xuất từ AWS Console, chi phí Fargate và ALB hiển thị cao hơn mỗi mục 0,01 USD. Phép tính ALB công khai giả định lưu lượng 0,001 GB/giờ vì biểu mẫu yêu cầu nhập giá trị LCU dương.

**Với khối lượng sử dụng thử nghiệm, dự trù khoảng 58 USD/tháng** cho hạ tầng chạy liên tục và các dịch vụ hỗ trợ đã được mô hình hóa. Bản xuất dữ liệu Cost Explorer giai đoạn 1–17/11/2026 cho thấy hoạt động hạ tầng chính tập trung vào ngày 11–17/11. Coi tuần này là đại diện cho một tháng 30 ngày. Cộng thêm 1,60 USD cho bốn secret đang lưu trữ và quy đổi chi phí sử dụng khác từ AWS Pricing Calculator (0,1804 USD × 30/7, bao gồm các lệnh gọi API Secrets Manager). Con số lập kế hoạch này chưa bao gồm ALB LCU, chi phí EventBridge chưa xác minh, RDS CPU credit, dung lượng sao lưu hoặc snapshot bổ sung và thuế.

### So sánh các kịch bản vận hành

| **Yếu tố chi phí** | **Kịch bản chạy liên tục** | **Kịch bản demo theo lịch** |
| :-- | :-- | :-- |
| Fargate và public IPv4 | Tính thời gian chạy theo tháng tham chiếu 730 giờ. | Đếm số giờ task thực sự hoạt động, bao gồm thời gian chuẩn bị và kiểm tra trước mỗi buổi demo. |
| ALB và RDS | Tính số giờ ALB và cơ sở dữ liệu được cung cấp tài nguyên hoặc đang chạy, cộng các khoản phí tùy cấu hình. | Ghi riêng thời gian tồn tại của ALB, thời gian chạy cơ sở dữ liệu và dung lượng snapshot giữ giữa các buổi demo. |
| Lưu trữ và cấu hình giữ lại | Đo dung lượng S3 và ECR, bản sao lưu và các thành phần cấu hình được duy trì. | Tính cả tài nguyên giữ để khôi phục trong lúc edge đang ở chế độ bảo trì. |
| Lưu lượng, quét tệp và AI | Sử dụng mức cơ sở xác định cho yêu cầu, lượt tải xuống, lượt quét và token. | Đo hoạt động mỗi phiên và ghi nhận số lần xử lý lặp. Giữ nguyên giả định mức sử dụng khi so sánh chi phí runtime. |

Sau thử nghiệm, đối chiếu chi phí từng dịch vụ với thời gian tài nguyên được cấp phát. Phân loại chênh lệch thành chi phí runtime, lưu trữ, lưu lượng hoặc xử lý tài liệu. AWS Budgets có thể thông báo khi chạm ngưỡng chi phí đã chọn, còn Cost Explorer giúp xác định dịch vụ gây tăng chi phí ngoài dự kiến.

## 7. Đánh giá rủi ro

| **Rủi ro** | **Tác động** | **Biện pháp giảm thiểu** |
| :-- | :-- | :-- |
| API hoặc cơ sở dữ liệu bị gián đoạn trong cấu hình demo | Các tính năng ứng dụng không khả dụng cho đến khi khôi phục dịch vụ. | Theo dõi readiness check, log ECS và trạng thái target ALB. Xác định quy trình phục hồi snapshot và thời gian ngừng hoạt động có thể chấp nhận. |
| PDF bị từ chối, quét thất bại hoặc xử lý chậm | Chứng chỉ hoặc CV đã tải lên chưa thể sử dụng đúng mục đích. | Hiển thị tiến độ quét, chặn thao tác đọc và trích xuất cho đến khi đủ điều kiện, theo dõi DLQ và kiểm tra đối tượng S3 tương ứng. |
| Gợi ý AI thiếu hoặc không chính xác | Gợi ý chưa được kiểm chứng có thể khiến thông tin sai được lưu vào hồ sơ. | Kiểm tra cấu trúc phản hồi và ánh xạ danh mục kỹ năng; yêu cầu người dùng xem lại và cho phép sửa trước khi lưu. |
| Migration hoặc phiên bản phát hành không tương thích | Khác biệt phiên bản có thể gây bất tương thích giữa frontend, backend và schema cơ sở dữ liệu. | Tạo snapshot trước thay đổi, phát hành theo thứ tự tương thích và ghi nhận commit, digest cùng revision để phục hồi. |
| Tài nguyên tiếp tục phát sinh phí sau demo | Compute, ALB, cơ sở dữ liệu, log và snapshot được lưu có thể tiếp tục bị tính phí. | Theo dõi giới hạn chi phí, xác định tài nguyên cần giữ lại và kiểm tra hóa đơn sau khi dọn dẹp. |

### Phân loại sự cố và khôi phục

1. **Xác định luồng bị ảnh hưởng.** Bắt đầu với readiness của API, trạng thái target ALB và ECS events. Nếu web và API vẫn truy cập được nhưng chỉ quy trình PDF bị lỗi, kiểm tra riêng trạng thái tài liệu, kết quả quét và hàng đợi.
2. **Giữ bằng chứng và các phương án phục hồi.** Ghi lại thời điểm sự cố, revision đang chạy, image digest và thay đổi gần nhất. Khi nghi ngờ lỗi rollout, xác minh tính tương thích schema trước khi quay lại một phiên bản API cũ.
3. **Xác nhận khôi phục bằng dữ liệu mẫu.** Khi phục hồi RDS, chọn đúng snapshot và kiểm tra kết nối TLS, quyền truy cập, các bản ghi mẫu và đối tượng media liên quan trên S3. So sánh thời điểm snapshot với cập nhật hợp lệ cuối cùng để xác định dữ liệu cần đối chiếu.
4. **Mở lại khi kiểm tra đạt yêu cầu.** Chỉ chuyển CloudFront về chế độ `live` sau khi health check và các luồng người dùng thiết yếu hoạt động thành công. Ghi lại nguyên nhân, cách phát hiện và những bước runbook cần cập nhật.

## 8. Kết quả kỳ vọng và tiêu chí nghiệm thu

| **Kịch bản nghiệm thu** | **Cách kiểm thử** | **Bằng chứng kỳ vọng** |
| :-- | :-- | :-- |
| Thành viên gửi đơn ứng tuyển | Dùng một vị trí đang tuyển còn chỗ. Thành viên nộp đơn và trưởng nhóm chấp nhận. | Đơn chuyển từ **Pending** sang **Accepted**. Thành viên xuất hiện trong dự án, số chỗ đã tuyển tăng đúng một lần và dữ liệu vẫn nhất quán sau khi tải lại. |
| Trưởng nhóm mời đồng đội | Chuẩn bị bộ dữ liệu kiểm thử riêng. Ứng viên đồng ý cho phép được tìm kiếm, trưởng nhóm gửi lời mời, ứng viên mở thông báo và chấp nhận. | Lời mời xác định đúng dự án và vị trí. Số chỗ đã tuyển không thay đổi khi lời mời còn Pending, chỉ cập nhật sau khi chấp nhận thành công. |
| Chat qua CloudFront | Mở hai phiên đăng nhập có quyền truy cập cùng cuộc trò chuyện, gửi tin nhắn từ một phiên. | Phiên còn lại nhận tin nhắn trong đúng cuộc trò chuyện. DevTools ghi nhận thao tác nâng cấp kết nối WebSocket với trạng thái `101`. |
| Quét PDF | Tải lên chứng chỉ mẫu hoặc PDF đầu vào AI. Theo dõi trạng thái quét từ lúc chờ đến khi có kết quả. | Các thao tác cần kết quả quét sạch vẫn bị chặn trong lúc chờ. Kết quả sạch của đúng đối tượng hoặc phiên bản S3 khiến tài liệu đủ điều kiện sử dụng cho mục đích tương ứng. |
| Bản nháp hồ sơ bằng AI | Gửi văn bản hoặc PDF đã được quét và đủ điều kiện; xem các trường được gợi ý, chỉnh sửa và xác nhận lưu. | Gợi ý có cấu trúc, cảnh báo chỉ ra dữ liệu thiếu hoặc mâu thuẫn. Hồ sơ đã lưu phản ánh đúng những trường người dùng xác nhận. |
| Phát hành và khôi phục | Truy cập trang qua CloudFront, kiểm tra readiness của API và các quy trình chính, sau đó xây dựng lại môi trường kiểm thử từ snapshot đã chọn theo runbook. | Web và API dùng cùng hostname, readiness trả về `200` và `Healthy`, dữ liệu mẫu khớp điểm khôi phục. Thông tin phát hành, snapshot ID và kết quả đối chiếu được ghi nhận. |

### Bằng chứng bàn giao

Mỗi lần kiểm thử nghiệm thu cần ghi lại môi trường, phiên bản, thao tác đã thực hiện, kết quả dự kiến và hành vi quan sát được. Dùng tài khoản mẫu cho quy trình tuyển dụng và ghi nhận trạng thái trước, sau. Với các lần phát hành, lưu commit, image digest, kết quả readiness và tham chiếu snapshot. Với PDF và AI, dùng tài liệu mẫu, lưu trạng thái quét và một bản nháp đã được người dùng xem xét. Các bản ghi này hỗ trợ kiểm chứng độc lập mà không tiết lộ thông tin đăng nhập thật hay dữ liệu cá nhân.

**Cơ sở kỹ thuật.** Tham khảo các template trong `tindy-infrastructure/cloudformation`, các runbook phát hành và triển khai lại Tindy, `DISCOVERY_PROJECT_FLOW.md`, `TEAMMATE_FLOW.md`, `AI_PROFILE_EXTRACTION.md` và tài liệu chat của backend.
