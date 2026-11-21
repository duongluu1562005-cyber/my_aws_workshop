# KIỂM TRA VÀ XÓA INTEGRATIONS

## Kiểm tra và xóa Integrations

Sau khi Runtime và Database đã được xóa, chọn **CloudFormation → Stacks → Active → tindy-integrations → Resources**. Stack báo cáo trạng thái `UPDATE_COMPLETE` và chứa 14 tài nguyên.

`integrations.yaml` quản lý GuardDuty PDF protection plan và role của nó, EventBridge result rule, hàng đợi SQS chính và DLQ cùng các queue policy, SNS topic/policy/subscription và CloudWatch alarms. Các ALB alarms vẫn thuộc quyền quản lý của Integrations sau khi Runtime bị xóa.

1. Xác nhận tên stack và danh sách tài nguyên.
2. Cuộn qua các tài nguyên để ghi lại các thành phần scanning, alerting và queue.

**📸 Chụp ảnh trạng thái stack và cả hai phần của danh sách tài nguyên.**

[Integrations trước khi xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-18-integrations-resources-before.png)

[Tài nguyên scanning và alarm](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-18a-integrations-resources-top.png)

[Tài nguyên SNS và SQS](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-18b-integrations-resources-bottom.png)

3. Một lần kiểm tra bằng AWS CLI trước các ảnh chụp này đã trả về `0` cho visible, in-flight và delayed messages trong cả `tindy-integrations-pdf-scan` và `tindy-integrations-pdf-scan-dlq`. Đây là các giá trị xấp xỉ tại thời điểm kiểm tra đó.
4. Nhấp **Delete stack**, xác nhận `tindy-integrations`, nhập tên stack và không chọn Express mode.

**📸 Chụp ảnh hộp thoại xác nhận xóa.**

[Xác nhận xóa Integrations](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-19-integrations-delete-confirm.png)

5. Xác nhận xóa. Khi stack biến mất khỏi Active, chọn **Deleted** và xác định stack được tạo vào **13 November 2026 at 20:29:01 UTC+07**.
6. Xác minh stack báo cáo `DELETE_COMPLETE`. Ảnh chụp Resources cũng cho thấy việc xóa hoàn tất của các alarms, protection plan, EventBridge rule, IAM role và SNS subscription được hiển thị.

**📸 Chụp ảnh trạng thái stack và các tài nguyên đã bị xóa.**

[Integrations đã bị xóa](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.5/cleanup-20-integrations-deleted.png)

Việc xóa Integrations đã hoàn tất. Edge, Network và Foundation là các bước tiếp theo.
