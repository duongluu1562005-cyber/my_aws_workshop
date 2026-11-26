---
title: "KIỂM TRA HẠ TẦNG TRƯỚC KHI DỌN DẸP"
date: 2026-11-19
weight: 1
chapter: false
pre: "<b> 5.7.1 </b>"
---

## Ghi lại các stack hiện có

1. Mở **CloudFormation → Stacks** tại `us-east-1`, chọn **Active**, và lọc theo `tindy`.
2. Xác nhận cả sáu stack đều tồn tại. Foundation, Integrations, Runtime và Edge ban đầu báo cáo trạng thái `UPDATE_COMPLETE`. Network và Database báo cáo trạng thái `CREATE_COMPLETE`. Hoàn tất các quá trình cập nhật đang hoạt động trước khi tiếp tục.

**📸 Chụp ảnh tên và trạng thái của sáu stack.**



![Các stack Tindy ban đầu](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.1/cleanup-01-stacks-before.png)

3. Mở **tindy-edge → Outputs**, ghi lại `DistributionId`, `DistributionDomainName` và `EdgeMode`, ban đầu là `live`.

![Outputs Edge ban đầu](/my_aws_workshop/images/5-Workshop/5.7-Cleanup/5.7.1/cleanup-02-edge-before.png)

