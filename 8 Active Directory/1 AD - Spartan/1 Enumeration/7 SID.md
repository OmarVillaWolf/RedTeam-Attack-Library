# Security Identifiers (SIDs)

Tags: #AD #Windows #SID #ADExplorer #Powershell 

Los Security Identifiers (SIDs) en Active Directory son identificadores únicos que se asignan a cada objeto, como usuarios o grupos, dentro de un dominio. Estos SIDs permanecen inalterables durante la vida del objeto, incluso si es trasladado entre dominios. Son esenciales para la seguridad en Windows, ya que determinan los permisos y niveles de acceso de los objetos en el sistema.

Un SID está formado por varios componentes, entre ellos el identificador de la autoridad de seguridad, el identificador del dominio y uno o más identificadores relativos (RID). Un ejemplo de cómo se representa un SID sería: `S-1-5-21-1234567890-1234567890-1234567890-1234`.

Se puede descargar **ADExplorer** para buscar cuentas de usuario y mostrar su SID.
## De SID a Nombre de Usuario

```powershell
❯ $sid = New-Object System.Security.Principal.SecurityIdentifier("S-1-5-21-...")   # Colocamos el SID entre las comillas dobles en 'Powershell'
❯ $user = $sid.Translate([System.Security.Principal.NTAccount])
❯ $user.Value     # Obtenemos el nombre del usuario que corresponde al identificador proporcionado 
```

```bash 
❯ wmic useraccount where sid="S-1-5-21-..."     # En la consola de Windows 'cmd' 
```

## De Nombre de Usuario a SID

```powershell 
❯ $user = Get-ADUser -Identity "nombre_usuario"           # En powershell
❯ $user.SID
```

```bash 
❯ wmic useraccount where name="nombre_usuario" get sid    # En la consola de Windows 'cmd'
```

