
# KẾT NỐI LẠI QUYỀN CI/CD SAU KHI TRIỂN KHAI LẠI

Kiểm tra AWS chỉ đọc xác nhận InfraPlannerEnabled là true, nhưng BackendDeployEnabled và LifecycleWorkflowsEnabled vẫn là false và các đầu vào frontend deploy đang để trống. Runtime được xây dựng lại có các tên API IAM role mới. Khôi phục các deployment role thông qua CloudFormation trước khi cập nhật các biến repository của GitHub Actions. Các bước này đang chờ thực thi.

## 1. Foundation change set

Mở CloudFormation → tindy-foundation → Create a change set, Standard, Use existing template. Đặt tên là `tindy-foundation-restore-cicd`.

| **Tham số** | **Giá trị** |
| :--- | :--- |
| `BackendDeployEnabled` | `true` |
| `BackendApiExecutionRoleName` | `tindy-runtime-ApiExecutionRole-ZmrKaSWrQxKZ` |
| `BackendApiTaskRoleName` | `tindy-runtime-ApiTaskRole-v8CoZDOkELnn` |
| `FrontendDeployDistributionId` | `E39MIF3N8DR3D6` |
| `FrontendDeployOidcSubject` | `repo:FCAJ-OJT-Fall26@337909751/tindy-platform-frontend@1406869416:ref:refs/heads/main` |
| `LifecycleWorkflowsEnabled` | `true` |

Frontend OIDC subject được lấy từ bản sao lưu stack-metadata trước khi dọn dẹp. Tên API role được lấy từ Runtime Resources hiện tại. Giữ GitHubOidcProviderMode Create và InfraPlannerEnabled true. Backend iam:PassRole nhắm đến các API role mới. Quyền frontend publishing nhắm đến bucket và distribution. Quyền lifecycle hỗ trợ các workflow xác minh trạng thái và snapshot của template.

Giữ nguyên các tùy chọn, xác nhận IAM capability và tạo change set. Dự kiến có các action Add cho FrontendDeployRole, BackendDeployRole và LifecycleRole. **📸 Chụp ảnh Parameters và Resource changes tại CREATE_COMPLETE / AVAILABLE** để xem xét. Sau khi thực thi thành công, lấy các role ARN mới từ Outputs và cập nhật các biến repository. Các role mới, biến GitHub và việc thực thi workflow vẫn chưa được xác minh.

## 2. Các biến repository cần đối chiếu sau đó

| **Repository** | **Biến** | **Nguồn** |
| :--- | :--- | :--- |
| Frontend | `TINDY_FRONTEND_DEPLOY_ROLE_ARN` | Foundation FrontendDeployRoleArn mới |
| Frontend | `TINDY_FRONTEND_BUCKET` | `tindy-foundation-frontendbucket-dxzgxfyg75gu` |
| Frontend | `TINDY_FRONTEND_DISTRIBUTION_ID` | `E39MIF3N8DR3D6` |
| Frontend | `TINDY_FRONTEND_HOST` | `d1vcn4g2ifl3k8.cloudfront.net` |
| Backend | `TINDY_BACKEND_DEPLOY_ROLE_ARN` | Foundation BackendDeployRoleArn mới |
| Infrastructure | `TINDY_INFRA_PLANNER_ROLE_ARN` | Foundation InfraPlannerRoleArn hiện tại |
| Infrastructure | `TINDY_LIFECYCLE_ROLE_ARN` | Foundation LifecycleRoleArn mới |

Các luồng demo còn lại được hoãn đến khi quay video theo yêu cầu của người vận hành. Các luồng chưa thực hiện không được ghi nhận là đã nghiệm thu.

## 3. Cấu hình CI/CD change set đã ghi lại

Standard change set và Use existing template đã được chọn trên `tindy-foundation`, với tên `tindy-foundation-restore-cicd`.

[CI/CD sử dụng template hiện có](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-79a-cicd-existing-template.png)

