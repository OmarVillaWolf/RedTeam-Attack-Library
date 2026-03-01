	us# Kerberos 

Tags: #Kerberos #DC #Windows 

## Sincronizar el reloj 

```bash 
Nota: Antes de iniciar al ataque debemos de sincronizar el reloj de la maquina de atacante con el AD

❯ ntpdate IP_DC     # Sincronizar el reloj con el DC 

❯ date -s "2025-01-04 15:30:00"   # Restablecer la fecha y hora
```

## Kerbrute 

```bash 
❯ git clone https://github.com/ropnop/kerbrute           # Descargar la herramienta
❯ go build .                                             # Entrar al dir 'kerbrute' y compilarlo 
```

```bash 
❯ kerbrute userenum -d domain1.corp --dc IP user.txt     # Enumerar usuarios validos en el DC 

# Ataque de diccionario para encontrar usuarios validos en el DC
❯ kerbrute userenum --dc IP -d domain1.corp /usr/share/SecLists/Usernames/xato-net-10-million-usernames.txt 
```