# Bypass 

Tags: #Powershell #AD 

## Detecciones de Powershell

```bash 
1. System-wide Transcription
2. Script Block Logging 
3. AntiMalware Scan Interface (AMSI)
4. Constrined Language Mode (CLM) - Integrated with Applocker and WDAC (Device Guard)
```

## Política de ejecución 

* [15 ways to bypass Powershell execution policy](https://www.netspi.com/blog/entryid/238/15-ways-to-bypass-the-powershell-execution-policy)

```powershell 
❯ powershell -ExecutionPolicy bypass 

❯ powershell -c <cmd>

❯ powershell -encodedcommand 
$env:PSExecutionPolicyPreference="bypass"
```

## Bypassing Powershell Security 

* [Invisi-shell](https://github.com/OmerYa/Invisi-Shell)

```bash 
1. Usar invisi-shell para 'bypass' los controles de seguridad en Powershell
2. La herramineta intercepta (modifica) los ensablados .NET 'System.Management.Automation.dll y System.Core.dll' para 'bypass' el login 
3. Utiliza una API de profiler de CLR para realizar el hook (enganche/intercepción)
4. Un profiler de Common Language Runtime (CLR) es una biblioteca de enlace dinámico (DLL) que consiste en funciones que reciben y envían mensajes al CLR utilizando la API de perfilado. La DLL del profiler es cargada por el CLR en tiempo de ejecución.
```

```powershell 
❯ RunWithPathAdmin.bat             # Con privilegios de admin
❯ RunWithRegistryNonAdmin.bat      # Sin privilegios de admin

Nota:
	1. Escribe 'Exit' desde la nueva sesión de Powershell para limpiar la consola 
```

## Bypassing AV Signatures for Powershell 

* [AMSITrigger](https://github.com/RythmStick/AMSITrigger)
* [DefenderCheck](https://github.com/t3hbb/DefenderCheck)
* [Invoke-Ofuscation](https://github.com/danielbohannon/Invoke-Obfuscation)

```bash 
1. Siempre se puede cargar scripts en memoria y evitar la detección usando AMSI bypass
2. Usar AMSITrigger o DefenderCheck para identificar codigo y strings desde un binario o script que Windows Defender podria marcar como sospechoso (malicioso)
3. Invoke-Ofuscation es usado para tener una ofuscación total del script en Powershell 
```

```powershell 
❯ AmsiTrigger_x64.exe -i C:\AD\Tools\Invoke-PowershellTcp.ps1   
❯ DefenderCheck.exe PowerUp.ps1

Nota:
	1. Si quieres ofuscar el 'PowerUp.ps1' ir a la linea '2640' y eliminar el contenido de la variable '$B64Binary = ""'
```
## Bypassing AV Signatures for Powershell 

```bash 
Pasos para evitar la detección basada en firmas son bastante simples:
1. Escanear usando AMSITrigger
2. Modificar el fragmento de código detectado
3. Volver a escanear usando AMSITrigger
4. Repetir los pasos 2 y 3 hasta obtener un resultado como “AMSI_RESULT_NOT_DETECTED” o “Blank”
```

## Bypassing AV Signatures for Powershell  - Invoke-Mimikatz

```bash 
En Mimikatz existen multiples detecciones, por lo que es necesario hacer varios cambios:

1. Remover los comentarios por 'default'
2. Cambiar el nombre del script, los nombres de las funciones y las variables 
3. Modificar los nombres de las variables de las llamadas a la API de Win32 que se detectan
4. Ofuscar contenido de PEBytes -> DLL de PowerKatz usando paquetes 
5. Implementar una función inversa para los PEBytes para evitar cualquier firma estatica 
6. Agregar una verificación de espacio aislado para desperdiciar recursos de análisis dinámico
7. Retirar las advertencias reflectantes de PE para una salida limpia 
8. Utilizar comandos ofuscados para la ejecución de Invoke-MimiEx
9. Análizar usando DefenderCheck
```