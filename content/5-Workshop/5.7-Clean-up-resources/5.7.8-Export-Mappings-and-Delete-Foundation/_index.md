
# EXPORT MAPPINGS AND DELETE FOUNDATION

## Export Foundation configuration before deletion

After Network deletion, AWS CLI exports were saved to `backup/tindy-foundation-before-cleanup` in the Worklog repository. Files include `resources.json`, `stack-metadata.json`, `deployed-template.json` and `retained-resource-mapping.csv`. These operational import records reside outside the website static directory.



Foundation has 13 resources and termination protection enabled. Its deployed template includes nine resources using `RetainExceptOnCreate`, covering two S3 buckets, the media bucket policy, ECR repository, four application secrets and GitHub OIDC provider. The remaining four deployment/operations IAM roles will be deleted under the template. The saved mapping contains logical IDs, types and physical IDs for the nine retained resources.

1. Open **CloudFormation → Stacks → Active → tindy-foundation → Resources** and compare its 13 resources with the exported mapping.

**📸 Capture both parts of Resources.**



[Foundation resources before deletion](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-28-foundation-resources-before.png)



[Foundation resources lower section](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-28a-foundation-resources-bottom.png)

2. Open **Outputs** and record the 17 outputs covering ECR, S3, secret, OIDC and IAM role references plus the Data Protection path. `DataProtectionParameterPath` identifies the SSM path used by the API. Keys created by the API at runtime require management separately from stack resources.

**📸 Capture both parts of Outputs.**



[Foundation outputs before deletion](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-29-foundation-outputs-before.png)



[Foundation outputs lower section](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-29a-foundation-outputs-bottom.png)

3. Select **Stack actions → Edit termination protection**, confirm `tindy-foundation`, choose **Deactivated** and save.

**📸 Capture Deactivated before saving.**



[Disable Foundation termination protection](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-30-foundation-disable-termination-protection.png)

4. Select **Delete stack**, enter `tindy-foundation` and leave Express mode unchecked.

**📸 Capture the deletion confirmation.**



[Foundation deletion confirmation](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-31-foundation-delete-confirm.png)

5. Confirm deletion and monitor Events. If the stack disappears from Active, select **Deleted** and locate Foundation.
6. Verify `tindy-foundation — DELETE_COMPLETE`. `DELETE_SKIPPED` indicates resources retained under the template policy. The screenshot shows the OIDC provider, ECR repository and frontend bucket retained, while the displayed roles were deleted.

**📸 Capture completed stack deletion and DELETE_SKIPPED resources.**



[Foundation deleted with retained resources](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.8/cleanup-32-foundation-deleted.png)



All six stacks in this session have been deleted successfully. Use the saved mapping to import retained Foundation resources into the new stack. Verify their existence and configuration before import.
