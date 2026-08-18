# FortiClient - VPN Before Logon

Tags: #RDP #Windows #FortiClient 

El fallo está en cómo **FortiClient maneja el diálogo de certificado no confiable** en la pantalla de inicio de sesión de Windows.

Las condiciones son:
- FortiClient vulnerable (5.4.3 o anterior, o 5.6.0).
- La opción **VPN Before Logon** está habilitada.
- El servidor VPN presenta un certificado inválido (autofirmado, expirado, acceso por IP en lugar de hostname, o un MITM)

## Vector de ataque 

* [CVE-2017-7344 ](https://clement.notin.org/blog/2017/12/22/CVE-2017-7344-Fortinet-FortiClient-Windows-privilege-escalation-at-logon/)

1. Iniciar sesión en RDP
2. Seleccionar VPN Before Logon
3. Intentar conectar (usuario y contraseña pueden ser cualquier cosa)
4. Aparece el aviso de certificado inválido (INICIO) como Alerta de Seguridad 
5. View Certificate -> Details -> Copy to File 
6. Certificate Export Wizard -> Select DER x.509 (.CER)
7. Examinar y habilitar todos los archivos (*.*) para encontrar la cmd 
8. Ir a la ruta 'C:\Windows\System32' para buscar 'cmd.exe'
9. Lanzar CMD como NT AUTHORITY\SYSTEM

```powershell 
# Crear un usuario y agregarlo al grupo administrators 
❯ net user omar P@ssw0rd123! /add
❯ net localgroup Administrators omar /add

# Verificar 
❯ net user omar
❯ net localgroup Administrators
```