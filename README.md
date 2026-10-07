[![CI](https://github.com/the-jodingo/aws-cloudformation/actions/workflows/ci.yml/badge.svg)](https://github.com/the-jodingo/aws-cloudformation/actions/workflows/ci.yml)
[![AWS CloudFormation](https://img.shields.io/badge/AWS-CloudFormation-FF9900?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/cloudformation/)
[![cfn-lint](https://img.shields.io/badge/linted%20with-cfn--lint-blue)](.github/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

# AWS CloudFormation Templates

A small set of validated CloudFormation templates for common EC2 and storage
patterns. Each is standalone and deployable as-is.

## Table of contents

- [Templates](#templates)
- [Requirements](#requirements)
- [Parameters](#parameters)
- [Deploy](#deploy)
- [Security](#security)
- [Notes](#notes)
- [CI](#ci)
- [License](#license)

## Templates

| Template | Creates |
|---|---|
| [`ec-2-template.yaml`](ec-2-template.yaml) | EC2 instance + security group (SSH) |
| [`Attach-an-EBS-volume.yaml`](Attach-an-EBS-volume.yaml) | EC2 instance + a 25 GB `gp3` EBS volume attached at `/dev/sdf` |
| [`S3Bucket.yml`](S3Bucket.yml) | EC2 instance + security group + an S3 bucket |

## Requirements

| Tool | Version |
|---|---|
| AWS CLI | v2 recommended |
| AWS credentials | with EC2, S3, and CloudFormation permissions |

## Parameters

All three templates accept the same two parameters:

| Parameter | Description | Default |
|---|---|---|
| `KeyName` | An existing EC2 KeyPair name, for SSH | *required* |
| `SSHLocation` | CIDR allowed to reach port 22 | `0.0.0.0/0` |

## Deploy

Always validate first:

```bash
aws cloudformation validate-template \
  --template-body file://ec-2-template.yaml
```

Then deploy, restricting SSH to your own address:

```bash
MY_IP=$(curl -s https://checkip.amazonaws.com)

aws cloudformation deploy \
  --template-file ec-2-template.yaml \
  --stack-name ec2-ssh-demo \
  --parameter-overrides KeyName=my-key SSHLocation=$MY_IP/32
```

Tear down:

```bash
aws cloudformation delete-stack --stack-name ec2-ssh-demo
```

## Security

`SSHLocation` defaults to `0.0.0.0/0` for convenience, which is **not safe for
anything real**. Always override it:

```bash
--parameter-overrides SSHLocation=$MY_IP/32
```

For production, prefer AWS Systems Manager Session Manager over opening port 22
at all.

## Notes

- The AMI ID (`ami-098e39bafa7e7303d`) is region-specific. Look up the current
  Amazon Linux 2 AMI for your region before deploying.
- `S3Bucket.yml` derives the bucket name from the stack name and account ID, so
  it stays globally unique without hardcoding a name.

## CI

GitHub Actions runs `cfn-lint` against every template on each push and PR:

```bash
pip install cfn-lint
cfn-lint "*.yaml" "*.yml" --ignore-checks W3002
```

## License

[MIT](LICENSE) © Joash Odingo
