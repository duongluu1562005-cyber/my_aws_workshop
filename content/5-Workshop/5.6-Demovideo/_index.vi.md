---
title: "KIỂM TRA ỨNG DỤNG VÀ VIDEO DEMO"
date: 2026-11-19
weight: 6
chapter: false
pre: "<b>5.6 </b>"
---

Sau khi triển khai sáu CloudFormation Stack, thực hiện bootstrap, migration và seed dữ liệu, đồng thời chuyển Edge sang môi trường hoạt động thực tế, tiến hành kiểm tra ứng dụng Tindy thông qua giao diện web.

Phần này mô tả quy trình một Backend Developer hoàn thiện hồ sơ cá nhân, tạo dự án, tìm kiếm cơ hội và đồng đội, trao đổi tin nhắn trong dự án và bổ sung thông tin hồ sơ từ tệp CV PDF.

## Video Demo

Video được ghi vào ngày **23/11/2026**, có thời lượng khoảng **7 phút 22 giây**. Hai cửa sổ trình duyệt được sử dụng để tạo các phiên đăng nhập riêng biệt, phục vụ việc tương tác giữa thành viên và người quản lý dự án.

Nên xem video ở chế độ toàn màn hình để đọc rõ các biểu mẫu và nội dung tin nhắn.

**Tindy — Video trình diễn ứng dụng trên AWS**

<video width="100%" controls preload="metadata">
  <source src="/my_aws_workshop/images/5-Workshop/5.6-Demovideo/video-demo.mp4" type="video/mp4">
  Trình duyệt của bạn không hỗ trợ phát video.
</video>

[Xem video Demo](/my_aws_workshop/images/5-Workshop/5.6-Demovideo/video-demo.mp4)

### Hướng dẫn xem video

Các mốc thời gian dưới đây giúp xác định những nội dung chính trong video.

| **Thời gian** | **Nội dung** |
|---|---|
| 00:00–01:50 | Đăng nhập và hoàn thiện hồ sơ Backend Developer. |
| 01:50–03:05 | Tạo và đăng dự án Campus Study Hub [Demo]. |
| 03:05–04:00 | Khám phá dự án, ứng tuyển và xét duyệt ứng viên với vai trò Leader. |
| 04:00–04:40 | Trao đổi tin nhắn giữa hai phiên đăng nhập. |
| 04:40–06:00 | Tìm kiếm đồng đội, gửi lời mời và kiểm tra trạng thái Accepted. |
| 06:00–06:25 | Trò chuyện trong dự án và chuyển sang AI Studio để chọn CV PDF. |
| 06:25–07:22 | Quét CV bằng AI Studio, trích xuất gợi ý và cập nhật hồ sơ. |

## 1. Chuẩn bị các phiên Demo

Mở ứng dụng thông qua tên miền CloudFront đã triển khai. Trong phiên Demo này, địa chỉ được sử dụng là:

`https://d1vcn4g2ifl3k8.cloudfront.net`

Đối với môi trường khác, có thể lấy giá trị `DistributionDomainName` từ Outputs của CloudFormation Stack Edge.

Sử dụng một tài khoản mới để thực hiện quy trình tạo hồ sơ và một tài khoản Demo đã có dữ liệu seed trong cửa sổ trình duyệt riêng.

Các tài khoản được seed dữ liệu đã hoàn tất quá trình tạo hồ sơ, có thể thực hiện vai trò xét duyệt ứng tuyển, nhận lời mời tham gia dự án và trao đổi tin nhắn. Nên sử dụng dữ liệu mẫu cho các thao tác tạo hoặc thay đổi dữ liệu trong bài thực hành.

Trước khi ghi hình, quá trình kiểm tra hệ thống thông qua CloudFront trả về HTTP 200 với trạng thái Healthy. ECS đang chạy một API Task và bốn CloudWatch Alarms được ghi nhận ở trạng thái OK.

Các bằng chứng kiểm tra hạ tầng được trình bày trong mục 5.4 và 5.5.

## 2. Hoàn thiện hồ sơ Backend Developer

Thực hiện các bước khai báo thông tin trong quy trình onboarding, bao gồm:

- Basic information
- Technical skills
- Preferred role
- Availability
- Interests & Goals
- Projects & experience
- GitHub & Links
- Review & complete profile

