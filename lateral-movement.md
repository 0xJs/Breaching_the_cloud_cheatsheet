## Steal access tokens
#### Google
-  Google JSON Tokens and credentials.db
-  JSON tokens typically used for service account access to GCP
-  If a user authenticates with gcloud from an instance their creds get stored here ```~/.config/gcloud/credentials.db```
```
sudo find /home -name "credentials.db
"
```

#### Web config and App config files
-  ```Web.config``` and ```app.config``` files might contain creds or access tokens.
- Look for management cert and extract to ```.pfx``` like publishsettings files

#### Internal repositories
- Find internal repos (scan for port 80, 443 or Query AD and look for subdomains or hostnames as git, code, repo, gitlab, bitbucket etc)
- Tools for finding secrets
  - Gitleaks https://github.com/zricethezav/gitleaks
  - Gitrob https://github.com/michenriksen/gitrob
  - Truffle hog https://github.com/dxa4481/truffleHog
  
#### Command history
- Look through command history
- ```~/.bash_history`` or ```%USERPROFILE%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt```
