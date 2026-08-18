# SNMP (Simple Network Management Protocol)

Tags: #SNMP #Comandos #UDP 


```bash 
❯ snmp_check IP        # Enumera el SNMP, mostando info del sistema, red, interfaces, versión, etc...
```

```bash
/usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt     # Diccionario del snmp a usar para Fuerza Bruta 
```

```bash
❯ onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp-onesixtyone.txt ❮IP❯     # Sirve para hacer Fuerza Bruta al snmp y encontrar los 'community strings'
```

```bash
❯ snmwalk -c public -v2c ❮IP❯          # Sirve para poder inspeccionar el puerto SNMP

	# c = Community string
	# v2c = version

❯ snmwalk -c public -v2c ❮IP❯ 1        # Colocar 1 significa que empezara desde la raiz '/' y asi poder enco0ntrar mas informacion acerca del protocolo, por default empieza desde el 2
```