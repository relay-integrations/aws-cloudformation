# aws-cloudformation-step-stack-deploy

This AWS CloudFormation deployer step container creates or updates a CloudFormation stack using a provided template.

## Specifications

| Setting | Child setting | Data type | Description | Default | Required |
|-----------|------------------|-----------|-------------|---------|----------|
| `aws` || mapping | A mapping of AWS account configuration. | None | True |
|| `accessKeyID` | string | An access key ID for the AWS account. You can pass the ID into Nebula as a secret. See the example below. | None | True |
|| `secretAccessKey` | string | The secret access key corresponding to the access key ID. Pass the key into Nebula as a secret. See the example below.| None | True |
|| `region` | string | The AWS region to use (for example, `us-west-2`). | None | True |
| `stackName` || string | The name of the stack to create or update. | None | True |
| `template` || string | The body of the CloudFormation template as a string in YAML or JSON. One of `template` or `templateFile` must be specified. | None | If `templateFile` is not present |
| `templateFile` || string | The relative path, within the Git repository given in the `git` parameters, to the template file to deploy. One of `template` or `templateFile` must be specified. | None | If `template` is not present |
| `git` || mapping | A map of git configuration. If you're using HTTPS, only `name` and `repository` are required. | None | True if `templateFile` is present |
|| `ssh_key` | string | The SSH key to use when cloning the git repository. You can pass the key into Nebula as a secret. See the example below. | None | True for SSH|
|| `known_hosts` | string | SSH known hosts file. Pass the contents of the file into Nebula as a secret. See the example below. | None | True for SSH |
|| `name` | string | A directory name for the git clone. | None | True |
|| `branch` | string | The Git branch to clone. | `master` | False |
|| `repository` | string | The git repository URL. | None | True |
| `parameters` || mapping | A key-value mapping of CloudFormation parameters to pass to the template. For more information, see [AWS CloudFormation parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html). | None | False |
| `s3` || mapping | An S3 bucket mapping for your s3 bucket and template | None | For large templates |
|| `bucket` | string | An S3 bucket to upload the template to. Required for templates larger than 51,200 bytes. | None | For large templates |
|| `prefix` | string | A folder name to prefix the artifacts' file names with when it uploads them to the S3 bucket. | None | False |
| `capabilities` || sequence of strings | A list of capabilities to use for the deployment, such as `CAPABILITY_NAMED_IAM`. | None | False |
| `tags` || mapping | A key-value mapping of tags to add to the deployment. | None | False |

> **Note**: The value you set for a secret must be a string. If you have multiple key-value pairs to pass into the secret, or your secret is the contents of a file, you must encode the values using base64 encoding, and use the encoded string as the secret value. 


## Examples

Here is an example of the step in a Nebula workflow:

```YAML
steps:

...

- name: cloudformation-deployer
  image: relaysh/aws-cloudformation-step-stack-deploy
  spec:
    aws:
      accessKeyID:
        $type: Secret
        name: key-id 
      secretAccessKey:
        $type: Secret
        name: access-key
      region: us-west-2  
    stackName: my-stack 
    templateFile: templates/template.yaml
    git: 
      ssh_key:
        $type: Secret
        name: ssh_key
      known_hosts:
        $type: Secret
        name: known_hosts
      name: my-git-repo
      branch: dev
      repository: path/to/your/repo
    parameters: 
      Environment: production
      CertificateARN: arn:aws:acm:us-east-1:123456789012:certificate/12345678-1234-1234-1234-123456789012
    s3:
      bucket: my-bucket
      prefix: app   
    capabilities:
      - CAPABILITY_NAMED_IAM
    tags:
      lifetime: 4d
```