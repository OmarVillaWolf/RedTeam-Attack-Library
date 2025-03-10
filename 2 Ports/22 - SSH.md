# SSH 

Tags: #SSH #Puerto #Pivoting #Proxychains 

SSH es un protocolo de administración remota que permite a los usuarios **controlar** y **modificar** sus servidores remotos a través de Internet mediante un mecanismo de **autenticación seguro**. Como una alternativa más segura al protocolo **Telnet**, que transmite información sin cifrar, SSH utiliza **técnicas criptográficas** para garantizar que todas las comunicaciones hacia y desde el servidor remoto estén cifradas.

## Practicar 

```bash 
1. https://hub.docker.com/r/linuxserver/openssh-server
2. https://launchpad.net/ubuntu
```

## SSH

```bash
❯ ssh ❮User❯@❮IP❯                  # Conectarse a SSH
```

```bash
❯ ssh ❮User❯@❮IP❯ -p 2222          # Conectarse a SSH en un puerto especifico
```

```bash
❯ sshpass -p <'PASSWORD'> ssh <USER>@<IP>      # Conectarse a SSH colocando la passwd en texto claro 
```

```bash
❯ ssh -i id_rsa <user>@<IP>         # Conectarse a SSH con un 'id_rsa' y privilegio '600'
```

```bash
❯ ssh <user>@localhost              # Conectar a SSH localmente sin proporcionar passwd solo con  'authorized_key' 
```

```bash
# En una 'restricted bash' (rbash) se puede ejecutar comandos en el SSH, si la 'restricted bash' esta mal configurada

❯ ssh <User>@<IP> <Command>    # Ejecutar un comando y hacer que la restricted bash proporcione una bash. 
	bash                      # Colocar bash como comando, se podrá interactuar aunque no se tenga una pseudo-consola. Pero se puede hacer un tratamiento de la consola Linux
```

```bash 
❯ ssh-keygen       # Crear una clave publica y una clave privada en Kali 
❯ cat ~/.ssh/id_rsa.pub | tr -d '\n' | xclip -sel clip   # Mirar el contenido de la clave publica    
	# ~ = /home/omar/...
	# tr = Quitar el salto de linea
	# xclip = Copiar el output en la clipboard

# El resultado se pega en el archivo que se crea con nombre 'authorized_keys' en la ruta de la maquina victima que es /root/.ssh
❯ nvim authorized_keys
```

## Enumerar usuarios 

```bash
❯ searchsploit ssh user enumeration (2)      # Exploit en Python2 para la versión <7.7 de SSH

❯ python2 45939.py <IP> <USER> 2/dev/null    # Verificar si un usuario existe en esa IP
```


