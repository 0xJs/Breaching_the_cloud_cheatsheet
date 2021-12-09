# Authenticated enumeration
### Enumerating AWS Access
- https://github.com/carnal0wnage/weirdAAL
```
python3 weirdAAL.py -m recon_all -t test
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
