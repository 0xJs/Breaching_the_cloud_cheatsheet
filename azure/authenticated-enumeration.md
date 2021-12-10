# Authenticated enumeration
## Manual enumeration
### Authentication

```
Import-Module Az
Connect-AzAccount

## Or this way sometimes gets around MFA restrictions

$credential = Get-Credential
Connect-AzAccount -Credential $credential
```

#### Import a context file

```
Import-AzContext -Profile 'C:\Temp\Live Tokens\StolenToken.json'
```

#### Export a context file

```
Save-AzContext -Path C:\Temp\AzureAccessToken.json
```

#### Connect to MSOL
```
connect-msolservice
```

### Basic enumeration
#### Get current user's role assignment
```
Get-AzRoleAssignment
```

#### Get context details
```
$context = Get-AzContext
$context.Name
$context.Account
```

#### List the current context
```
Get-AzContext | fl *
```

#### Get the AZ subscriptions the user has access too
```
Get-AzSubscription
```

#### Choose a subscription
```
Select-AzSubscription -SubscriptionID "SubscriptionID"
```

#### Get all users
```
Get-MSolUser -all
```

#### Get all groups
```
Get-MSolGroup -all
```

#### Get members of group
```
Get-MSolGroupMember -GroupObjectID <GUID>
```

#### Get azure resources
```
Get-AzResource
Get-AzResourceGroup
```

### Runbooks
#### List Azure Runbooks
```
Get-AzAutomationAccount
Get-AzAutomationRunbook -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName>
```

#### Export a runbook
```
Export-AzAutomationRunbook -AutomationAccountName <account name> -ResourceGroupName <resource group name> -Name <runbook name> -OutputFolder .\Desktop\
```

#### Get basic information
- Interesting information: UsersPermissionToReadOtherUsersEnabled, DirSyncServiceAccount, PasswordSynchronizationEnabled, Address/phone/emails
```
Get-MSolCompanyInformation
```

#### Get all commands for AZ and MSOnline
```
Get-Module -Name Az.Accounts | Select-Object -ExpandProperty ExportedCommands
Get-Module -Name MSOnline | Select-Object -ExpandProperty ExportedCommands
```

#### List webapps
```
Get-AzAdApplication
Get-AzWebApp
```

### SQl server
#### List SQL servers
```
Get-AzSQLServer
```

#### List databases
```
Get-AzSqlDatabase -ServerName <Server Name> -ResourceGroupName <Resource Group Name>
```

#### List SQL Firewall rules
```
Get-AzSqlServerFirewallRule –ServerName <ServerName> -ResourceGroupName <ResourceGroupName>
```
  
#### List out SQL server AD Admins
```
Get-AzSqlServerActiveDirectoryAdminstrator -ServerName <ServerName> -ResourceGroupName <ResourceGroupName>
```

### Virtual Machines
#### List VM's user has access too
```
Get-AzVM
```

#### Get OS Details
- List local admin username
```
$vm = Get-AzVM -Name <name> 
$vm.OSProfile
```

#### List vm's which are a managed identity
```
(az vm list | ConvertFrom-Json) | ForEach-Object {$_.name;(az vm identity show --resource-group $_.resourceGroup --name $_.name | ConvertFrom-Json)}
```

### Networking
#### List virtual networks
```
Get-AzVirtualNetwork
```

#### List public IP addresses assigned to virtual NICs
```
Get-AzPublicIpAddress
```

#### Get Azure ExpressRoute (VPN) Info
```
Get-AzExpressRouteCircuit
```

#### Get Azure VPN Info
```
Get-AzVpnConnection
```

## Powerzure enumeration
- https://github.com/hausec/PowerZure
- https://powerzure.readthedocs.io/en/latest/

#### Whoami
```
Show-AzureCurrentUser
```

### List all users
```
Get-AzureUser -All
```

#### List all groups
```
Get-AzureGroup -All
```

#### List all users of a group
```
Get-AzureGroup –Group ‘Global Admins’
```

#### List resources
```
Get-AzureTargets
```

#### List out owners of applications
```
Get-AzureAppOwners
```

#### Lists members of a role
```
Get-AzureADRole -Role <ROLE NAME>
```

#### Lists key vaults
```
Show-AzureKeyVaultContent
```

#### List storage accounts
```
Show-AzureStorageContent
```

#### Lists runbook contents
```
Get-AzureRunbookContent
```

#### Create link to download a VM disk
```
Get-AzureVMDisk 
```

## MFASweep
- Use MFASweep to find inconsistensies through MFA requirements
- https://github.com/dafthack/MFASweep
- Blogpost: https://www.blackhillsinfosec.com/exploiting-mfa-inconsistencies-on-microsoft-services/
```
Import-Module MFASweep.ps1
Invoke-MFASweep -Username <EMAIL> -Password <PASSWORD>
```
