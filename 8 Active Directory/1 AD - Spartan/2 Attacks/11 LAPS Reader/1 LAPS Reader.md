# LAPS Reader 

Tags: #AD #Windows 

* [ReadLAPSPassword](https://www.thehacker.recipes/ad/movement/dacl/readlapspassword)

LAPS (Local Administrator Password Solution) es una solución de Microsoft que gestiona las contraseñas de la cuenta de administrador local en los equipos de un dominio. Con LAPS, las contraseñas son generadas aleatoriamente, almacenadas de forma segura en un atributo del objeto de computadora correspondiente en Active Directory (AD), y accesibles solo para usuarios autorizados.

```bash 
Si un atacante compromete una cuenta que tiene permisos para leer los atributos de LAPS en AD, aquí hay varios ataques o acciones maliciosas que podría realizar:

1. Acceso a Equipos Individuales: El atacante podría utilizar las contraseñas recuperadas para acceder a cuentas de administrador local en máquinas individuales, permitiéndole movimiento lateral dentro de la red.
    
2. Escalada de Privilegios: Con acceso a la cuenta de administrador local, el atacante podría intentar escalar privilegios en la red, instalando herramientas de ataque, creando cuentas de usuario locales adicionales para mantener el acceso, o explotando otras vulnerabilidades.
    
3. Instalación de Malware o Ransomware: Podría instalar malware, incluido ransomware, en los equipos a los que tiene acceso, lo que podría resultar en una interrupción significativa de los servicios y posiblemente en la exfiltración de datos.
    
4. Recopilación de Información: El acceso administrativo a las estaciones de trabajo y servidores podría permitirle recopilar información confidencial y datos sensibles que podrían ser utilizados para futuros ataques o para obtener ganancias económicas.
    
5. Movimiento Lateral: Podría moverse lateralmente a través de la red utilizando las credenciales obtenidas para autenticarse en otras máquinas que tengan la misma contraseña de administrador local o donde la cuenta tenga privilegios.
    
6. Persistencia: Establecer múltiples puntos de persistencia en la red, asegurándose de que tenga acceso continuo incluso si una de las entradas es detectada y cerrada.
    
7. Bypass de Seguridad: Con el control de cuentas de administrador local, el atacante podría desactivar o alterar configuraciones de seguridad y software antivirus para evadir la detección.
    
8. Denegación de Servicio: Puede usar sus privilegios para deshabilitar servicios críticos, borrando datos o causando interrupciones en la operación normal del negocio.
```

```powershell
❯ Import-Module .\PowerView.ps1
❯ Get-DomainComputer COMPUTER -Properties ms-mcs-AdmPwd,ComputerName,ms-mcs-AdmPwdExpirationTime

	# COMPUTER = Nombre del PC
	# ms-mcs-AdmPwd = Contraseña del PC y poder ingresar por RDP a el
```