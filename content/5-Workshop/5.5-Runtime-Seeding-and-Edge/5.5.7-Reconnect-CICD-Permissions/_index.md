
# RECONNECT CI/CD PERMISSIONS AFTER REDEPLOY

Read-only AWS inspection confirms InfraPlannerEnabled is true, but BackendDeployEnabled and LifecycleWorkflowsEnabled remain false and frontend deploy inputs are empty. The rebuilt Runtime has new API IAM role names. Restore deployment roles through CloudFormation before updating GitHub Actions repository variables. These steps await execution.

## 1. Foundation change set

Open CloudFormation → tindy-foundation → Create a change set, Standard, Use existing template. Name it `tindy-foundation-restore-cicd`.

| **Parameter**                  | **Value**                                                                               |
| :----------------------------- | :-------------------------------------------------------------------------------------- |
| `BackendDeployEnabled`         | `true`                                                                                  |
| `BackendApiExecutionRoleName`  | `tindy-runtime-ApiExecutionRole-ZmrKaSWrQxKZ`                                           |
| `BackendApiTaskRoleName`       | `tindy-runtime-ApiTaskRole-v8CoZDOkELnn`                                                |
| `FrontendDeployDistributionId` | `E39MIF3N8DR3D6`                                                                        |
| `FrontendDeployOidcSubject`    | `repo:FCAJ-OJT-Fall26@337909751/tindy-platform-frontend@1406869416:ref:refs/heads/main` |
| `LifecycleWorkflowsEnabled`    | `true`                                                                                  |

The frontend OIDC subject comes from the pre-cleanup stack-metadata backup. API role names come from current Runtime Resources. Retain GitHubOidcProviderMode Create and InfraPlannerEnabled true. Backend iam:PassRole targets the new API roles. Frontend publishing permissions target the bucket and distribution. Lifecycle permissions support the template’s verification and snapshot workflows.

Retain options, acknowledge IAM capability and create the change set. Expect Adds for FrontendDeployRole, BackendDeployRole and LifecycleRole. **📸 Capture Parameters and Resource changes at CREATE_COMPLETE / AVAILABLE** for review. After successful execution, obtain new role ARNs from Outputs and update repository variables. New roles, GitHub variables and workflow execution remain unverified.

## 2. Repository variables to reconcile afterward

| **Repository** | **Variable**                     | **Source**                                     |
| :------------- | :------------------------------- | :--------------------------------------------- |
| Frontend       | `TINDY_FRONTEND_DEPLOY_ROLE_ARN` | New Foundation FrontendDeployRoleArn           |
| Frontend       | `TINDY_FRONTEND_BUCKET`          | `tindy-foundation-frontendbucket-dxzgxfyg75gu` |
| Frontend       | `TINDY_FRONTEND_DISTRIBUTION_ID` | `E39MIF3N8DR3D6`                               |
| Frontend       | `TINDY_FRONTEND_HOST`            | `d1vcn4g2ifl3k8.cloudfront.net`                |
| Backend        | `TINDY_BACKEND_DEPLOY_ROLE_ARN`  | New Foundation BackendDeployRoleArn            |
| Infrastructure | `TINDY_INFRA_PLANNER_ROLE_ARN`   | Current Foundation InfraPlannerRoleArn         |
| Infrastructure | `TINDY_LIFECYCLE_ROLE_ARN`       | New Foundation LifecycleRoleArn                |

Remaining demo flows are deferred to video recording at the operator’s request. Unperformed flows are not recorded as accepted.

## 3. Recorded CI/CD change-set configuration

Standard change set and Use existing template were selected on `tindy-foundation`, with name `tindy-foundation-restore-cicd`.

[CI/CD uses existing template](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-79a-cicd-existing-template.png)

[CI/CD change set name](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-79c-cicd-changeset-name.png)

The Parameters screenshot matches the guide: BackendDeployEnabled true, both new API roles, distribution `E39MIF3N8DR3D6`, the frontend OIDC subject checked against backup, and LifecycleWorkflowsEnabled true. GitHubOidcProviderMode remains Create and InfraPlannerEnabled true.

[CI/CD Foundation parameters](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-79b-cicd-parameters.png)

These values restore GitHub Actions permissions for rebuilt resources. This is CI/CD OIDC configuration. User-login OAuth configuration steps are outside the workshop scope.

Continue with Next, retain options, acknowledge IAM capability and create the change set. **📸 Capture Resource changes at CREATE_COMPLETE / AVAILABLE**. Expect Adds for `FrontendDeployRole`, `BackendDeployRole` and `LifecycleRole`. Change-set completion and creation of the new roles remain unverified at this milestone.

## 4. Review the three CI/CD roles

Resource changes confirms exactly three **Add** actions with type `AWS::IAM::Role`:

| **Logical ID**       | **Purpose**                                                                          |
| :------------------- | :----------------------------------------------------------------------------------- |
| `BackendDeployRole`  | Publish API images and update ECS, with PassRole scoped to the configured API roles. |
| `FrontendDeployRole` | Publish frontend to the bucket and invalidate the configured distribution.           |
| `LifecycleRole`      | Support state verification and snapshot workflows under template permissions.        |

