# Across Domain Trust - AD CS

Tags: #AD 

* [Certified_Pre-Owned.pdf](https://specterops.io/wp-content/uploads/sites/3/2022/06/Certified_Pre-Owned.pdf)

- **Active Directory Certificate Services (AD CS)** permite el uso de una **Infraestructura de Clave Pública (PKI)** dentro de un bosque de Active Directory.
- **AD CS** ayuda en la autenticación de usuarios y máquinas, así como en el cifrado y firmado de documentos, sistemas de archivos, correos electrónicos y más.
- **“AD CS es un rol de servidor que permite construir una infraestructura de clave pública (PKI) y proporcionar criptografía de clave pública, certificados digitales y capacidades de firma digital para tu organización.”**

| Término                           | Descripción                                                                                                                                                |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CA (Certification Authority)      | Autoridad certificadora que emite certificados. El servidor con el rol de AD CS (puede ser un DC o uno separado) es la CA.                                 |
| Certificate                       | Certificado emitido a un usuario o máquina, utilizado para autenticación, cifrado, firma, etc.                                                             |
| CSR (Certificate Signing Request) | Solicitud de firma de certificado realizada por un cliente hacia la CA para obtener un certificado.                                                        |
| Certificate Template              | Define la configuración de un certificado. Incluye permisos de inscripción (enrollment), EKUs, expiración, etc.                                            |
| EKU OIDs                          | Identificadores de uso extendido de clave. Determinan para qué se puede usar el certificado (ej: autenticación de cliente, Smart Card Logon, SubCA, etc.). |

Existen múltiples formas de abusar de **AD CS** (consulta el paper _"Certified Pre-Owned"_ en las notas de la presentación):

- Extraer certificados de usuarios y máquinas
- Usar certificados para obtener el hash NTLM
- Persistencia a nivel de usuario y máquina
- Escalación a **Domain Admin** y **Enterprise Admin**
- Persistencia a nivel dominio

| Categoría             | ID       | Descripción                                                                     |
| --------------------- | -------- | ------------------------------------------------------------------------------- |
| Stealing Certificates | THEFT1   | Exportar certificados con llaves privadas usando APIs criptográficas de Windows |
| Stealing Certificates | THEFT2   | Extraer certificados de usuario con llaves privadas usando DPAPI                |
| Stealing Certificates | THEFT3   | Extraer certificados de máquina con llaves privadas usando DPAPI                |
| Stealing Certificates | THEFT4   | Robar certificados desde archivos y almacenes                                   |
| Stealing Certificates | THEFT5   | Usar Kerberos PKINIT para obtener el hash NTLM                                  |
| Persistence           | PERSIST1 | Persistencia de usuario solicitando nuevos certificados                         |
| Persistence           | PERSIST2 | Persistencia de máquina solicitando nuevos certificados                         |
| Persistence           | PERSIST3 | Persistencia de usuario/máquina renovando certificados                          |

| ESC                      | Descripción                                                                                                      |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| ESC1                     | El enrollee puede solicitar un certificado para CUALQUIER usuario                                                |
| ESC2                     | EKU de “Any Purpose” o sin EKU (potencialmente peligroso)                                                        |
| ESC3                     | Solicitar un certificado de Enrollment Agent y usarlo para pedir certificados en nombre de CUALQUIER usuario     |
| ESC4                     | ACLs demasiado permisivas en templates                                                                           |
| ESC5                     | Mal control de acceso en el servidor CA / objeto de computadora de la CA                                         |
| ESC6 (Parcheado May'22)  | Configuración EDITF_ATTRIBUTESUBJECTALTNAME2 en la CA permite solicitar certificados para CUALQUIER usuario      |
| ESC7                     | Mal control de acceso en roles de la CA como “CA Administrator” y “Certificate Manager”                          |
| ESC8                     | NTLM relay hacia endpoints HTTP de enrollment                                                                    |
| ESC9                     | Sin Security Extension (el enrollee puede modificar su UPN y solicitar cert en nombre de CUALQUIER usuario)      |
| ESC10                    | Mapeo débil implícito de certificados (el enrollee puede modificar su UPN para solicitar cert como otro usuario) |
| ESC11                    | NTLM relay hacia endpoints RPC de enrollment                                                                     |
| ESC12                    | Robo de clave privada de la CA (ej: YubiHSM)                                                                     |
| ESC13                    | El enrollee obtiene privilegios del grupo vinculado                                                              |
| ESC14 (Por parchear)     | Autenticación como objetivo usando certificado referenciado en `altSecurityIdentities`                           |
| ESC15 (Parcheado Nov'24) | EKUwu – abuso de templates versión 1 para sobrescribir EKUs                                                      |
Los siguientes ESC son fáciles de abusar: 
- ESC1, ESC3, ESC4, ESC5, ESC7, ESC8

| Categoría          | ID        | Descripción                                                             |
| ------------------ | --------- | ----------------------------------------------------------------------- |
| Domain Persistence | DPERSIST1 | Forjar certificados usando llaves privadas de la CA robadas             |
| Domain Persistence | DPERSIST2 | Crear CA raíz/intermedia maliciosa                                      |
| Domain Persistence | DPERSIST3 | Backdoor en el servidor de la CA o en el objeto de computadora de la CA |

