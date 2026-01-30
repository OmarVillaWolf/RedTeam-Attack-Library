# Política de ejecución de PowerShell

Tags: #AD #Windows #Evasion #Politicas 

## Utilizando el parámetro Bypass

Esta técnica aprovecha un parámetro introducido por Microsoft que permite eludir la política de ejecución al ejecutar scripts desde un archivo. Según Microsoft, cuando se utiliza este indicador, "no se bloquea nada, ni se generan advertencias o avisos". Esto se logra sin realizar cambios en la configuración del sistema ni escribir datos en el disco.

Una vez descargado el archivo `powerview.ps1`, se abre una terminal y se ejecuta el comando `powershell -executionpolicy bypass`. Este comando inicia PowerShell en un modo de política de ejecución "bypass", permitiendo la ejecución de cualquier script sin restricciones.

```powershell
❯ powershell -ep bypass 
```

## Utilizando el parámetro Unrestricted

Esto es similar a configurar el parámetro con "Bypass". Sin embargo, cuando se utiliza este indicador, Microsoft afirma que "carga todos los archivos de configuración y ejecuta todos los scripts. Si se ejecuta un script no firmado que se ha descargado de Internet, se pide permiso antes de que se ejecute". Esta técnica no da lugar a un cambio de configuración ni requiere escribir en el disco.

```powershell
❯ powershell -ep unrestricted
```

## Configurando el ExecutionPolicy sobre el proceso

La política de ejecución se puede aplicar en muchos niveles. Esto incluye el proceso sobre el que tiene control. Con esta técnica, la política de ejecución se puede configurar con el valor de Bypas durante la duración de su sesión. Además, no da como resultado un cambio de configuración ni requiere escribir en el disco.

```powershell
❯ Set-ExecutionPolicy Bypass -Scope Process
```