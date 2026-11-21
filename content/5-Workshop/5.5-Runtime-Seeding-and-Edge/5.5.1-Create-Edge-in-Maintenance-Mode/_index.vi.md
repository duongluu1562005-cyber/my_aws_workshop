
# TẠO EDGE Ở CHẾ ĐỘ BẢO TRÌ

`edge.yaml` tạo CloudFront distribution, Origin Access Control, frontend bucket policy và maintenance routing function. Tạo Edge trước Runtime để lấy hostname mới cho API public host và cấu hình OAuth.

1. Tại `us-east-1`, chọn **CloudFormation → Create stack → With new resources (standard)** và tải lên `edge.yaml`.

**📸 Chụp ảnh tên tệp đã chọn.**



[Template Edge đã tải lên](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-29-edge-template.png)

2. Đặt tên stack là `tindy-edge`. Đặt `FrontendBucketName` từ Foundation Outputs, sử dụng `EdgeMode = maintenance` và để trống `VpcOriginId` và `AlbDnsName`.

**📸 Chụp ảnh tên stack và cả bốn tham số.**



[Tham số Edge maintenance](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-30-edge-maintenance-parameters.png)

3. Thêm các tag `Project=Tindy`, `Environment=tindy`, `Owner=TindyTeam`. Giữ Express mode ở trạng thái tắt và rollback ở trạng thái bật, xem xét và tạo stack.
4. Chờ `CREATE_COMPLETE`, sau đó ghi lại `DistributionId`, `DistributionDomainName` và `EdgeMode` từ Outputs.

**📸 Chụp ảnh trạng thái hoàn tất và Outputs.**

5. Thay `<cloudfront-hostname>` bằng hostname của distribution mới và chạy trong PowerShell.

```powershell
curl.exe -i "https://<cloudfront-hostname>/api/health/ready"
```

Xác minh HTTP `503` và `{"status":"maintenance"}`. Giữ chế độ maintenance được bật cho đến khi Runtime và database schema sẵn sàng.



**📸 Chụp ảnh lệnh, trạng thái HTTP và JSON.**

## Tiến độ tạo Edge

Template edge.yaml đã được chọn, với tên stack tindy-edge, frontend bucket từ Foundation, EdgeMode maintenance và AlbDnsName/VpcOriginId để trống. Cả ba tag vận hành đã được nhập.



[Tags của Edge](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-30a-edge-tags.png)



Trang Review hiển thị nút **Submit** và termination protection ở trạng thái **Deactivated**.



[Review Edge trước khi Submit](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-30b-edge-review-submit.png)



Stack đạt trạng thái **CREATE_COMPLETE**. Outputs xác nhận distribution `E39MIF3N8DR3D6`, hostname `d1vcn4g2ifl3k8.cloudfront.net` và mode `maintenance`.



[Edge hoàn tất và Outputs mới](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-32-edge-outputs.png)



Sử dụng hostname này cho Runtime `CloudFrontPublicHost`. Kiểm tra maintenance qua HTTPS.

```powershell
curl.exe -i https://d1vcn4g2ifl3k8.cloudfront.net/api/health/ready
```

**📸 Chụp ảnh lệnh, HTTP 503 và maintenance JSON.** Một lần kiểm tra HTTPS bằng curl đã xác minh HTTP `503 Service Unavailable`, `Server: CloudFront`, `Cache-Control: no-store` và `{"status":"maintenance"}`. Ảnh chụp màn hình terminal được ghi lại bên dưới.



[HTTPS 503 maintenance](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.1/deploy-33-edge-maintenance-check.png)
