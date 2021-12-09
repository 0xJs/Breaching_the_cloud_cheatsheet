# Authenticated enumeration
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
