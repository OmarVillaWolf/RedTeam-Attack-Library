# Language Mode

Tags: #LanguageMode

```powershell 
❯ $ExecutionContext.SessionState.LanguageMode     # Muestra el modo de ejecución/restricción actual de PowerShell, o sea qué tan restringido está PowerShell

Donde:
	1. FullLanguage = Modo PowerShell completo (normal) | [System.IO.File]::ReadAllText(...) funciona 
	2. ConstrainedLanguage = PowerShell restringido por AppLocker/WDAC/Device Guard | [System.Reflection.Assembly]::Load(...) falla
	3. RestrictedLanguage = bloquea casi toda automatización avanzada
	4. NoLanguage = NO scripting real permitido (El más restrictivo)
```