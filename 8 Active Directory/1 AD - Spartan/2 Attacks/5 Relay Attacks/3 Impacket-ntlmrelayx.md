# Impacket-ntlmrelayx 

Tags: #AD #Windows #Relay #SMB #Impacket 

**Impacket-ntlmrelayx** es una herramienta de la suite Impacket, desarrollada por Fortra (anteriormente SecureAuth), diseñada para ejecutar ataques de retransmisión NTLM. Este tipo de ataque explota las vulnerabilidades en el manejo de autenticación del protocolo NTLM (NT LAN Manager) en redes Windows, permitiendo a los atacantes interceptar y retransmitir credenciales para acceder a recursos y servicios de la red sin necesidad de descifrar contraseñas.

* [SMB - Relay - attacks](https://swisskyrepo.github.io/InternalAllTheThings/active-directory/internal-mitm-relay/#smb-signing-disabled-and-ipv4)

```bash 
# Necesitamos tener varias condiciones:

1. Mirar la IP de la maquina de atacante
2. Que el 'SMB' este como 'SMB Signing Disabled' o 'Signing = False' 
3. Utilizando técnicas de envenenamiento de LLMNR y NBT-NS, el atacante puede hacer que las solicitudes de autenticación se dirijan a su máquina, esto se hará por medio de la tool de 'Impacket'.
```

```bash  
❯ impacket-ntlmrelayx --no-http-server -smb2support -t smb://IP    

	# smb = IP de la maquina victima que tiene el SMB no firmado 


Notas: 
	1. Este ataque hará un Man-in-the-middle, por lo que estará escuchando hasta que capture el HASH de SAM
	2. Hacer que la victima ingrese a un directorio que apunte a la 'IP del atacante' para generar tráfico y poder capturar el HASH de SAM.
	3. Una vez capturado el HASH se puede hacer 'Pass-the-hash' para autenticarse. 
```

## Análisis de impacket-ntlmrelayx

```bash 
1. Configuración del Ataque:
    - El comando ejecutado 'impacket-ntlmrelayx --no-http-server -smb2support -t smb://IP' configura un ataque de retransmisión NTLM.
    - '--no-http-server': Indica que no se debe iniciar un servidor HTTP para la retransmisión.
    - '-smb2support': Habilita el soporte para SMB versión 2.
    - '-t smb://IP': Define el objetivo del ataque, en este caso, un servidor con la dirección IP `10.0.1.249` accesible a través del protocolo SMB.
  

2. Carga de Protocolos Cliente:
    - La herramienta carga varios protocolos cliente, como DCSYNC, LDAPS, LDAP, IMAP(S), SMTP, HTTP(S), MSSQL, SMB y RPC. Esto significa que está preparada para retransmitir credenciales a través de cualquiera de estos protocolos.
   

3. Establecimiento de Servidores:
    - Configura varios servidores, incluyendo SMB, WCF y un servidor RAW en el puerto 6666, para escuchar conexiones entrantes y posiblemente retransmitirlas.
        
    
4. Ataque de Retransmisión:
    - Recibe una conexión desde 'IP' y autentica exitosamente contra el objetivo 'smb://IP' como 'DOMAIN1/ADMIN'.
    - Luego de controlar la conexión, indica que no hay más objetivos disponibles.
  

5. Explotación y Extracción de Hashes:
    - La herramienta manipula servicios en el sistema objetivo, como iniciar el servicio 'RemoteRegistry'.
    - Extrae el 'bootKey' del sistema objetivo, que es esencial para descifrar hashes almacenados localmente.
    - Finalmente, vuelca los hashes del SAM (Security Accounts Manager) del sistema objetivo, incluyendo cuentas de usuario como 'Administrator', 'Guest', 'DefaultAccount', entre otras.
```