---
title: "Infrastructure Pipeline: ECS Deploy Runner"
hide_title: true
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import VersionBadge from '../../../../../src/components/VersionBadge.tsx';
import { HclListItem, HclListItemDescription, HclListItemTypeDetails, HclListItemDefaultValue, HclGeneralListItem } from '../../../../../src/components/HclListItem.tsx';

<a href="https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner" className="link-button" title="View the source code for this module in GitHub.">View Source</a>

<a href="https://github.com/gruntwork-io/terraform-aws-ci/releases?q=" className="link-button" title="Release notes for only the service catalog versions which impacted this service.">Release Notes</a>

# Infrastructure Pipeline: ECS Deploy Runner

This module can be used to set up a secure CI/CD pipeline for your infrastructure code ([Terraform](https://www.terraform.io), [Terragrunt](https://terragrunt.gruntwork.io), [Packer](https://www.packer.io/), [Docker](https://www.docker.com/), etc). You can use this in combination with existing CI servers (e.g Jenkins, CircleCI, Gitlab) to set up workflows that:

*   Run `plan` and `apply` with approval stages

*   Build docker images for every PR

*   Build VM images (e.g., AMIs) for every PR

*   Automatically update infrastructure code and commit and push the changes to Git

These workflows can be implemented without directly running the steps from your CI servers. Instead, the CI server can coordinate the CI / CD flow, and for anything that requires sensitive / powerful IAM permissions, it can trigger pre-defined, locked-down jobs in an isolated ECS task, and stream the logs from that task as if it’s running locally.

Refer to the [infrastructure-deployer CLI](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/infrastructure-deployer) to integrate this with existing CI servers. You can also refer to the [infrastructure-deploy-script module](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/infrastructure-deploy-script) for more information on the underlying deployment scripts.

![Terraform and Terragrunt CI/CD architecture](/img/reference/modules/terraform-aws-ci/ecs-deploy-runner/tftg-pipeline-architecture.png)

## Features

*   Set up an automated CI / CD pipeline for your infrastructure code (Terraform / Terragrunt / Packer / Docker)

*   Supports approval work flows: see plan first and approve it in your CI / CD system before running apply

*   Build AMIs and Docker images

*   Isolated runtime environment in AWS using AWS Lambda and ECS Fargate/EC2

*   Deploy any reference (commit sha, tag, or branch) from any git repository

## Learn

Note

This repo is a part of [the Gruntwork Infrastructure as Code Library](https://gruntwork.io/infrastructure-as-code-library/), a collection of reusable, battle-tested, production ready infrastructure code. If you’ve never used the Infrastructure as Code Library before, make sure to read [How to use the Gruntwork Infrastructure as Code Library](https://gruntwork.io/guides/foundations/how-to-use-gruntwork-infrastructure-as-code-library/)!

### Core concepts

*   [Overview](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#overview): An overview of the architecture deployed by in this module, including how to implement a CI/CD pipeline for infrastructure code.

*   [Threat model of the deploy runner](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#threat-model-of-the-deploy-runner): An overview of the threat model used to design the security features of the solution, including a description of the potential attack vectors that are mitigated by the solution, and those attacks that require policy and behavioral changes to mitigate.

### Repo organization

*   [modules](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules): the main implementation code for this repo, broken down into multiple standalone, orthogonal submodules.

*   [examples](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/examples): This folder contains working examples of how to use the submodules.

*   [test](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/test): Automated tests for the modules and examples.

## Deploy

### Non-production deployment (quick start for learning)

If you just want to try this repo out for experimenting and learning, check out the following resources:

*   [examples folder](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/examples): The `examples` folder contains sample code optimized for learning, experimenting, and testing (but not production usage).

## Manage

*   [What configuration is recommended for container_images?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#what-configuration-is-recommended-for-container_images)

*   [How do I restrict what args can be passed into the scripts?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#how-do-i-restrict-what-args-can-be-passed-into-the-scripts)

*   [How do I trigger a deployment?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#how-do-i-trigger-a-deployment)

*   [How do I trigger a deployment from CI?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#how-do-i-trigger-a-deployment-from-ci)

*   [How do I customize the deployment task runtime environment?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#what-container-is-used-for-the-deploy-task)

*   [How do I use the deployment task container with a private VCS system such as GitHub Enterprise?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#how-do-i-use-the-ecs-deploy-runner-with-a-private-vcs-system-such-as-github-enterprise)

*   [How do I stream logs from the deployment task?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#how-do-i-stream-logs-from-the-deployment-task)

*   [How do I access the stdout and stderr output from the underlying scripts?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#how-do-i-access-the-stdout-and-stderr-output-from-the-underlying-scripts)

*   [What are the IAM permissions necessary to trigger a deployment?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/ecs-deploy-runner/core-concepts.md#what-are-the-iam-permissions-necessary-to-trigger-a-deployment)

*   [How do I see the list of supported containers?](https://github.com/gruntwork-io/terraform-aws-ci/tree/main/modules/infrastructure-deployer/core-concepts.md#how-do-i-see-the-list-of-supported-containers)




## Reference

<Tabs>
<TabItem value="inputs" label="Inputs" default>

### Required

<HclListItem name="container_images" requirement="required" type="map(object(…))">
<HclListItemDescription>

Map of names to docker image (repo and tag) to use for the ECS task. Each entry corresponds to a different ECS task definition that can be used for infrastructure pipelines. The key corresponds to a user defined name that can be used with the invoker function to determine which task definition to use.

</HclListItemDescription>
<HclListItemTypeDetails>

```hcl
map(object({
    # Docker container identifiers
    docker_image = string
    docker_tag   = string

    # Map of environment variable names to secret manager arns of secrets to share with the container during runtime.
    # Note that the container processes will not be able to directly read these secrets directly using the Secrets
    # Manager API (they are only available implicitly through the env vars).
    secrets_manager_env_vars = map(string)

    # Map of environment variable names to values share with the container during runtime.
    # Do NOT use this for sensitive variables! Use secrets_manager_env_vars for secrets.
    environment_vars = map(string)

    # List of additional secrets manager entries that the container should have access to, but not directly injected as
    # environment variables. These secrets can be read by the container processes using the Secrets Manager API, unlike
    # those shared as environment variables with `secrets_manager_env_vars`.
    additional_secrets_manager_arns = list(string)

    # Security configuration for each script for each container. Each entry in the map corresponds to a script in the
    # triggers directory. If a script is not included in the map, then the default is to allow no additional args to be
    # passed in when invoked.
    script_config = map(object({
      # Which options and args to always pass in alongside the ones provided by the command.
      # This is a map of option keys to args to pass in. Each arg in the list will be passed in as a separate option.
      # E.g., if you had {'foo': ['bar', 'baz', 'car']}, then this will be: `--foo bar --foo baz --foo car`
      # This will be passed in first, before the args provided by the user in the event data.
      hardcoded_options = map(list(string))

      # Unlike hardcoded_options, this is used for hardcoded positional args and will always be passed in at the end of
      # the args list.
      hardcoded_args = list(string)

      # Whether or not positional args are allowed to be passed in.
      allow_positional_args = bool

      # This is a list of option keys that are explicitly allowed. When set, any option key that is not present in this
      # list will cause an exception. Note that `null` means allow any option, as opposed to `[]` which means allow no
      # option. Only one of `allowed_options` or `restricted_options` should be used.
      allowed_options = list(string)

      # List of options without arguments
      allowed_options_without_args = list(string)

      # This is a list of option keys that are not allowed. When set, any option key passed in that is in this list will
      # cause an exception. Empty list means allow any option (unless restricted by allowed_options).
      restricted_options = list(string)

      # This is a map of option keys to a regex for specifying what args are allowed to be passed in for that option key.
      # There is no restriction to the option if there is no entry in this map.
      restricted_options_regex = map(string)
    }))

    # Whether or not the particular container is the default container for the pipeline. This container is used when no
    # name is provided to the infrastructure deployer. Exactly one must be marked as the default. An arbitrary container
    # will be picked amongst the list of defaults when multiple are marked as default.
    # If no containers are marked as default, then the invoker lambda function always requires a container name to be
    # provided.
    default = bool
  }))
```

</HclListItemTypeDetails>
<HclGeneralListItem title="More Details">
<details>


```hcl

     Map of environment variable names to secret manager arns of secrets to share with the container during runtime.
     Note that the container processes will not be able to directly read these secrets directly using the Secrets
     Manager API (they are only available implicitly through the env vars).

```
</details>

<details>


```hcl

     Map of environment variable names to values share with the container during runtime.
     Do NOT use this for sensitive variables! Use secrets_manager_env_vars for secrets.

```
</details>

<details>


```hcl

     List of additional secrets manager entries that the container should have access to, but not directly injected as
     environment variables. These secrets can be read by the container processes using the Secrets Manager API, unlike
     those shared as environment variables with `secrets_manager_env_vars`.

```
</details>

<details>


```hcl

     Security configuration for each script for each container. Each entry in the map corresponds to a script in the
     triggers directory. If a script is not included in the map, then the default is to allow no additional args to be
     passed in when invoked.

```
</details>

<details>


```hcl

       Unlike hardcoded_options, this is used for hardcoded positional args and will always be passed in at the end of
       the args list.

```
</details>

<details>


```hcl

       Whether or not positional args are allowed to be passed in.

```
</details>

<details>


```hcl

       This is a list of option keys that are explicitly allowed. When set, any option key that is not present in this
       list will cause an exception. Note that `null` means allow any option, as opposed to `[]` which means allow no
       option. Only one of `allowed_options` or `restricted_options` should be used.

```
</details>

<details>


```hcl

       List of options without arguments

```
</details>

<details>


```hcl

       This is a list of option keys that are not allowed. When set, any option key passed in that is in this list will
       cause an exception. Empty list means allow any option (unless restricted by allowed_options).

```
</details>

<details>


```hcl

       This is a map of option keys to a regex for specifying what args are allowed to be passed in for that option key.
       There is no restriction to the option if there is no entry in this map.

```
</details>

<details>


```hcl

     Whether or not the particular container is the default container for the pipeline. This container is used when no
     name is provided to the infrastructure deployer. Exactly one must be marked as the default. An arbitrary container
     will be picked amongst the list of defaults when multiple are marked as default.
     If no containers are marked as default, then the invoker lambda function always requires a container name to be
     provided.

```
</details>

</HclGeneralListItem>
</HclListItem>

<HclListItem name="vpc_id" requirement="required" type="string">
<HclListItemDescription>

AWS ID of the VPC where the ECS task and invoker lambda should run.

</HclListItemDescription>
</HclListItem>

<HclListItem name="vpc_subnet_ids" requirement="required" type="list(string)">
<HclListItemDescription>

List of VPC Subnet IDs where the ECS task and invoker lambda should run.

</HclListItemDescription>
</HclListItem>

### Optional

<HclListItem name="artifact_config" requirement="optional" type="object(…)">
<HclListItemDescription>

Configuration for storing artifacts from the underlying commands. When set, stdout, stderr, and interleaved output will be stored in the configured S3 bucket. Set to null if you do not wish for artifacts to be stored. Note that when null, the args for configuring storage of outputs will not be available.

</HclListItemDescription>
<HclListItemTypeDetails>

```hcl
object({
    # Whether to allow user to selectively decide when artifacts should be stored in S3. When true,
    # users must provide `--store-outputs true` to the script args in the infrastructure-deployer call to store the
    # outputs. When false, every run will be hardcoded to `--store-outputs true`.
    allow_runtime_selection = bool

    # S3 bucket and region (us-east-1) where the outputs will be stored.
    bucket_name = string
    region      = string

    # Key prefix to use if lambda event does not specify. Outputs will be stored at PREFIX/stdout, PREFIX/stderr, and
    # PREFIX/interleaved. Note that this will overwrite the output even if the key already exists.
    default_key_prefix = string
  })
```

</HclListItemTypeDetails>
<HclListItemDefaultValue defaultValue="null"/>
<HclGeneralListItem title="More Details">
<details>


```hcl

     S3 bucket and region (us-east-1) where the outputs will be stored.

```
</details>

<details>


```hcl

     Key prefix to use if lambda event does not specify. Outputs will be stored at PREFIX/stdout, PREFIX/stderr, and
     PREFIX/interleaved. Note that this will overwrite the output even if the key already exists.

```
</details>

</HclGeneralListItem>
</HclListItem>

<HclListItem name="cloudwatch_log_group_kms_key_id" requirement="optional" type="string">
<HclListItemDescription>

The ID (ARN, alias ARN, AWS ID) of a customer managed KMS Key to use for encrypting log data.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_name" requirement="optional" type="string">
<HclListItemDescription>

A custom name to set for the CloudWatch Log Group used to stream the container logs. When null, the Log Group will default to <a href="#name"><code>name</code></a>.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_retention_in_days" requirement="optional" type="number">
<HclListItemDescription>

The number of days to retain log events in the log group. Refer to https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group#retention_in_days for all the valid values. When null, the log events are retained forever.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_subscription_destination_arn" requirement="optional" type="string">
<HclListItemDescription>

The ARN of the destination to deliver matching log events to. Kinesis stream or Lambda function ARN. Only applicable if <a href="#should_create_cloudwatch_log_group"><code>should_create_cloudwatch_log_group</code></a> is true, and <a href="#container_images"><code>container_images</code></a> is non-empty.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_subscription_distribution" requirement="optional" type="string">
<HclListItemDescription>

The method used to distribute log data to the destination. Only applicable when <a href="#cloudwatch_log_group_subscription_destination_arn"><code>cloudwatch_log_group_subscription_destination_arn</code></a> is a kinesis stream. Valid values are `Random` and `ByLogStream`.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_subscription_filter_pattern" requirement="optional" type="string">
<HclListItemDescription>

A valid CloudWatch Logs filter pattern for subscribing to a filtered stream of log events.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="&quot;&quot;"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_subscription_role_arn" requirement="optional" type="string">
<HclListItemDescription>

ARN of an IAM role that grants Amazon CloudWatch Logs permissions to deliver ingested log events to the destination. Only applicable when <a href="#cloudwatch_log_group_subscription_destination_arn"><code>cloudwatch_log_group_subscription_destination_arn</code></a> is a kinesis stream.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="cloudwatch_log_group_tags" requirement="optional" type="map(string)">
<HclListItemDescription>

Tags to apply on the CloudWatch Log Group, encoded as a map where the keys are tag keys and values are tag values.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="container_cpu" requirement="optional" type="number">
<HclListItemDescription>

The default CPU units for the instances that Fargate will spin up. The invoker allows users to override the CPU at run time, but this value will be used if the user provides no value for the CPU. Options here: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html#fargate-tasks-size.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="1024"/>
</HclListItem>

<HclListItem name="container_default_launch_type" requirement="optional" type="string">
<HclListItemDescription>

The default launch type of the ECS deploy runner workers. This launch type will be used if it is not overridden during invocation of the lambda function. Must be FARGATE or EC2.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="&quot;FARGATE&quot;"/>
</HclListItem>

<HclListItem name="container_max_cpu" requirement="optional" type="number">
<HclListItemDescription>

The maximum CPU units that is allowed to be specified by the user when invoking the deploy runner with the Lambda function.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="2048"/>
</HclListItem>

<HclListItem name="container_max_memory" requirement="optional" type="number">
<HclListItemDescription>

The maximum memory units that is allowed to be specified by the user when invoking the deploy runner with the Lambda function.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="8192"/>
</HclListItem>

<HclListItem name="container_memory" requirement="optional" type="number">
<HclListItemDescription>

The default memory units for the instances that Fargate will spin up. The invoker allows users to override the memory at run time, but this value will be used if the user provides no value for memory. Options here: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html#fargate-tasks-size.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="2048"/>
</HclListItem>

<HclListItem name="custom_tags" requirement="optional" type="map(string)">
<HclListItemDescription>

A map of custom tags to apply to all the resources created in this module. The key is the tag name and the value is the tag value.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="{}"/>
<HclGeneralListItem title="Examples">
<details>
  <summary>Example</summary>


```hcl
     {
       key1 = "value1"
       key2 = "value2"
     }

```
</details>

</HclGeneralListItem>
</HclListItem>

<HclListItem name="ec2_worker_pool_configuration" requirement="optional" type="object(…)">
<HclListItemDescription>

Worker configuration of a EC2 worker pool for the ECS cluster. If null, no EC2 worker pool will be allocated and the deploy runner will be in Fargate only mode.

</HclListItemDescription>
<HclListItemTypeDetails>

```hcl
object({
    # The minimum number of EC2 Instances launchable for this ECS Cluster. Useful for auto-scaling limits.
    min_size = number
    # The maximum number of EC2 Instances that must be running for this ECS Cluster. We recommend making this twice
    # min_size, even if you don't plan on scaling the cluster up and down, as the extra capacity will be used to
    # deploy updates to the cluster.
    max_size = number

    # AMI to use for launching the EC2 instances for the ECS cluster.
    ami = string

    # Instance type (e.g. t2.micro) to use for the EC2 instances. We recommend using at least large class instances.
    instance_type = string

    # The User Data script to run on each of the ECS Cluster's EC2 Instances on their first boot. Set to null if there
    # is no boot script to run. Use user_data for setting a single plain text script, and user_data_base64 if you want
    # to use cloud-init boot scripts.
    user_data        = string
    user_data_base64 = string
  })
```

</HclListItemTypeDetails>
<HclListItemDefaultValue defaultValue="null"/>
<HclGeneralListItem title="More Details">
<details>


```hcl

     AMI to use for launching the EC2 instances for the ECS cluster.

```
</details>

<details>


```hcl

     Instance type (e.g. t2.micro) to use for the EC2 instances. We recommend using at least large class instances.

```
</details>

<details>


```hcl

     The User Data script to run on each of the ECS Cluster's EC2 Instances on their first boot. Set to null if there
     is no boot script to run. Use user_data for setting a single plain text script, and user_data_base64 if you want
     to use cloud-init boot scripts.

```
</details>

</HclGeneralListItem>
</HclListItem>

<HclListItem name="ecs_task_exec_role_permissions_boundary" requirement="optional" type="string">
<HclListItemDescription>

The ARN of the policy that is used to set the permissions boundary for the ECS Task Execution IAM role. This policy should be created outside of this module.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="ecs_task_permissions_boundary" requirement="optional" type="string">
<HclListItemDescription>

The ARN of the policy that is used to set the permissions boundary for the ECS Task IAM role. This policy should be created outside of this module.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="invoker_lambda_cloudwatch_log_group_kms_key_id" requirement="optional" type="string">
<HclListItemDescription>

The ID (ARN, alias ARN, AWS ID) of a customer managed KMS Key to use for encrypting log data for the invoker lambda function.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="invoker_lambda_cloudwatch_log_group_retention_in_days" requirement="optional" type="number">
<HclListItemDescription>

The number of days to retain log events in the log group for the invoker lambda function. Refer to https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group#retention_in_days for all the valid values. When null, the log events are retained forever.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="invoker_lambda_cloudwatch_log_group_tags" requirement="optional" type="map(string)">
<HclListItemDescription>

Tags to apply on the CloudWatch Log Group for the invoker lambda function, encoded as a map where the keys are tag keys and values are tag values.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="invoker_lambda_loglevel" requirement="optional" type="string">
<HclListItemDescription>

Set the logging level of the invoker lambda function. Must be one of debug, info, warn, error.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="&quot;info&quot;"/>
</HclListItem>

<HclListItem name="invoker_lambda_reserved_concurrent_executions" requirement="optional" type="number">
<HclListItemDescription>

The amount of reserved concurrent executions for the invoker lambda function. Set to -1 to explicitly denote concurrent executions are unreserved.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="invoker_lambda_role_permissions_boundary" requirement="optional" type="string">
<HclListItemDescription>

The ARN of the policy that is used to set the permissions boundary for the invoker lambda function's execution role. This policy should be created outside of this module.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="invoker_lambda_should_create_cloudwatch_log_group" requirement="optional" type="bool">
<HclListItemDescription>

When true, precreate the CloudWatch Log Group to use for log aggregation from the invoker lambda function execution. This is useful if you wish to customize the CloudWatch Log Group with various settings such as retention periods and KMS encryption. When false, AWS Lambda will automatically create a basic log group to use.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="true"/>
</HclListItem>

<HclListItem name="name" requirement="optional" type="string">
<HclListItemDescription>

Name of this instance of the deploy runner stack. Used to namespace all resources.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="&quot;ecs-deploy-runner&quot;"/>
</HclListItem>

<HclListItem name="outbound_security_group_name" requirement="optional" type="string">
<HclListItemDescription>

When non-null, set the security group name of the ECS Deploy Runner ECS Task to this string. When null, a unique name will be generated by Terraform to avoid conflicts when deploying multiple instances of the ECS Deploy Runner.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="repository_credentials_secrets_manager_arn" requirement="optional" type="string">
<HclListItemDescription>

The ARN of a AWS Secrets Manager secret containing credentials to access the private repository. See the docs for details on the format of the secret: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/private-auth.html. Note that appropriate secrets manager permissions need to be added to the task execution role for this to work.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="secrets_manager_kms_key_arn" requirement="optional" type="string">
<HclListItemDescription>

ARN of the KMS Key used to encrypt the AWS Secrets Manager entries. Note that if this variable is provided, this module will grant read and decrypt access to the KMS key to the ECS task. Only required if a custom KMS key was used to encrypt the secrets manager entry.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="null"/>
</HclListItem>

<HclListItem name="should_create_cloudwatch_log_group" requirement="optional" type="bool">
<HclListItemDescription>

When true, precreate the CloudWatch Log Group to use for log aggregation from the ECS Task execution. This is useful if you wish to customize the CloudWatch Log Group with various settings such as retention periods and KMS encryption. When false, AWS ECS will automatically create a basic log group to use.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="true"/>
</HclListItem>

<HclListItem name="use_managed_iam_policies" requirement="optional" type="bool">
<HclListItemDescription>

When true, all IAM policies will be managed as dedicated policies rather than inline policies attached to the IAM roles. Dedicated managed policies are friendlier to automated policy checkers, which may scan a single resource for findings. As such, it is important to avoid inline policies when targeting compliance with various security standards.

</HclListItemDescription>
<HclListItemDefaultValue defaultValue="true"/>
</HclListItem>

</TabItem>
<TabItem value="outputs" label="Outputs">

<HclListItem name="cloudwatch_log_group_name">
<HclListItemDescription>

Name of the CloudWatch Log Group used to store the log output from the Deploy Runner ECS task.

</HclListItemDescription>
</HclListItem>

<HclListItem name="default_ecs_task_arn">
<HclListItemDescription>

AWS ARN of the default ECS Task Definition. Can be used to trigger the ECS Task directly.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_cluster_arn">
<HclListItemDescription>

AWS ARN of the ECS Cluster that can be used to run the deploy runner task.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_ec2_worker_asg_name">
<HclListItemDescription>

Name of the Autoscaling Group associated with the EC2 worker pool of the ECS Cluster that can be used to run the deploy runner task.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_ec2_worker_iam_role">
<HclListItemDescription>

AWS ARN and name of the IAM role associated with the EC2 worker pool of the ECS Cluster that can be used to run the deploy runner task.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_task_arns">
<HclListItemDescription>

Map of AWS ARNs of the ECS Task Definition. Each entry corresponds to an entry in the <a href="#container_images"><code>container_images</code></a> input map, with the keys aligned.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_task_execution_role_arn">
<HclListItemDescription>

ECS Task execution role ARN

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_task_families">
<HclListItemDescription>

Map of the families of the ECS Task Definition that is currently live. Each entry corresponds to an entry in the <a href="#container_images"><code>container_images</code></a> input map, with the keys aligned.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_task_iam_roles">
<HclListItemDescription>

Map of AWS ARNs and names of the IAM role that will be attached to the ECS task to grant it access to AWS resources. Each container will have its own IAM role, and each entry in this map corresponds to an entry in the <a href="#container_images"><code>container_images</code></a> input map, with the keys aligned.

</HclListItemDescription>
</HclListItem>

<HclListItem name="ecs_task_revisions">
<HclListItemDescription>

Map of the current revision of the ECS Task Definition that is live. Each entry corresponds to an entry in the <a href="#container_images"><code>container_images</code></a> input map, with the keys aligned.

</HclListItemDescription>
</HclListItem>

<HclListItem name="invoker_function_arn">
<HclListItemDescription>

AWS ARN of the invoker lambda function that can be used to invoke a deployment.

</HclListItemDescription>
</HclListItem>

<HclListItem name="invoker_function_name">
<HclListItemDescription>

Name of the invoker lambda function that can be used to invoke a deployment.

</HclListItemDescription>
</HclListItem>

<HclListItem name="security_group_allow_all_outbound_id">
<HclListItemDescription>

Security Group ID of the ECS task

</HclListItemDescription>
</HclListItem>

</TabItem>
</Tabs>


<!-- ##DOCS-SOURCER-START
{
  "originalSources": [
    "https://github.com/gruntwork-io/terraform-aws-ci/tree/modules/ecs-deploy-runner/readme.adoc",
    "https://github.com/gruntwork-io/terraform-aws-ci/tree/modules/ecs-deploy-runner/variables.tf",
    "https://github.com/gruntwork-io/terraform-aws-ci/tree/modules/ecs-deploy-runner/outputs.tf"
  ],
  "sourcePlugin": "module-catalog-api",
  "hash": "9446e32c2a13c6ae1d4580c8d7c4373e"
}
##DOCS-SOURCER-END -->