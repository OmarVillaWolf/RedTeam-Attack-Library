# Credential Extraction 

Tags:  #AD #Windows #Powershell 

## ¿Qué es LSA / LSASS?
- **LSA (Local Security Authority)**  
  Responsable de la autenticación en sistemas Windows.
- **LSASS (Local Security Authority Subsystem Service)**  
  Proceso que implementa LSA y maneja la seguridad del sistema.

## ¿Qué almacena LSASS?
LSASS mantiene credenciales en diferentes formatos:
- NT Hash  
- Claves AES  
- Tickets de Kerberos  
- Otros datos de autenticación  
## ¿Cuándo se almacenan credenciales en LSASS?
Las credenciales se cargan en memoria cuando un usuario:
- Inicia sesión localmente o vía RDP  
- Usa `RunAs`  
- Ejecuta un servicio de Windows  
- Ejecuta una tarea programada (Scheduled Task / Batch Job)  
- Utiliza herramientas de administración remota  
## Importancia ofensiva
- LSASS es un **objetivo crítico** para extracción de credenciales  
- Permite acceso a:
  - Hashes
  - Tickets
  - Credenciales en texto claro (en ciertos casos)
## Consideraciones de seguridad
- Es uno de los procesos **más monitoreados** en Windows  
- Interactuar directamente con LSASS puede:
  - Activar alertas (EDR / AV)
  - Generar logs sospechosos  
## Credenciales accesibles SIN tocar LSASS
### SAM Hive (Registro)
- Contiene:
  - Credenciales locales  
  - NTLM hashes  
### LSA Secrets / SECURITY Hive
- Contiene:
  - Contraseñas de cuentas de servicio  
  - Credenciales cacheadas de dominio  
### DPAPI (en disco)
Credenciales protegidas que pueden extraerse:
- Credential Manager / Vault  
- Cookies de navegador  
- Certificados  
- Tokens (ej. Azure)  
## Nota mental (para pentesting)

> Si no puedes tocar LSASS → ve por:
> SAM → LSA Secrets → DPAPI

```powershell 
❯ Get-PSReadLineOption           # Mostrar la ruta del histórico  
	C:\Users\Omar\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\history.txt 
```