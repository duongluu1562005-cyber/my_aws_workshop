---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# AWS IAM ACCESS ANALYZER – PHÁT HIỆN QUYỀN TRUY CẬP KHÔNG SỬ DỤNG ĐỂ TĂNG CƯỜNG BẢO MẬT ĐÁM MÂY

## Nội dung bài viết

Quản lý quyền truy cập là một phần quan trọng trong việc bảo mật môi trường AWS. Theo thời gian, người dùng và các vai trò IAM (IAM Roles) có thể tích lũy những quyền truy cập không còn cần thiết, làm gia tăng các rủi ro bảo mật tiềm ẩn.

**AWS IAM Access Analyzer** giúp các tổ chức phát hiện những quyền truy cập không được sử dụng và xem xét các quyền đã cấp cho danh tính IAM cũng như quyền truy cập vào những tài nguyên AWS được hỗ trợ. Các kết quả phân tích này góp phần thực hiện **nguyên tắc đặc quyền tối thiểu (Principle of Least Privilege)**, theo đó người dùng và dịch vụ chỉ được cấp những quyền cần thiết để thực hiện nhiệm vụ.

### Những điểm chính (Key Takeaways)

- **Unused Access Analysis (Phân tích quyền truy cập không sử dụng):** Xác định các IAM Role, Access Key, mật khẩu và quyền truy cập không được sử dụng trong một khoảng thời gian được cấu hình.
- **Least-Privilege Security (Bảo mật theo nguyên tắc đặc quyền tối thiểu):** Giúp quản trị viên xác định những quyền truy cập có thể đã được cấp rộng hơn mức cần thiết.
- **External Access Findings (Phát hiện quyền truy cập từ bên ngoài):** Phát hiện các tài nguyên được hỗ trợ có chính sách cho phép truy cập từ bên ngoài phạm vi tin cậy đã thiết lập.
- **Access Findings (Kết quả phân tích quyền truy cập):** Cung cấp thông tin để quản trị viên đánh giá liệu các quyền truy cập hiện tại có phù hợp hay không.
- **Permission Remediation (Điều chỉnh quyền truy cập):** Hỗ trợ việc xem xét, chỉnh sửa chính sách IAM, xóa thông tin xác thực không còn sử dụng hoặc kiểm tra các vai trò IAM không hoạt động.
- **Continuous Analysis (Phân tích liên tục):** Giúp tổ chức thường xuyên đánh giá quyền truy cập khi cấu hình quyền và tài nguyên AWS thay đổi.

Tuy nhiên, việc một quyền truy cập không được sử dụng không đồng nghĩa với việc quyền đó cần phải bị xóa ngay lập tức. Quản trị viên cần xác minh xem quyền đó có phục vụ các hoạt động không thường xuyên, khôi phục khẩn cấp hoặc những nhu cầu hợp lệ khác hay không.

### Ứng dụng thực tế (Practical Applications)

AWS IAM Access Analyzer phù hợp với các tổ chức quản lý nhiều người dùng IAM, vai trò IAM và tài nguyên AWS.

Ví dụ, một nhóm phát triển có thể phát hiện một IAM Role không còn hoạt động hoặc những quyền truy cập không được sử dụng trong nhiều tháng. Sau khi xem xét kết quả phân tích và xác minh yêu cầu của ứng dụng, quản trị viên có thể điều chỉnh chính sách truy cập nhằm giảm bớt các đặc quyền không cần thiết.

Việc thường xuyên rà soát quyền truy cập giúp duy trì khả năng kiểm soát bảo mật tốt hơn khi môi trường đám mây ngày càng mở rộng.

Ngoài ra, tính năng phân tích quyền truy cập không sử dụng có thể phát sinh chi phí bổ sung. Vì vậy, các tổ chức nên xem xét bảng giá AWS trước khi kích hoạt.

## Sơ đồ kiến trúc (Architecture Diagram)

![Sơ đồ quy trình AWS IAM Access Analyzer](/my_aws_workshop/images/3_Blog/anh2.png)

*Hình 2: Quy trình sử dụng IAM Access Analyzer để rà soát quyền truy cập không sử dụng và tăng cường bảo mật.*

Quy trình rà soát bảo mật IAM có thể được mô tả như sau:

**IAM Users và IAM Roles → IAM Access Analyzer → Xem xét kết quả phân tích → Xác minh yêu cầu truy cập → Điều chỉnh quyền truy cập**

Quy trình này giúp quản trị viên đánh giá các quyền truy cập có thể không còn cần thiết trước khi thực hiện thay đổi chính sách IAM.

## Tài liệu tham khảo (References)

- [AWS Documentation – IAM Access Analyzer Findings](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-findings.html)
- [AWS Documentation – Understanding IAM Access Analyzer Findings](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-concepts.html)
- [AWS Documentation – Resolving IAM Access Analyzer Findings](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-findings-remediate.html)