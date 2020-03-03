---
subcategory: "Step Function (SFN)"
layout: "aws"
page_title: "AWS: aws_sfn_state_machine"
description: |-
  Provides a Step Function State Machine resource.
---

# Resource: aws_sfn_state_machine

Provides a Step Function State Machine resource

## Example Usage

```hcl
# ...

resource "aws_sfn_state_machine" "sfn_state_machine" {
  name     = "my-state-machine"
  role_arn = "${aws_iam_role.iam_for_sfn.arn}"

  definition = <<EOF
{
  "Comment": "A Hello World example of the Amazon States Language using an AWS Lambda Function",
  "StartAt": "HelloWorld",
  "States": {
    "HelloWorld": {
      "Type": "Task",
      "Resource": "${aws_lambda_function.lambda.arn}",
      "End": true
    }
  }
}
EOF
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the state machine.
* `definition` - (Required) The Amazon States Language definition of the state machine.
* `role_arn` - (Required) The Amazon Resource Name (ARN) of the IAM role to use for this state machine.
* `logging_configuration` - (Optional) Defines what execution history events are logged and where they are logged. The fields are documented below.
* `tags` - (Optional) Key-value mapping of resource tags

### Logging configuration

For **logging_configuration** the following arguments are supported:

* `destination` - (Required) The Amazon Resource Name (ARN) of the the CloudWatch log group to which you want your logs emitted to. The ARN must end with `:*`.
* `include_execution_data` - (Optional) Determines whether execution data is included in your log. When set to `false`, data is excluded.
* `level` - (Required) Defines which category of execution history events are logged. Possible values include: `ALL`, `FATAL`, `ERROR` and `OFF`.

Example with logging configuration:

```hcl
resource "aws_cloudwatch_log_group" "cloudwatch_log_group" {
  name = "example"
}

resource "aws_sfn_state_machine" "sfn_state_machine" {
  name     = "my-state-machine"
  role_arn = "${aws_iam_role.iam_for_sfn.arn}"

  logging_configuration {
    include_execution_data = true
    level                  = "FATAL"
    destination            = "${aws_cloudwatch_log_group.cloudwatch_log_group.arn}"
  }

  definition = <<EOF
{
  "Comment": "A Hello World example of the Amazon States Language using an AWS Lambda Function",
  "StartAt": "HelloWorld",
  "States": {
    "HelloWorld": {
      "Type": "Task",
      "Resource": "${aws_lambda_function.lambda.arn}",
      "End": true
    }
  }
}
EOF
}
```


## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - The ARN of the state machine.
* `creation_date` - The date the state machine was created.
* `status` - The current status of the state machine. Either "ACTIVE" or "DELETING".

## Import

State Machines can be imported using the `arn`, e.g.

```
$ terraform import aws_sfn_state_machine.foo arn:aws:states:eu-west-1:123456789098:stateMachine:bar
```
