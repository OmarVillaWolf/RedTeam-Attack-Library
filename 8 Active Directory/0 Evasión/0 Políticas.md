# PowerShell Execution Policy - Bypass

Tags: #AD #Bypass #AD #Politicas 

* [15 ways to bypass Powershell execution policy](https://www.netspi.com/blog/entryid/238/15-ways-to-bypass-the-powershell-execution-policy)

Métodos para ejecutar scripts/comandos de PowerShell evitando temporalmente. Las restricciones de Execution Policy, principalmente durante la sesión/proceso actual.

```powershell 
# Bypass para la sesión/proceso actual
❯ Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process

# Iniciar una nueva sesión de PowerShell ignorando la Execution Policy
❯ powershell -ExecutionPolicy Bypass

# Ejecutar un comando específico ignorando la Execution Policy
❯ powershell -c "<comando>"

# Ejecutar un comando/script mediante Base64
❯ powershell -EncodedCommand <BASE64>

# Establecer la preferencia de Execution Policy de la sesión actual
❯ $env:PSExecutionPolicyPreference="Bypass"
```