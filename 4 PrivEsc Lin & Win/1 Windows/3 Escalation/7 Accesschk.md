# Accesschk 

Tags: #Windows #PrivEsc #Accesschk 

**AccessChk** es una herramienta de Sysinternals que enumera permisos en objetos de Windows. En privesc sirve para encontrar configuraciones débiles que las herramientas automáticas a veces pasan por alto.

* [accesschk](https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk)

```powershell 
# Servicios con permisos débiles 
❯ accesschk.exe -uwcqv "Users" * /accepteula
❯ accesschk.exe -uwcqv "Everyone" * /accepteula
❯ accesschk.exe -uwcqv "Authenticated Users" * /accepteula

	- Busca servicios donde usuarios normales tengan `SERVICE_ALL_ACCESS` o `SERVICE_CHANGE_CONFIG`.

# Directorios escribibles 
❯ accesschk.exe -uwdq "C:\Program Files" /accepteula
❯ accesschk.exe -uwdq "C:\Program Files (x86)" /accepteula

	- Útil para DLL hijacking o binario reemplazable.

# Archivos escribibles 
❯ accesschk.exe -uwsq "C:\Windows\System32" /accepteula

# Claves de registro 
❯ accesschk.exe -uwkq HKLM\SYSTEM\CurrentControlSet\Services /accepteula
```