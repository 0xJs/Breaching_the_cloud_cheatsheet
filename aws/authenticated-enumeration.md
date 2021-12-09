# Authenticated enumeration
## Authentication

#### Set AWS programmatic keys for authentication 
- use ```--profile=<name>`` for a new profile
```bash
aws configure
```

#### Open S3 bucket enumeration

List the contents of an S3 bucket

```bash
aws s3 ls s3://<bucketname>/ 
```

Download contents of bucket

```bash
aws s3 sync s3://bucketname s3-files-dir
```

## Manual enumeration
### Get basis account info
```
aws sts get-caller-identity
```

#### List EC2 instances
```
aws ec2 describe-instances --region <region>
```

#### List IAM users
```
aws iam list-users
```

#### List IAM roles
```
aws iam list-roles
```

#### List s3 buckets
```
aws iam list-roles
```
