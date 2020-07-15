# Amazon Web Services (AWS)

The following is a brief series of steps that should help get people up and
running with AWS. 

## Administration

As useful as AWS is, the developer community considers it a necessary evil.
Rightfully so, as using it can be painstaking and full of errors before the 
first sign of success. Major aspects of using AWS rely on setup and
configuration: creating a root account, IAM users and groups, security
credentials, and so on. Once these are set, and with well-defined naming
conventions, the administrative evil can be somewhat bypassed with future use.

### IAM Users & Groups

Use IAM Users & Groups to provision permissions and policies. Otherwise, root
access could come in harm's way.

### Roles

Aside from IAM, roles are going to be necessary to attach to services such as
AWS Lambda. In particular, these roles will also have attached policies, which
will allow them to interact with various parts of the AWS ecosystem.

In order for AWS Lambda to use encrypted environment variables with AWS Key
Management Service (KMS), Lambda functions will require roles with policies
that are VPC-enabled. For instance, `AWSLambdaVPCAccessExecutionRole` is one
such policy. Otherwise, any function call with decryption will timeout and
throw an error.

## Command Line Interface (CLI)

### Installation

- [MacOS](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-mac.html)

### Configuration

- [Basic](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)

Note the use of a _named profile_ for credentials associated directly with
a particular project or group (i.e., the lab). This configuration is made via

```
aws configure --profile profilename
```

where _profilename_ is replaced with the intended name of that user profile for
AWS access configuration.

You can both check for existence and view for certainty the configuration and
credentials (do NOT make changes to these files directly):

#### Check configuration

```
open ~/.aws/config
```

#### Check credentials

```
open ~/.aws/credentials
```

## Amazon S3

### Introduction

- [What is Amazon S3?](https://docs.aws.amazon.com/AmazonS3/latest/dev/Welcome.html)
- [Getting Started](https://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html)

### General Usage with AWS CLI

- [Using high-level (s3) commands with the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html)
- [Using API-Level (s3api) commands with the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-apicommands.html)

Creating your first bucket from the command line, assuming proper permissions,
should be straightforward with `aws s3api`:

```
aws s3api create-bucket --bucket mytest-bucket
```

where _mytest-bucket_ is the name of the bucket you'd like to create. This is
made at the default location of your AWS profile configuration. If you'd like
to specify a location, include `LocationConstraint=location` as an option on
the command. The _location_ can be one of these 
[regions under the 'Code' column](https://docs.aws.amazon.com/general/latest/gr/rande.html#regional-endpoints). A succesful response of this command to create buckets will look like

```
{
    "Location": "/mytest-bucket"
}
```

### Upload Local Files to S3

To upload a local file, such as a ZIP file that is too large to directly send
to AWS Lambda (>50 MB), run

```
aws s3 cp mytest-function.zip s3://mytest-bucket
```

where *mytest-function.zip* is the compressed file and *mytest-bucket* is an
already existing S3 bucket. Before running this, you might include the option
`--dryrun` to be sure it works as intended.

## AWS Lambda

### Introduction

- [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [Getting Started](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html)

### General Usage with AWS CLI

**Important**: In order to use AWS Lambda with package dependencies, such as
the `tweepy` package in Python, the command line interface `aws` must be installed on your
local machine. This is non-negotiable both for security purposes and for
package deployment. If you have not configured your CLI, please scroll back up
and do so before continuing on this section.

- [Using AWS Lambda with the AWS CLI](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-awscli.html)
- [Using AWS Lambda with with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3-example.html)

### Python

- [Building Lambda functions with Python](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html)

### Building Dependencies

Nothing comes easy with AWS, and using Python packages is no exception to this
rule. To install `pandas` and `numpy`, an AWS Linux compatible version of each
must be compiled. A great tutorial on doing so with Docker is [found here](https://blog.alloy.co/deploying-aws-lambda-layers-with-pandas-for-data-science-38fe37a44a81)
and references these useful [public Docker images](https://github.com/lambci/docker-lambda).