[Tên CI/CD change set](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-79c-cicd-changeset-name.png)

Ảnh chụp Parameters khớp với hướng dẫn: BackendDeployEnabled true, cả hai API role mới, distribution `E39MIF3N8DR3D6`, frontend OIDC subject đã được kiểm tra đối chiếu với bản sao lưu, và LifecycleWorkflowsEnabled true. GitHubOidcProviderMode vẫn là Create và InfraPlannerEnabled true.

[Tham số CI/CD Foundation](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-79b-cicd-parameters.png)

Các giá trị này khôi phục quyền GitHub Actions cho các tài nguyên được xây dựng lại. Đây là cấu hình CI/CD OIDC. Các bước cấu hình OAuth đăng nhập người dùng nằm ngoài phạm vi workshop.

Tiếp tục với Next, giữ nguyên các tùy chọn, xác nhận IAM capability và tạo change set. **📸 Chụp ảnh Resource changes tại CREATE_COMPLETE / AVAILABLE**. Dự kiến có các action Add cho `FrontendDeployRole`, `BackendDeployRole` và `LifecycleRole`. Việc hoàn tất change set và tạo các role mới vẫn chưa được xác minh tại mốc này.

## 4. Xem xét ba CI/CD role

Resource changes xác nhận chính xác ba action **Add** với loại `AWS::IAM::Role`:

| **Logical ID** | **Mục đích** |
| :--- | :--- |
| `BackendDeployRole` | Phát hành API image và cập nhật ECS, với PassRole được giới hạn cho các API role đã cấu hình. |
| `FrontendDeployRole` | Phát hành frontend lên bucket và thực hiện invalidation cho distribution đã cấu hình. |
| `LifecycleRole` | Hỗ trợ các workflow xác minh trạng thái và snapshot theo quyền của template. |

[Ba CI/CD role được thêm](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-80-cicd-three-role-adds.png)

Danh sách khớp với phạm vi khôi phục CI/CD. Ảnh chụp này hiển thị Resource changes mà không có trạng thái Overview. Trong Console, xác nhận **CREATE_COMPLETE / AVAILABLE**, thực thi change set và giữ rollback cùng Use deletion policy nếu được yêu cầu. Chờ Foundation đạt **UPDATE_COMPLETE**.

**📸 Chụp ảnh trạng thái stack và Outputs**, bao gồm `FrontendDeployRoleArn`, `BackendDeployRoleArn`, `LifecycleRoleArn` và `InfraPlannerRoleArn`. Sử dụng các ARN thực tế sau khi cập nhật cho các biến repository. Việc tạo role và cập nhật biến GitHub vẫn chưa được xác minh tại mốc này.

## 5. Foundation hoàn tất và các role ARN đã xác minh

Ảnh chụp Outputs hiển thị cả bốn role. Kiểm tra AWS CLI chỉ đọc xác nhận Foundation **UPDATE_COMPLETE** và các ARN sau:

| **Output** | **ARN** |
| :--- | :--- |
| FrontendDeployRoleArn | `arn:aws:iam::572454213668:role/tindy-foundation-FrontendDeployRole-3fN15Svzx4RJ` |
| BackendDeployRoleArn | `arn:aws:iam::572454213668:role/tindy-foundation-BackendDeployRole-gTP2d0hD0F1W` |
| LifecycleRoleArn | `arn:aws:iam::572454213668:role/tindy-foundation-LifecycleRole-VTyUOZ4hpsjt` |
| InfraPlannerRoleArn | `arn:aws:iam::572454213668:role/tindy-foundation-InfraPlannerRole-xhQ3LjfCL4Xt` |

[Frontend deploy role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81a-frontend-deploy-role-output.png)

[Backend deploy role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81b-backend-deploy-role-output.png)

[Lifecycle role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81c-lifecycle-role-output.png)

[Infra planner role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81d-infra-planner-role-output.png)

## 6. Cập nhật các biến GitHub repository

