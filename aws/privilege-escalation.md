# Privilege Escalation
## PACU Check for privilege escalation
```bash
run iam__privesc_scan
```

## Execute commands on vm's
- Requires EC@COnfig or System Manager agent on instances
- Or SSH keys

## Gain AWS console access
- https://github.com/NetSPI/aws_consoler

## Check for credentials
- Check the following dirs for scripts/creds:
  - ``` C:\ProgramData\Amazon`` 
  - ```C:\Program Files\Amazon\WorkSpacesConfig\```

## Lamda
### Read lamda functions
- Copy access keys found in the environment variables
```
sudo aws lambda list-functions --profile <PROFILE> --region <REGION>
```

#### Create a new profile for the access keys
```
sudo aws configure --profile <PROFILE>
```

#### Use the creds, for example list ec2 instances:
```
sudo aws ec2 describe-instances --profile <PROFILE> --region <REGION>
```
