---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# SQS + LAMBDA: PARTIAL BATCH RESPONSE, DEAD-LETTER QUEUES AND THE SNOWBALL ANTI-PATTERN

**This article was prepared to be shared with the AWS Study Group community.**

**Facebook Post:** [View on Facebook – AWS Study Group VN](https://www.facebook.com/groups/660548818043427/?multi_permalinks=2350046552426970)

## Introduction

When AWS Lambda processes messages from Amazon SQS, a single failed message can cause the entire batch to be retried. This may result in duplicate processing, unnecessary costs and increased log volume.

This article summarizes AWS documentation and introduces best practices for reliable and cost-effective message processing.

![SQS and Lambda Architecture](/my_aws_workshop/images/3_Blog/blog1.jpg)

*Figure 1. SQS and Lambda architecture with Partial Batch Response, DLQ, CloudWatch and SNS.*

## 1. Partial Batch Response

By default, when a Lambda invocation fails, the entire batch of SQS messages becomes available for retry after the visibility timeout.

Enabling `ReportBatchItemFailures` allows Lambda to report individual failed messages instead of retrying the entire batch.

Key benefits:

- Reduce unnecessary message processing.
- Minimize duplicate operations.
- Reduce execution and logging costs.
- Improve processing efficiency.

Applications should also implement idempotency because SQS messages may still be delivered more than once.

## 2. Dead-Letter Queue and Snowball Anti-pattern

Without a Dead-Letter Queue (DLQ), failed messages may be repeatedly returned to the source queue. Over time, these messages can consume processing resources and increase costs. This is known as the **snowball anti-pattern**.

AWS recommends configuring a DLQ through the SQS source queue's redrive policy.

| Configuration | Recommended starting point |
|---|---|
| Visibility timeout | At least 6 times the Lambda timeout, plus the batching window if used |
| maxReceiveCount | At least 5 |
| Dead-Letter Queue | Enabled |
| DLQ monitoring | CloudWatch Alarm and SNS |

Failed messages can be investigated and redriven after the underlying issue has been resolved.

## 3. Provisioned Mode

On November 14, 2025, AWS introduced Provisioned Mode for SQS event source mappings.

According to AWS, this feature provides:

- Up to 3 times faster scaling.
- Up to 16 times higher concurrency.
- Support for up to 20,000 concurrent executions.
- Configurable minimum and maximum event pollers.

However, Provisioned Mode introduces additional costs. Small projects should generally use the default mode unless workload measurements justify the upgrade.

## 4. Monitoring and Cost Optimization

Amazon CloudWatch and Amazon SNS can help detect processing failures and control operational costs.

Recommended practices:

- Configure a CloudWatch alarm when the DLQ contains messages.
- Monitor Lambda Errors and Throttles.
- Track individual failed messages using custom metrics.
- Configure CloudWatch Logs retention.
- Log message IDs and error information instead of unnecessary payloads.

## 5. Deployment Checklist

- Enable `ReportBatchItemFailures`.
- Implement idempotent message processing.
- Configure an appropriate visibility timeout.
- Configure a DLQ and redrive policy.
- Enable CloudWatch monitoring and SNS notifications.
- Evaluate costs before enabling Provisioned Mode.

## Conclusion

Combining Partial Batch Response, Dead-Letter Queues and CloudWatch monitoring improves the reliability of SQS and Lambda integration while reducing unnecessary processing costs.

For small cloud projects, starting with a simple configuration and gradually optimizing based on actual workload requirements is a practical approach.

## References

1. [AWS Lambda – Handling errors for an SQS event source](https://docs.aws.amazon.com/lambda/latest/dg/services-sqs-errorhandling.html)
2. [AWS – Best practices for implementing partial batch responses](https://docs.aws.amazon.com/prescriptive-guidance/latest/lambda-event-filtering-partial-batch-responses-for-sqs/best-practices-partial-batch-responses.html)
3. [AWS re:Post – Retrying valid SQS messages](https://repost.aws/knowledge-center/lambda-retrying-valid-sqs-messages)
4. [AWS Lambda – Provisioned Mode for SQS event source mapping](https://aws.amazon.com/about-aws/whats-new/2025/11/aws-lambda-provisioned-mode-sqs-esm)
5. [AWS Compute Blog – SQS event processing with Lambda Provisioned Mode](https://aws.amazon.com/blogs/compute/low-latency-high-throughput-sqs-event-processing-with-aws-lambda-provisioned-mode/)