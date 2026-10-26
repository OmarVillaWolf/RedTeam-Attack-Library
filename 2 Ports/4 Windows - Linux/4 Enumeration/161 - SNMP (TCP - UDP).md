# SNMP (Simple Network Management Protocol)

Tags: #SNMP #Comandos #UDP 


## Enumeración del protocolo 
```bash 
❯ snmp_check IP        # Enumera el SNMP, mostando info del sistema, red, interfaces, versión, etc...
```

```bash
# Buscar credenciales    <- IMPORTANTE 
❯ snmpwalk -c public -v2c ❮IP❯ 1        # Colocar 1 significa que empezará desde la raiz '/' y asi poder encontrar más información acerca del protocolo, por default empieza desde el 2

NOTA:
	- Si muestra un usuario ejemplo: "kiero" probarlo en los otros puertos abiertos como FTP, SSH.. de la siguiente manera: 'kiero:kiero' 
```

```bash 
# Enumerar el servicio con credenciales válidas 
# Se puede encontrar (Credenciales en texto claro, usuarios del sistema, interfaces de red, software instalado)
❯ snmpwalk -v3 -u user -A 'password' -a MD5 -l authNoPriv IP 
	# u = Es el usuario válido 
	# A = Passphrase del usuario (Password)
```

## Fuerza bruta 
* [Legba tool](https://github.com/evilsocket/legba)
```bash 
❯ legba snmp3 --target IP --username users.txt --password passwords.txt
```

```bash
# Buscar los 'community strings'
❯ onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt ❮IP❯  

NOTA: Diccionario del snmp a usar   
	- /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt
```
