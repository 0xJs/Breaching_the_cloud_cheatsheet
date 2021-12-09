# Authenticated enumeration
#### Connect to Azure
```
Connect-AzAccount
```

#### Connect to MSOL
```
connect-msolservice
```

#### Get current user's role assignment
```
Get-AzRoleAssignment
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

#### Get automation accounts
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

#### Get the AZ subscriptions the user has access too
```
Get-AzSubscription
```

#### List the current context
```
Get-AzContext | fl *
```

#### Get all commands for AZ and MSOnline
```
Get-Module -Name Az.Accounts | Select-Object -ExpandProperty ExportedCommands
Get-Module -Name MSOnline | Select-Object -ExpandProperty ExportedCommands
```
