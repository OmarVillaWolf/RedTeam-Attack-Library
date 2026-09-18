# Bypass

Tags: #Powershell #AD #DefenderCheck

## Detecciones de Powershell

```bash 
1. System-wide Transcription
2. Script Block Logging 
3. AntiMalware Scan Interface (AMSI)
4. Constrined Language Mode (CLM) - Integrated with Applocker and WDAC (Device Guard)
```

## Bypassing AV Signatures for Powershell 

```bash 
Los pasos para evitar la detección basada en firmas son:
1. Escanear usando AMSITrigger
2. Modificar el fragmento de código detectado
3. Volver a escanear usando AMSITrigger
4. Repetir los pasos 2 y 3 hasta obtener un resultado como “AMSI_RESULT_NOT_DETECTED” o “Blank”
```

## Bypassing AV Signatures for Powershell  - Invoke-Mimikatz

```bash 
En Mimikatz existen múltiples detecciones, por lo que es necesario hacer varios cambios:

1. Remover los comentarios por 'default'
2. Cambiar el nombre del script, los nombres de las funciones y las variables 
3. Modificar los nombres de las variables de las llamadas a la API de Win32 que se detectan
4. Ofuscar contenido de PEBytes -> DLL de PowerKatz usando paquetes 
5. Implementar una función inversa para los PEBytes para evitar cualquier firma estatica 
6. Agregar una verificación de espacio aislado para desperdiciar recursos de análisis dinámico
7. Retirar las advertencias reflectantes de PE para una salida limpia 
8. Utilizar comandos ofuscados para la ejecución de Invoke-MimiEx
9. Análizar usando DefenderCheck
   ❯ .\DefenderCheck.exe Invoke-Mimi.ps1
   ❯ .\DefenderCheck.exe Invoke-MimiEx.ps1
```