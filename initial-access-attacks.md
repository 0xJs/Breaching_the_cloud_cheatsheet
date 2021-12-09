# Initial access attacks
## Key disclosure in public repositories
- Scavange repos for keys
- Find keys in realtime: https://github.com/eth0izzle/shhgit
- Tools for finding secrets
  - Gitleaks https://github.com/zricethezav/gitleaks
  - Gitrob https://github.com/michenriksen/gitrob
  - Truffle hog https://github.com/dxa4481/truffleHog

### Gitleaks
- https://github.com/zricethezav/gitleaks
#### Search for secrets
```
./gitleaks detect -v source ~/tools/gitleaks/
```

#### Use web browser to view the commit
```
https://github.com/[git account]/[repo name]/commit/[commit ID]
```

## Password attacks
- Credential stuffing (databreaches)
- Password spraying

#### Password spraying Azure
- https://github.com/dafthack/MSOLSpray
- https://github.com/ustayready/fireprox
```
Import-Module .\MSOLSpray.ps1
Invoke-MSOLSpray -UserList validemails.txt -Password <PASSWORD> -Verbose
```

## Web server exploitation
- Here are some generic things ot look for:
  - Out of date web technologies with known vulns
  - SQL or command injection vulns
  - Server-side-request forgery (SSRF)
- Good place to start post shell:
  - Creds in metadata service
  - Certificates
  - Environment variables
  - Storage Accounts


#### Reused access certs as private keys on web servers
1. Comprimise web server
2. Extract certificate with mimkatz
3. Use it to authenticate to Azure
```
mimikatz# crypto::capi
mimikatz# privilege::debug
mimikatz# crypto::cng
mimikatz# crypto::certificates /systemstore:local_machine /store:my /export
```

### AWS Instance Metadata
- Metadata endpoint is hosted on a non routable IP adress at 169.254.169.254
- Can contain access/secret keys to AWS and IAM credentials
- Server compromise or SSRF vulnerabilities might allow remote attackers to reach it.
- IAM credentials can be stored here ```http://169.254.169.254/latest/meta-data/iam/security-credentials/<IAM Role Name>```
- New version requeres token, a put request is send and then responded to with a token. Then that token can be used to query data
```
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` 
curl http://169.254.169.254/latest/meta-data/profile -H "X-aws-ec2-metadata-token: $TOKEN"
```

## Phishing
- Still the #1 method of compromise
- Two primary phishing techniques:
  - Cred harvesting / session hijacking
    -  https://github.com/drk1wi/Modlishka
    -  https://github.com/kgretzky/evilginx2
  - Remote workstation compromise w/ C2

#### Google workspace calendar event injection
- Silently injects events to target calendars
- Bypasses the “don’t auto-add” setting
- Include link to phishing page
- https://www.blackhillsinfosec.com/google-calendar-event-injection-mailsniper/