[Three CI/CD role additions](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-80-cicd-three-role-adds.png)

The list matches the CI/CD restoration scope. This screenshot shows Resource changes without the Overview status. In Console, confirm **CREATE_COMPLETE / AVAILABLE**, execute the change set, and retain rollback and Use deletion policy if prompted. Wait for Foundation **UPDATE_COMPLETE**.

**📸 Capture stack status and Outputs**, including `FrontendDeployRoleArn`, `BackendDeployRoleArn`, `LifecycleRoleArn` and `InfraPlannerRoleArn`. Use actual post-update ARNs for repository variables. Role creation and GitHub variable updates remain unverified at this milestone.

## 5. Foundation completion and verified role ARNs

Outputs screenshots show all four roles. Read-only AWS CLI inspection confirms Foundation **UPDATE_COMPLETE** and the following ARNs:

| **Output**            | **ARN**                                                                           |
| :-------------------- | :-------------------------------------------------------------------------------- |
| FrontendDeployRoleArn | `arn:aws:iam::572454213668:role/tindy-foundation-FrontendDeployRole-3fN15Svzx4RJ` |
| BackendDeployRoleArn  | `arn:aws:iam::572454213668:role/tindy-foundation-BackendDeployRole-gTP2d0hD0F1W`  |
| LifecycleRoleArn      | `arn:aws:iam::572454213668:role/tindy-foundation-LifecycleRole-VTyUOZ4hpsjt`      |
| InfraPlannerRoleArn   | `arn:aws:iam::572454213668:role/tindy-foundation-InfraPlannerRole-xhQ3LjfCL4Xt`   |

[Frontend deploy role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81a-frontend-deploy-role-output.png)

[Backend deploy role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81b-backend-deploy-role-output.png)

[Lifecycle role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81c-lifecycle-role-output.png)

[Infra planner role](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-81d-infra-planner-role-output.png)

## 6. Update GitHub repository variables

In each repository, open **Settings → Secrets and variables → Actions → Variables → Repository variables**. Edit existing variables or create missing ones using the names and sources in section 2. Use the new frontend/backend/lifecycle ARNs above and the verified current planner ARN. Use the bucket, distribution ID and host from section 2. TINDY_FRONTEND_HOST contains the hostname without a URL scheme.

**📸 Capture saved variable lists in all three repositories.** These are GitHub Actions repository variables, separate from application secrets. GitHub variable updates and successful workflow OIDC authentication remain unverified at this milestone. Remaining UI demo flows are deferred to video recording.

## 7. Recorded repository variables

Frontend has all four variables. Visible bucket, distribution ID and hostname values match the guide. Backend has its deploy role variable. Infrastructure has planner and lifecycle variables. Rows show recent updates, but ARN values are truncated in the screenshots, so the images alone do not establish the complete ARN strings.

[Frontend repository variables](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-82a-frontend-repository-variables.png)

[Backend repository variable](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-82b-backend-repository-variable.png)

[Infrastructure repository variables](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-82c-infra-repository-variables.png)

## 8. Verify federation using existing workflows

Local workflow source provides two suitable verification paths. In the backend repository, open **Actions → Verify backend OIDC → Run workflow**, selecting **main**. It checks the role variable, assumes the role through OIDC and verifies STS identity without deploying the API.

In the frontend repository, open **Actions → Deploy frontend → Run workflow**, select **main**, mode **verify-oidc**, and leave release_sha empty. This mode verifies federation. Do not select deploy for this check.

**📸 Capture both successful workflow runs and identity verification steps.** If the GitHub workflow differs from local source, inspect its contents before running. Workflow execution and frontend/backend federation remain unverified. Planner and lifecycle workflow execution also remain unverified at this milestone.

## 9. CI/CD verification workflow results

**Verify backend OIDC #2** on main, abbreviated commit `844ff5a`, reached **Success** in 26 seconds with a successful verify-oidc job. The inspected workflow checks configuration, assumes the role through OIDC and verifies STS identity. This Summary screenshot records the run result, without a separate identity log capture.

[Backend OIDC verification success](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-83-backend-oidc-success.png)

**Deploy frontend #4** on main, abbreviated commit `233bbff`, reached **Success** in 21 seconds. Build was skipped and publish succeeded, consistent with the instructed verify-oidc mode. This establishes federation verification and does not record a new frontend release.

[Frontend OIDC verification success](/aws_my_workshop/images/5-Workshop/5.5-Policy/5.5.7/deploy-84-frontend-oidc-success.png)

Both runs show an ubuntu-latest runner migration notice while retaining Success status. Frontend/backend federation checks passed according to workflow results. Actual backend release, frontend deployment and post-redeploy planner/lifecycle runs have not been performed. Remaining demo flows are deferred to video recording at the operator’s request.
