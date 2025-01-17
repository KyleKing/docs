# Controls

Pipelines takes a defense in depth approach to securing workflows. This document provides an overview of the controls that Pipelines employs to ensure that only infrastructure that has been written in code and approved by a reviewer can be deployed in your AWS accounts.

## Dual-repository approach

Pipelines dual-repository approach separates infrastructure definitions from infrastructure deployment mechanisms. Pipelines requires two repositories —`infrastructure-pipelines`, where deployment workflows are defined and `infrastructure-live`, where infrastructure is defined as code. Each repository should have branch protection rules to prevent un-reviewed code from being deployed. Refer to [Recommended Settings](branch-protection#recommended-settings) in [Branch Protection](branch-protection) to learn more.

To control access to these repositories we recommend creating GitHub teams. Write access to the `infrastructure-pipelines` repository should be limited to individuals that already have administrative access to your AWS accounts (see [accessing AWS resources](#accessing-aws-resources)). Read and write access to the `infrastructure-live` repository should be granted to any individual who needs to define infrastructure as code. See [repository access](repository-access.md) for more details.

Pipelines uses GitHub Actions in both repositories to define workflows. Workflows running in `infrastructure-pipelines` deploy infrastructure changes, workflows running in `infrastructure-live` determine what infrastructure needs to be deployed and dispatch deployment jobs to the `infrastructure-pipelines` repository.

Workflows in `infrastructure-live` call workflows that are defined on the `main` branch of `infrastructure-pipelines`. This means that any change to the workflow must go through normal pull request approval processes. Further, the IAM role that Pipelines uses has a [trust policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) that specifies it can only be assumed from workflows running on the `main` branch. If workflows are run from another branch, retrieving short lived tokens will fail.

## Token strategy

Gruntwork Pipelines uses a series of GitHub Personal Access Tokens (PAT) to allow cross repository code and workflow access. This approach ensures that each token has the minimal required permissions to perform it's tasks. The full list of tokens and required permissions are listed below.

- `GRUNTWORK_CODE_ACCESS_TOKEN` - A [classic PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#personal-access-tokens-classic) with repository access to your `infrastructure-live` and `infrastructure-modules` repositories, as well as Gruntwork Library modules.
- `INFRA_LIVE_ACCESS_TOKEN` - A [fine-grained PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) that has read and write access to your `infrastructure-live` repository. This token is used in Pipelines to create pull requests after code generation and add pull request comments.
- `PIPELINES_DISPATCH_TOKEN` - A fine-grained PAT that can run Workflows in your `infrastructure-pipelines` repository.

Steps to create a PAT can be found in the official documentation. Refer to [creating a personal access token classic](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic) and [creating a fine-grained personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token), respectively. We recommend using [machine users](../security/machine-users.md) for this use case.

To learn more about GitHub PATs, refer to their documentation on [managing personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).

## Guards

Pipelines employs guards to prevent unapproved code from being deployed to or destroyed in your AWS Accounts. As with other aspects of pipelines, this guard is performed in two places. First, Pipelines jobs are automatically generated by branch. After dispatch, Pipelines performs additional checks to ensure that the information in the request matches code that exists on the `main` branch.

Pipelines has guards in place for `apply` and `destroy` actions. In order for these actions to run, the newly created or deleted code must be present (or not present) on main.

## AWS Credentials

Pipelines requires the use of an IAM Role configured with a trust policy to allow GitHub Actions to use OpenId Connect (OIDC) with to run actions in your AWS accounts. This prevents the requirement of creating long lived AWS credentials and storing them as secrets in GitHub Actions. At execution time, Pipelines exchanges the GitHub OIDC token for short-lived AWS credentials generated using AWS STS to run actions in your AWS accounts.

These credentials, although temporary, should still be treated as secrets. The Pipelines role has administrative permissions to many AWS resources to allow you to deploy many resources without needing to update the policy.

### OpenID Connect

Pipelines provisions an OpenId Connect identity provider in AWS IAM, setting up GitHub as the provider and setting the audience to AWS STS and your GitHub organization. To learn more about provisioning OpenId Connect providers in AWS, refer to the [official documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html).

The Pipelines IAM role uses a trust policy to limit access to assume the role to a single repository and branch in your GitHub Organization. The OpenID Connect provider is set as the principal, then a condition is added to the limit access to the `infrastructure-pipelines` repository in your GitHub organization, to the ref `main`. An example of the trust policy can be found below.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::0123456789012:oidc-provider/token.actions.githubusercontent.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringEquals": {
                    "token.actions.githubusercontent.com:sub": "repo:acme-co/infrastructure-pipelines:ref:refs/heads/main"
                }
            }
        }
    ]
}
```

Refer to [Configuring OpenId Connect in Amazon Web Services](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) to learn more.

### Accessing AWS resources

Access to AWS is only available to workflows running on `main` in the `infrastructure-pipelines` repository. The `infrastructure-live` repository does not have access to AWS directly. Pipelines runs workflows in `infrastructure-live` that call workflows in `infrastructure-pipelines` where the workflow runs on `main`, which grants the workflow in `infrastructure-pipelines` temporary access to run `terragrunt plan`, `terragrunt apply`, or `terragrunt destroy` actions.

As highlighted in [dual-repository approach](#dual-repository-approach), because the workflows in `infrastructure-pipelines` can get temporary access to access resources in AWS, write access to this repository should be limited to a trusted set of administrators who likely already have administrative privileges in AWS.


<!-- ##DOCS-SOURCER-START
{
  "sourcePlugin": "local-copier",
  "hash": "6eebd747e10e5e7c5ca53cd839a6433c"
}
##DOCS-SOURCER-END -->
