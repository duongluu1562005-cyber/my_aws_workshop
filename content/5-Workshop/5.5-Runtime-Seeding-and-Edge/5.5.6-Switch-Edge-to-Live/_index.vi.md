
# CHUYỂN EDGE SANG CHẾ ĐỘ LIVE

Runtime ở trạng thái UPDATE_COMPLETE với API Desired 1 / Running 1 / Pending 0 và ALB 1 Healthy / 0 Unhealthy. Edge vẫn ở chế độ maintenance. Giai đoạn này sử dụng Runtime Outputs để cấu hình CloudFront API origin.

## 1. Thu thập Runtime Outputs

Mở CloudFormation → tindy-runtime → Outputs và sao chép `AlbDnsName` và `VpcOriginId`. `AlbDnsName` xác định hostname của ALB nội bộ. `VpcOriginId` xác định CloudFront VPC Origin được tạo bởi Runtime. Sử dụng nút sao chép trên Console, giữ nguyên chữ hoa/chữ thường và tránh khoảng trắng khi dán. AlbDnsName chỉ được chứa hostname, không có protocol hoặc path.



[Runtime ALB DNS Output](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-59a-runtime-alb-output.png)



[Runtime VPC Origin ID Output](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-59b-runtime-vpc-origin-output.png)

## 2. Tạo Edge live change set

Chọn **tindy-edge → Stack actions → Create a change set**. Chọn **Standard change set → Use existing template → Next** và đặt tên là `tindy-edge-go-live`.

| **Tham số** | **Giá trị** | **Mục đích** |
| :--- | :--- | :--- |
| `EdgeMode` | `live` | Kích hoạt cấu hình định tuyến API live trong template. |
| `AlbDnsName` | Sao chép Output tương ứng từ tindy-runtime. | Hostname ALB nội bộ cho API origin. |
| `VpcOriginId` | Sao chép Output tương ứng từ tindy-runtime. | Kết nối origin với VPC Origin hiện tại. |
| `FrontendBucketName` | Giữ nguyên giá trị hiện có. | Tiếp tục sử dụng frontend bucket hiện có. |

Giữ nguyên các tùy chọn hiện tại và tạo change set. **📸 Chụp ảnh Parameters đã điền và Resource changes tại CREATE_COMPLETE / AVAILABLE.** Xem xét các thay đổi trước khi thực thi. Mốc này chỉ xác nhận rằng Outputs đã được thu thập. Việc kích hoạt Edge live vẫn chưa được xác minh.



Sau khi thực thi thành công ở giai đoạn tiếp theo, xác minh readiness qua HTTPS trên CloudFront hostname. Alarm dimensions, AI PDF, OAuth và frontend deployment vẫn là các giai đoạn tiếp theo riêng biệt.

## 3. Các màn hình chuẩn bị change set đã ghi lại

**Standard change set** và **Use existing template** đã được chọn cho `tindy-edge`. Banner đề cập đến `tindy-runtime-start-api` là thông tin tham chiếu đến lần thực thi Runtime trước đó.



[Edge live sử dụng template hiện có](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-60a-edge-live-existing-template.png)



Tên change set là `tindy-edge-go-live`. Phần mô tả giữ nguyên mô tả của Edge template hiện có.



[Tên Edge live change set](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-60b-edge-live-changeset-name.png)



Ảnh chụp Parameters ghi lại `EdgeMode = live`, `AlbDnsName` và `VpcOriginId` đã được điền, cùng giá trị `FrontendBucketName` được giữ lại là `tindy-foundation-frontendbucket-dxzgxfyg75gu`. ALB hostname không có protocol prefix. Sao chép nguyên văn cả hai đầu vào origin từ Runtime Outputs để tránh lỗi khi nhập lại ký tự.



[Tham số Edge live](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-60c-edge-live-parameters.png)



Tiếp tục với Next, giữ nguyên các tùy chọn hiện tại, xem xét, sau đó tạo change set. **📸 Chụp ảnh Resource changes tại CREATE_COMPLETE và execution status AVAILABLE.** Việc hoàn tất change set và kích hoạt Edge live vẫn chưa được xác minh tại mốc này.

## 4. Xem xét live change set

Change set `tindy-edge-go-live` đạt trạng thái **CREATE_COMPLETE**, Execution status **AVAILABLE**. Change set sử dụng Standard change set, Express mode Disabled và Roll back all stack resources.

| **Logical ID** | **Action** | **Loại tài nguyên** | **Replacement** |
| :--- | :--- | :--- | :--- |
| `EdgeDistribution` | Modify | `AWS::CloudFront::Distribution` | False |

Chính xác một tài nguyên được thay đổi tại chỗ, khớp với việc cập nhật cấu hình origin và routing cho chế độ live. Việc thực thi và readiness qua CloudFront vẫn chưa được xác minh tại mốc này.



[Xem xét Edge live change set](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-61-edge-live-changeset.png)

## 5. Thực thi và xác minh readiness

Chọn **Execute change set**, giữ nguyên cơ chế rollback đã cấu hình và **Use deletion policy** nếu được yêu cầu, sau đó xác nhận thực thi. Theo dõi Events cho đến khi đạt **UPDATE_COMPLETE**. Kiểm tra Outputs để xác nhận `EdgeMode = live`.



**📸 Chụp ảnh stack UPDATE_COMPLETE và EdgeMode live trong Outputs.** Sau khi cập nhật hoàn tất, chạy trong PowerShell:

```powershell
curl.exe -i https://d1vcn4g2ifl3k8.cloudfront.net/api/health/ready
```