Trong mỗi repository, mở **Settings → Secrets and variables → Actions → Variables → Repository variables**. Chỉnh sửa các biến hiện có hoặc tạo các biến còn thiếu bằng tên và nguồn trong phần 2. Sử dụng các ARN frontend/backend/lifecycle mới ở trên và planner ARN hiện tại đã được xác minh. Sử dụng bucket, distribution ID và host từ phần 2. TINDY_FRONTEND_HOST chứa hostname không có URL scheme.

**📸 Chụp ảnh danh sách biến đã lưu trong cả ba repository.** Đây là các biến repository của GitHub Actions, tách biệt với application secrets. Việc cập nhật biến GitHub và xác thực OIDC thành công của workflow vẫn chưa được xác minh tại mốc này. Các luồng UI demo còn lại được hoãn đến khi quay video.

## 7. Các biến repository đã ghi lại

Frontend có đầy đủ cả bốn biến. Các giá trị bucket, distribution ID và hostname hiển thị khớp với hướng dẫn. Backend có biến deploy role. Infrastructure có các biến planner và lifecycle. Các hàng hiển thị những lần cập nhật gần đây, nhưng giá trị ARN bị cắt ngắn trong ảnh chụp, vì vậy chỉ riêng các ảnh này không xác nhận toàn bộ chuỗi ARN.

[Các biến frontend repository](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-82a-frontend-repository-variables.png)

[Biến backend repository](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-82b-backend-repository-variable.png)

[Các biến infrastructure repository](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-82c-infra-repository-variables.png)

## 8. Xác minh federation bằng các workflow hiện có

Mã nguồn workflow cục bộ cung cấp hai cách xác minh phù hợp. Trong backend repository, mở **Actions → Verify backend OIDC → Run workflow**, chọn **main**. Workflow kiểm tra biến role, đảm nhận role thông qua OIDC và xác minh STS identity mà không triển khai API.

Trong frontend repository, mở **Actions → Deploy frontend → Run workflow**, chọn **main**, mode **verify-oidc**, và để trống release_sha. Chế độ này xác minh federation. Không chọn deploy cho lần kiểm tra này.

**📸 Chụp ảnh cả hai lần chạy workflow thành công và các bước xác minh danh tính.** Nếu GitHub workflow khác với mã nguồn cục bộ, kiểm tra nội dung của nó trước khi chạy. Việc thực thi workflow và federation frontend/backend vẫn chưa được xác minh. Việc thực thi các workflow planner và lifecycle cũng vẫn chưa được xác minh tại mốc này.

## 9. Kết quả workflow xác minh CI/CD

**Verify backend OIDC #2** trên main, commit rút gọn `844ff5a`, đạt trạng thái **Success** trong 26 giây với job verify-oidc thành công. Workflow đã được kiểm tra thực hiện kiểm tra cấu hình, đảm nhận role thông qua OIDC và xác minh STS identity. Ảnh chụp Summary này ghi lại kết quả lần chạy, không có ảnh chụp log danh tính riêng biệt.

[Xác minh backend OIDC thành công](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-83-backend-oidc-success.png)

**Deploy frontend #4** trên main, commit rút gọn `233bbff`, đạt trạng thái **Success** trong 21 giây. Build đã được bỏ qua và publish thành công, phù hợp với chế độ verify-oidc được hướng dẫn. Điều này xác nhận việc xác minh federation và không ghi nhận một bản phát hành frontend mới.

[Xác minh frontend OIDC thành công](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-84-frontend-oidc-success.png)

Cả hai lần chạy đều hiển thị thông báo chuyển đổi ubuntu-latest runner nhưng vẫn giữ trạng thái Success. Các kiểm tra federation frontend/backend đã thành công theo kết quả workflow. Việc phát hành backend thực tế, triển khai frontend và các lần chạy planner/lifecycle sau khi triển khai lại chưa được thực hiện. Các luồng demo còn lại được hoãn đến khi quay video theo yêu cầu của người vận hành.
