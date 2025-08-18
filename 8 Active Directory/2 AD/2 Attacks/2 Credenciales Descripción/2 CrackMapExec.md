# CrackMapExec 

Tags: #AD #Crackmapexec #SMB #Parrot #Kali 

CrackMapExec (CME) es una herramienta de post-explotación y auditoría de seguridad ampliamente reconocida en ciberseguridad. Desarrollada en Python, está diseñada para automatizar la evaluación de redes extensas de Windows, aunque también admite interacciones con otros sistemas operativos. Es especialmente valiosa en pruebas de penetración y actividades de Red Teaming, facilitando el análisis y la explotación de redes complejas.

```bash 
1. Enumeración de Sistemas y Usuarios:
    - CME puede identificar rápidamente los sistemas operativos de los hosts en una red, los servicios que están ejecutando, y otros atributos.
    - Puede enumerar usuarios, grupos y políticas de contraseña de sistemas Windows, incluyendo aquellos en un dominio de Active Directory (AD).
    
2. Fuerza Bruta y Paso de Credenciales:
    - Permite realizar ataques de fuerza bruta para validar credenciales en una variedad de servicios, incluyendo SMB, SSH, WinRM, entre otros.
    - Es capaz de realizar "pass-the-hash" y "pass-the-ticket" para autenticar en sistemas Windows utilizando hashes NTLM o tickets Kerberos respectivamente, sin necesidad de las contraseñas en texto claro.
        
    
3. Movimiento Lateral:
    - Facilita el movimiento lateral mediante la ejecución remota de comandos a través de varios métodos como WMI (Windows Management Instrumentation), SMB (Server Message Block), y WinRM (Windows Remote Management).
    - Puede desplegar y ejecutar payloads y scripts en sistemas remotos.
        
4. Manipulación de Active Directory:
    - Puede interactuar con AD para extraer información valiosa, como detalles de políticas de grupo, relaciones de confianza entre dominios y mucho más.
    - Capacidad para identificar y explotar configuraciones erróneas en AD.
        
5. Ejecución de Módulos:
    - CME es modular, lo que significa que los usuarios pueden escribir y ejecutar sus propios módulos para realizar tareas específicas.
    - Varios módulos están disponibles para diversas tareas, desde la exfiltración de datos hasta la manipulación de configuraciones de sistema.
     
6. Interacción con Impacket:
    - CrackMapExec puede integrarse con la biblioteca Impacket para ampliar aún más sus capacidades, especialmente en lo que respecta a protocolos de red y técnicas de explotación.
        
7. Bypass de Seguridad:
    - Incluye técnicas para evadir soluciones de seguridad de punto final y detectar sistemas con protecciones como Antivirus y EDR (Endpoint Detection and Response).
```

## NetExec 

* [NetExec](https://github.com/Pennyw0rth/NetExec)

```bash 
❯ nxc ldap IP -u 'clearpass.user' -p 'Password@1' --users   

	# ldap = Protocolo a usar 
	# IP = Dirección IP y debe de estar con su dominio 'domain1.corp' en el '/etc/hosts' 
	# u = Usuario del dominio (Brecha asumida o Vector de ataque con acceso inicial) del AD
	# P = password
	# users = Enumeración de usuarios 

❯ nxc smb IP -u 'clearpass.user' -p 'Password@1' --users  
```