Dự kiến HTTP 200. **📸 Chụp ảnh đầy đủ HTTP status và response body.** Nếu trả về lỗi, giữ lại response và kiểm tra cấu hình origin cùng logs trước khi xác nhận live thành công. Việc xác minh frontend và OAuth được thực hiện riêng sau đó.

## 6. Kết quả Edge live và readiness đã xác minh

Stack `tindy-edge` đạt trạng thái **UPDATE_COMPLETE**. Outputs xác nhận `EdgeMode = live`, distribution ID `E39MIF3N8DR3D6`, và hostname `d1vcn4g2ifl3k8.cloudfront.net`.



[Cập nhật Edge live hoàn tất](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-62-edge-live-update-complete.png)



Yêu cầu HTTPS đến `/api/health/ready` trả về **HTTP/1.1 200 OK** với body:

```json
{"status":"Healthy","service":"Tindy API"}
```

Response ghi lại Server Kestrel, Via CloudFront, X-Cache Miss from cloudfront, và Cache-Control no-store, no-cache. Điều này xác nhận API readiness thông qua CloudFront hostname sau khi kích hoạt live. Các luồng login, media, AI PDF và frontend vẫn chưa được xác minh bằng lần kiểm tra này.



[CloudFront API readiness HTTP 200](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-63-cloudfront-readiness-200.png)



Tiếp theo, kiểm tra frontend trên cùng hostname và đối chiếu OAuth callbacks cùng cấu hình phát hành frontend với URL mới. Integrations alarm dimensions và AI PDF vẫn là các giai đoạn tiếp theo riêng biệt.

## 7. Trang đăng nhập frontend đã hiển thị

Ảnh chụp trình duyệt ghi lại trang đăng nhập Tindy với logo, phần giới thiệu, các trường Email or Username/Password, nút Sign in và các nút Google/GitHub. Điều này xác nhận trang đăng nhập đã hiển thị. Đăng nhập bằng mật khẩu và OAuth thành công vẫn chưa được xác minh.



[Trang đăng nhập frontend Tindy](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-64-frontend-login-page.png)

### Xác minh đăng nhập tài khoản demo

Cấu hình dữ liệu backend cung cấp `demo20` hoặc `demo20@tindy.test`. Sử dụng mật khẩu đã cung cấp cho seed thông qua secret `tindy/database/seed-password`. Xem mật khẩu trong Secrets Manager → chọn secret → Retrieve secret value và nhập trực tiếp vào ứng dụng. Không đưa mật khẩu vào ảnh chụp workshop và các tệp tài liệu.



Chọn Sign in, xác minh trang kết quả và liệu dữ liệu dự án có được tải hay không. **📸 Chụp ảnh trang sau khi đăng nhập**, hoặc bất kỳ thông báo lỗi nào. Số lượng dự án thực tế và các tương tác với ứng dụng sẽ được xác minh khi thực hiện. Ảnh chụp trang đăng nhập không xác nhận những điều này. Google/GitHub OAuth yêu cầu xác minh riêng bằng callbacks cho hostname mới.

## 8. Đăng nhập và hiển thị Discover Projects đã được xác minh

Sau khi đăng nhập bằng tài khoản demo, frontend hiển thị Discover Projects và hồ sơ `Nguyễn Minh An (Demo)`. Ảnh chụp hiển thị `Student Marketplace [Demo]`, một vị trí Backend Developer, hai vị trí còn trống, các kỹ năng ASP.NET Core/C#, mức độ phù hợp Rule-based **93%** và phần giải thích các yếu tố chấm điểm.



Phần overview của bạn hiển thị **Loaded 20**. Điều này ghi lại số lượng được tải trên giao diện tại thời điểm chụp và không xác nhận tổng số dự án trong cơ sở dữ liệu. Ảnh chụp xác nhận trang sau khi đăng nhập và dữ liệu Discovery đã hiển thị. Các thao tác Apply/Save, duy trì session sau khi tải lại, Find Teammates và OAuth vẫn chưa được xác minh.



[Đăng nhập demo và Discover Projects](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-65-demo-login-discover-projects.png)



Tiếp theo, tải lại trang để kiểm tra việc duy trì session, mở **Full specs** trên project card để xem chi tiết dự án, sau đó chuyển sang **Find Teammates** để kiểm tra dữ liệu thành viên. **📸 Chụp ảnh chi tiết dự án và Find Teammates sau khi dữ liệu được tải.**

## 9. Chi tiết dự án và dữ liệu Find Teammates đã hiển thị

Project Details tải `Student Marketplace [Demo]`, trạng thái tuyển thành viên, leader `Trần Thu Hà (Demo)`, 1/7 thành viên, mô tả, mục tiêu và technology stack. Các điều khiển Apply to Position, Save và Message leader được hiển thị. Ảnh chụp xác nhận việc hiển thị chi tiết. Các thao tác ghi dữ liệu này chưa được xác minh.



[Chi tiết dự án đã tải](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-66-project-details.png)



Find Teammates hiển thị `Campus Room Booking [Demo]`, một vị trí Backend Developer với hai vị trí còn trống, ứng viên `Phan Nhật Minh (Demo)` và **55% Position Match**. Phần kiểm tra mức độ phù hợp hiển thị required skills 0%, preferred role 100%, availability 100% và optional skills 50%. Phần overview của bạn ghi nhận Loaded 19 tại thời điểm chụp.



[Find Teammates và mức độ phù hợp vị trí](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.6/deploy-67-find-teammates.png)



Cả hai trang đều hiển thị dữ liệu trong giao diện đã đăng nhập. Các ảnh chụp này không xác nhận độc lập việc tải lại bằng F5 hoặc truy xuất tất cả 50 dự án. Apply, Save, Shortlist, Invite, OAuth và media/AI yêu cầu kiểm tra riêng trong phạm vi trình diễn.
