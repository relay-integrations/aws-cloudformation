# aws-cloudformation-step-stack-deploy

This AWS CloudFormation deployer step container creates or updates a CloudFormation stack using a provided template.

* See [step.yaml](step.yaml) for usage examples
* The formal schema for inputs is in [spec.schema.json](spec.schema.json)
* The outputs will be a context-dependent set of key-value pairs based on the composition of the stack, as described by `aws cloudformation --describe-stacks`.