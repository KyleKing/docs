---
title: "Resource-based policies for Secrets Manager secrets"
hide_title: true
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import VersionBadge from '../../../../../src/components/VersionBadge.tsx';
import { HclListItem, HclListItemDescription, HclListItemTypeDetails, HclListItemDefaultValue, HclGeneralListItem } from '../../../../../src/components/HclListItem.tsx';

<a href="https://github.com/gruntwork-io/terraform-aws-security/tree/main/modules/secrets-manager-resource-policies" className="link-button" title="View the source code for this module in GitHub.">View Source</a>

<a href="https://github.com/gruntwork-io/terraform-aws-security/releases?q=" className="link-button" title="Release notes for only the service catalog versions which impacted this service.">Release Notes</a>

# Resource-based policies for Secrets Manager secrets

This module manages [resource-based policies on AWS Secrets Manager secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_resource-based-policies.html).

The module accepts a `secret_policies` variable containing a map of objects containing the ARN and a few options for customizing the resource-based policy. These options include:

1.  To provide read only access to the secret value (e.g. the `secretsmanager:GetSecretValue` IAM action), use the `iam_entities_with_read_access` attribute.
2.  To provide full access to the secret value (e.g. `secretsmanager:*` IAM actions), use the `iam_entities_with_full_access` attribute.
3.  If neither of these canned options are desired, use the `policy_statement_json` attribute to provide a completely custom policy. The value should be a JSON string generated by the [`aws_iam_policy_document` data source](https://www.terraform.io/docs/providers/aws/d/iam_policy_document.html).

Note that `iam_entities_with_read_access` and `iam_entities_with_full_access` are convenient shortcuts. They are mutually exclusive with `policy_statement_json`, which gives you full control over the policy. If the latter is provided, the former will be ignored.

Note also that you should only manage the policy for any given secret one time. That is, do not pass the same `arn` value more than once. Doing so will result in an non-deterministic policy on that secret.




## Reference

<Tabs>
<TabItem value="inputs" label="Inputs" default>

### Required

<HclListItem name="secret_policies" requirement="required" type="map(object(…))">
<HclListItemTypeDetails>

```hcl
map(object({
    # Secret manager secret ARN for which the policy applies to.
    arn = string

    # NOTE: Either (`iam_entities_with_read_access` and/or `iam_entities_with_write_access`) OR `policy_statement_json`
    # may be provided, but not both.

    # List of IAM entity ARNs (account, user, or role) that should have read access to the secret
    iam_entities_with_read_access = list(string)

    # List of IAM entity ARNs (account, user, or role) that should have full access ("*") to the secret
    iam_entities_with_full_access = list(string)

    # JSON string providing an IAM policy statement. This should be constructed using the aws_iam_policy_document data source.
    policy_statement_json = string
  }))
```

</HclListItemTypeDetails>
<HclGeneralListItem title="More Details">
<details>


```hcl

     List of IAM entity ARNs (account, user, or role) that should have read access to the secret

```
</details>

<details>


```hcl

     List of IAM entity ARNs (account, user, or role) that should have full access ("*") to the secret

```
</details>

<details>


```hcl

     JSON string providing an IAM policy statement. This should be constructed using the aws_iam_policy_document data source.

```
</details>

</HclGeneralListItem>
</HclListItem>

</TabItem>
<TabItem value="outputs" label="Outputs">



</TabItem>
</Tabs>


<!-- ##DOCS-SOURCER-START
{
  "originalSources": [
    "https://github.com/gruntwork-io/terraform-aws-security/tree/modules/secrets-manager-resource-policies/readme.md",
    "https://github.com/gruntwork-io/terraform-aws-security/tree/modules/secrets-manager-resource-policies/variables.tf",
    "https://github.com/gruntwork-io/terraform-aws-security/tree/modules/secrets-manager-resource-policies/outputs.tf"
  ],
  "sourcePlugin": "module-catalog-api",
  "hash": "ed6c130cee39c0eda824c7c0b65bcb16"
}
##DOCS-SOURCER-END -->