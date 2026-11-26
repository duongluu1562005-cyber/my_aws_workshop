---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# SQS + LAMBDA: PARTIAL BATCH RESPONSE, DEAD-LETTER QUEUE VÀ VÒNG LẶP SNOWBALL

**Bài viết được chuẩn bị để chia sẻ với cộng đồng AWS Study Group.**

**Bài đăng Facebook:** [Xem bài viết tại AWS Study Group VN](https://www.facebook.com/groups/660548818043427/?multi_permalinks=2350046552426970)

## Giới thiệu

Khi AWS Lambda xử lý các thông điệp từ Amazon SQS, chỉ một thông điệp bị lỗi cũng có thể khiến toàn bộ batch phải xử lý lại. Điều này có thể gây trùng lặp dữ liệu, phát sinh chi phí và tăng dung lượng log.

Bài viết tổng hợp kiến thức từ tài liệu AWS và giới thiệu các phương pháp giúp xử lý thông điệp hiệu quả, đáng tin cậy và tiết kiệm chi phí.

![Kiến trúc SQS và Lambda](/my_aws_workshop/images/3_Blog/blog1.jpg)

*Hình 1. Kiến trúc SQS và Lambda kết hợp Partial Batch Response, DLQ, CloudWatch và SNS.*

## 1. Partial Batch Response

Theo mặc định, khi Lambda xử lý thất bại, toàn bộ batch thông điệp SQS sẽ có thể được xử lý lại sau khi visibility timeout kết thúc.

Việc kích hoạt `ReportBatchItemFailures` cho phép Lambda báo cáo từng thông điệp bị lỗi thay vì xử lý lại toàn bộ batch.

Các lợi ích chính:

- Giảm việc xử lý thông điệp không cần thiết.
- Hạn chế các thao tác bị trùng lặp.
- Giảm chi phí thực thi và lưu trữ log.
- Nâng cao hiệu quả xử lý.

Ứng dụng cũng cần đảm bảo tính idempotency để tránh tác động trùng lặp khi một thông điệp được gửi nhiều lần.

## 2. Dead-Letter Queue và vòng lặp Snowball

Nếu không có Dead-Letter Queue (DLQ), các thông điệp bị lỗi có thể liên tục quay trở lại hàng đợi nguồn. Theo thời gian, chúng sẽ tiêu tốn tài nguyên xử lý và làm tăng chi phí. Hiện tượng này được gọi là **snowball anti-pattern**.

AWS khuyến nghị cấu hình DLQ thông qua redrive policy của hàng đợi SQS nguồn.

| Cấu hình | Giá trị khuyến nghị ban đầu |
|---|---|
| Visibility timeout | Ít nhất 6 lần Lambda timeout, cộng thêm batching window nếu sử dụng |
| maxReceiveCount | Tối thiểu 5 |
| Dead-Letter Queue | Bật |
| Giám sát DLQ | CloudWatch Alarm và SNS |

Các thông điệp bị lỗi có thể được kiểm tra và đưa trở lại hàng đợi để xử lý sau khi nguyên nhân gây lỗi đã được khắc phục.

## 3. Provisioned Mode

Ngày 14/11/2025, AWS giới thiệu Provisioned Mode cho SQS event source mapping.

Theo AWS, tính năng này cung cấp:

- Tốc độ mở rộng nhanh hơn tới 3 lần.
- Khả năng xử lý đồng thời cao hơn tới 16 lần.
- Hỗ trợ tối đa 20.000 lượt thực thi đồng thời.
- Cho phép cấu hình số lượng event poller tối thiểu và tối đa.

Tuy nhiên, Provisioned Mode phát sinh thêm chi phí. Đối với các dự án nhỏ, nên ưu tiên chế độ mặc định và chỉ nâng cấp khi có nhu cầu thực tế dựa trên kết quả đo lường.

## 4. Giám sát và tối ưu chi phí

Amazon CloudWatch và Amazon SNS giúp phát hiện lỗi trong quá trình xử lý và kiểm soát chi phí vận hành.

Các phương pháp được khuyến nghị:

- Cấu hình CloudWatch Alarm khi DLQ có thông điệp.
- Theo dõi các chỉ số Errors và Throttles của Lambda.
- Sử dụng custom metrics để theo dõi từng thông điệp bị lỗi.
- Thiết lập thời gian lưu trữ CloudWatch Logs.
- Chỉ ghi log mã thông điệp và thông tin lỗi cần thiết.

## 5. Checklist trước khi triển khai

- Kích hoạt `ReportBatchItemFailures`.
- Đảm bảo tính idempotency khi xử lý thông điệp.
- Cấu hình visibility timeout phù hợp.
- Cấu hình DLQ và redrive policy.
- Thiết lập giám sát CloudWatch và thông báo SNS.
- Đánh giá chi phí trước khi sử dụng Provisioned Mode.

## Kết luận

Việc kết hợp Partial Batch Response, Dead-Letter Queue và CloudWatch giúp nâng cao độ tin cậy của hệ thống SQS và Lambda, đồng thời giảm chi phí xử lý không cần thiết.

Đối với các dự án cloud quy mô nhỏ, nên bắt đầu với cấu hình đơn giản và từng bước tối ưu dựa trên nhu cầu thực tế của hệ thống.

## Tài liệu tham khảo

1. [AWS Lambda – Handling errors for an SQS event source](https://docs.aws.amazon.com/lambda/latest/dg/services-sqs-errorhandling.html)
2. [AWS – Best practices for implementing partial batch responses](https://docs.aws.amazon.com/prescriptive-guidance/latest/lambda-event-filtering-partial-batch-responses-for-sqs/best-practices-partial-batch-responses.html)
3. [AWS re:Post – Retrying valid SQS messages](https://repost.aws/knowledge-center/lambda-retrying-valid-sqs-messages)
4. [AWS Lambda – Provisioned Mode for SQS event source mapping](https://aws.amazon.com/about-aws/whats-new/2025/11/aws-lambda-provisioned-mode-sqs-esm)
5. [AWS Compute Blog – SQS event processing with Lambda Provisioned Mode](https://aws.amazon.com/blogs/compute/low-latency-high-throughput-sqs-event-processing-with-aws-lambda-provisioned-mode/)