# Enumeración Local en Windows 

Tags: #LocalEnumeration #Windows #PrivEsc #Meterpreter

## Enumeración general 

1. Enumerar todos los directorios por si se encuentra un archivo interesante 

## Enumeración de la información del sistema 

### Que estamos buscando?

1. Hostname 
2. OS name (Windows 7, 8, etc...)
3. OS build & Service Pack (Windows 7 SP1 7600)
4. OS Architecture (x64/x86)
5. Installed updates/Hotfixes

# Comandos Windows 

```bash 
❯ hostname      # Muestar el nombre del usuario 
❯ C:\Windows\System32\hostname.exe

❯ systeminfo    # Despliega toda la información del SO como (Hotfix)
❯ C:\Windows\System32\systeminfo.exe

❯ wmic qfe get Caption,Description,HotfixID,InstalledOn     # Muestra información adicional 
	# Buscar los security Update para ver cuando fue instalado 
```
# Comandos de Meterpreter

```bash 
❯ getuid                      # Muestra el nombre del usuario servidor  

❯ sysinfo                     # Muestra el hostname, SO, build service pack, arquitectura, dominio  

❯ cd C:\\Windows\System32
	❯ cat eula.txt           # Archivo que provee información pertinente del SO, build y service pack 

❯ dir                         # Listas el contenido del directorio 

❯ shell                       # Generas una consola para comandos Windows 
```

