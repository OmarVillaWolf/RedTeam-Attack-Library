# Credential Extraction 

Tags:  #AD #Windows #Powershell 


```bash 
1. Local Security Authority (LSA) is responsible for authentication on a Windows machine. Local Security Authority Subsystem Service (LSASS) is its service.
2. LSASS stores credentials in multiple forms - NT hash, AES, Kerberos tickets and so on.
3. Credentials are stored by LSASS when a user:
	- Logs on to a local session or RDP
	- Uses RunAs
	- Run a Windows service
	- Runs a scheduled task or batch job
	- Uses a Remote Administration tool

4. The LSASS process is therefore a very attractive target.
5. It is also the most monitored process on a Windows machine.
6. Some of the credentials that can be extracted without touching LSASS
	- SAM hive (Registry) - Local credentials
	- LSA Secrets/SECURITY hive (Registry) - Service account passwords, Domain cached credentials etc.
	- DPAPI Protected Credentials (Disk) - Credentials Manager/Vault, Browser Cookies, Certificates, Azure Tokens etc.
```

```powershell 
❯ Get-PSReadLineOption           # Mostrar la ruta del history 
	C:\Users\Omar\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\history.txt 
```