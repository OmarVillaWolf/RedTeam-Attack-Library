# WSMan

Tags: #Windows #AD #Powershell #WSMan #CRTE 


```powershell
# Ejecución usuando las credenciales en texto plano 
❯ WSManRM-WinRM.exe <hostname> <command> <domain\username> <password>  
		# hostname = Es el nombre del equipo al cual se quiere accesar 
		# command = Comando a ejecutar 
		
Ejemplo:
	WSManRM-WinRM.exe us-mailmgmt "set computername" .\administrator 'P@$$w0rd123!'


# Ejecución usando las credenciales de sesión 
❯ WSManRM-WinRM.exe <hostname> <command>
```

```powershell 
# Si se puede ejecutar el comando anterior satisfactoriamente, ingresar al server víctima de la siguiente manera:
❯ winrs -r:<hostname> -u:<domain\username> cmd 

Ejemplo:
	winrs -r:us-mailmgmt -u:.\administrator cmd 
```