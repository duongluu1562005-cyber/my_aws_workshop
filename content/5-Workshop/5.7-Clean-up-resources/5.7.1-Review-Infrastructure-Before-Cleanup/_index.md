
# REVIEW INFRASTRUCTURE BEFORE CLEANUP

## Record the existing stacks

1. Open **CloudFormation → Stacks** in `us-east-1`, select **Active**, and filter for `tindy`.
2. Confirm all six stacks exist. Foundation, Integrations, Runtime and Edge initially report `UPDATE_COMPLETE`. Network and Database report `CREATE_COMPLETE`. Finish active updates before proceeding.

**📸 Capture the six stack names and status.**



[Initial Tindy stacks](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.1/cleanup-01-stacks-before.png)

3. Open **tindy-edge → Outputs**, record `DistributionId`, `DistributionDomainName` and `EdgeMode`, initially `live`.

[Initial Edge outputs](/aws_my_workshop/images/5-Workshop/5.7-Cleanup/5.7.1/cleanup-02-edge-before.png)
