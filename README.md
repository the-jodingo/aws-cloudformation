# AWS CloudFormation Templates

A small set of CloudFormation templates for common EC2 and storage patterns.
Each template is standalone and deployable as-is.

## Templates

| Template | What it creates |
|---|---|
| `ec-2-template.yaml` | EC2 instance + security group (SSH) |
| `Attach-an-EBS-volume.yaml` | EC2 instance + an additional 25 GB `gp3` EBS volume attached |
| `S3Bucket.yml` | EC2 instance + security group + an S3 bucket |

## Parameters

Every template takes the same two parameters:

| Parameter | Description | Default |
|---|---|---|
| `KeyName` | An existing EC2 KeyPair name (for SSH) | *required* |
| `SSHLocation` | CIDR allowed to SSH — **set this to your own IP/32** | `0.0.0.0/0` |

> **Security note:** `SSHLocation` defaults to `0.0.0.0/0` for convenience.
> Change it to your own address before deploying anything real:
> `--parameter-overrides SSHLocation=$(curl -s https://checkip.amazonaws.com)/32`

## Deploy

```bash
aws cloudformation deploy \
  --template-file ec-2-template.yaml \
  --stack-name ec2-ssh-demo \
  --parameter-overrides KeyName=my-key SSHLocation=203.0.113.10/32 \
  --capabilities CAPABILITY_IAM
```

Validate before deploying:

```bash
aws cloudformation validate-template --template-body file://ec-2-template.yaml
```

## Notes

- The AMI ID is region-specific (`ami-098e39bafa7e7303d`). Look up the current
  Amazon Linux 2 AMI for your region before deploying.
- The S3 bucket name is generated from the stack name and account ID, so it
  stays globally unique without hardcoding a name.