| **Thông tin** | **Nội dung thực hiện** |
|---|---|
| Basic information | Nhập tên hiển thị, tên người dùng và trường đại học hoặc cộng đồng. |
| Skills | Chọn các kỹ năng như .NET, C#, ASP.NET Core, Docker, PostgreSQL và Git. |
| Role | Chọn vai trò Backend Developer. |
| Availability | Nhập số giờ có thể tham gia mỗi tuần. |
| Goals and experience | Mô tả kinh nghiệm về REST API, cơ sở dữ liệu và mục tiêu học tập khi làm việc nhóm. |
| Review | Kiểm tra thông tin hồ sơ trước khi hoàn tất onboarding. |

Thông tin hồ sơ giúp giới thiệu năng lực của thành viên và cung cấp dữ liệu đầu vào cho quá trình ghép nối.

Điểm Discovery được minh họa trong các bước tiếp theo là kết quả của cơ chế **Rule-based match**, tức là đánh giá mức độ phù hợp dựa trên các quy tắc được thiết lập.

![Lựa chọn kỹ năng trong quá trình onboarding](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-50.jpg)

## 3. Tạo dự án và vị trí tuyển thành viên

Truy cập **Leader Workspace → Create project**.

Trong video, dự án `Campus Study Hub [Demo]` được tạo với phần mô tả, loại dự án Web Application và vị trí tuyển dụng Frontend Developer yêu cầu kỹ năng React/TypeScript.

Quy trình tạo dự án bao gồm ba giai đoạn:

**The big picture → People & skills → Commitment & review**

Người dùng nhập thông tin dự án, yêu cầu của vị trí tuyển dụng, thời gian cam kết và hạn tuyển thành viên, sau đó kiểm tra và đăng dự án.

Trong video, phần xác nhận thể hiện các thông tin:

- Một vị trí tuyển dụng.
- Thời gian tham gia 10 giờ/tuần.
- Thời hạn dự án ba tháng.
- Quy mô nhóm tối đa năm thành viên.

Sau khi lưu, dự án xuất hiện trong Leader Workspace với trạng thái đang tuyển thành viên, sẵn sàng tiếp nhận đơn ứng tuyển và hỗ trợ tìm kiếm đồng đội.

![Dự án đã tạo trong Leader Workspace](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-180.jpg)

## 4. Khám phá dự án và xét duyệt ứng tuyển

Truy cập **Discover Projects → Find Projects** để xem danh sách dự án, các vị trí đang tuyển và điểm đánh giá mức độ phù hợp.

Người dùng có thể mở thông tin chi tiết dự án hoặc xem phần giải thích điểm ghép nối trước khi ứng tuyển.

Trong video, một đơn ứng tuyển được gửi đến dự án `Learning Roadmap Board [Demo]` cho vị trí Backend Developer, kèm theo tin nhắn giới thiệu.

Ở phiên đăng nhập của Leader, mục Candidate applications hiển thị ứng viên và chức năng **Accept to team**.

Sau khi đơn ứng tuyển được xử lý, cả hai người dùng đều xuất hiện trong phần trò chuyện của dự án.

Điểm ghép nối trong video chỉ mang tính minh họa, không phải giá trị cố định cho mọi người dùng. Kết quả phụ thuộc vào thông tin hồ sơ, yêu cầu vị trí, thời gian có thể tham gia và dữ liệu hiện tại.

![Leader xét duyệt đơn ứng tuyển](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-230.jpg)

## 5. Trao đổi tin nhắn trong dự án

Truy cập **Messages → Project chats** và chọn một dự án mà cả hai tài khoản đều tham gia.

Gửi tin nhắn từ một phiên đăng nhập, kiểm tra nội dung hiển thị ở cửa sổ còn lại và gửi tin nhắn phản hồi để kiểm tra khả năng trao đổi hai chiều.

Video minh họa việc nhắn tin hai chiều trong dự án `Learning Roadmap Board [Demo]`.

Sau khi lời mời tham gia dự án được chấp nhận, `Campus Study Hub [Demo]` cũng hiển thị các sự kiện tham gia của thành viên và tin nhắn trong nhóm.

Đây là bằng chứng về hoạt động của chức năng trên giao diện. Video không thể hiện riêng phản hồi WebSocket 101 trong DevTools.

![Hai phiên đăng nhập trao đổi tin nhắn](https://mjoy1410.github.io/FCAJ-Worklog/images/5-Workshop/demo/frame-250.jpg)

## 6. Tìm kiếm đồng đội và gửi lời mời

Chuyển sang chức năng **Find Teammates**, sau đó chọn dự án đang quản lý và vị trí cần tuyển.

