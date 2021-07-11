<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/qualys-cloud-agent/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# Qualys Cloud Agent configset

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

## Why?

Configsets are essentially configuration management. Use configsets to configure and update your EC2 instances automatically. Lono allows you to use configsets in a reusable way.

Configsets work with [AWS::CloudFormation::Init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html) and [cfn-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html). Configsets do not magically get applied after being added to the CloudFormation template though.  The `cfn-init` script must be called to apply the configset.

Usually the `cfn-init` script is called in the UserData script. This ensures configsets are applied when instances are launched.

Additionally, the [cfn-hup](https://github.com/boltopspro-docs/cfn-hup) script can be set up to apply configsets **continuously**.

This configset configures and runs the cfn-hup daemon. The `cfn-auto-reloader.conf` is configured to detect changes every 1 minute.  When the stack [Metatdata AWS::CloudFormation::Init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html) changes, cfn-hup detects this and updates the EC2 instance. This ensures that the EC2 instance will always be up-to-date.

The cfn-hup configset should usually be one of the first things to be setup.

## What are lono configsets?

Lono configsets allow CloudFormation [cfn-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html) [configsets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html) that are typically embedded in the template to be reusable.  More info: [Lono Configsets docs](https://lono.cloud/docs/configsets/).

## Usage

Use `configset` to enable the configset for the blueprint.  Example:

configs/demo/configsets/base.rb:

```ruby
configset("qualys-cloud-agent", resource: "Instance")
```

This adds the configset to the `resource` with the logical id `Instance` in your CloudFormation template.  The configset is added to the [Resources[].Metadata.AWS::CloudFormation::Init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html) attribute of the `Instance` resource.

Here's an example adding to a `LaunchConfig` resource:

```ruby
configset("qualys-cloud-agent", resource: "LaunchConfig")
```

Here's an example adding to a `LaunchTemplate` resource:

```ruby
configset("qualys-cloud-agent", resource: "LaunchTemplate")
```

## Requirements

- The agent only runs on x86 64 bit architectures
- The target EC2 instance must have S3 and SSM read access
- The following variables need to be set in within your project in #{stackname}/configsets/variables.rb:

```ruby
@customer_id_ssm_key = "/qualys/cloud-agent/CustomerId" # or your own custom key
@activation_id_ssm_key = "/qualys/cloud-agent/ActivationId" # or your own custom key
```

These are used to retrieve the corresponding secrets, which are used when starting the agent.


