# Klist 

Tags: #AD #Klist #Windows #Powershell 

`klist` es una herramienta de línea de comandos en Windows utilizada para inspeccionar los tickets de Kerberos almacenados en la caché de un cliente. Es especialmente útil para diagnosticar problemas de autenticación y verificar la correcta emisión de tickets. La utilidad permite visualizar tanto los Ticket Granting Tickets (TGT) como los Service Tickets (ST), mostrando detalles como su validez, tipo de cifrado y flags asociados.

En el contexto de la ciberseguridad ofensiva, `klist` se utiliza para entender el estado de las credenciales Kerberos en una máquina comprometida, lo que puede ser crucial para movimientos laterales y escalada de privilegios en un entorno de Active Directory.

```bash 
❯ klist     # Ejecutamos la herramienta en la maquina Windows de la victima 
```

```bash 
1. Información General:
    - 'Current LogonId is 0:0x10d6046': Identifica la sesión actual en la que se ejecuta el comando.
        
2. Detalles de los Tickets en Caché:
    - Número de Tickets: Se muestran dos tickets en caché ('Cached Tickets: (2)').
        
3. Ticket #0:
    - Cliente: 'regular.user @ DOMAIN1.CORP'. Este es el usuario para el cual se emitió el ticket.
    - Servidor: 'krbtgt/DOMAIN1.CORP @ DOMAIN1.CORP'. Es el Ticket Granting Ticket (TGT) para el dominio 'DOMAIN1.CORP'.
    - Tipo de Encriptación del Ticket: 'AES-256-CTS-HMAC-SHA1-96'.
    - Flags del Ticket: Indican características como 'forwardable', 'renewable', 'initial', 'pre_authent', 'name_canonicalize'.
    - Tiempos de Validez: Inicio ('Start Time'), fin ('End Time') y renovación ('Renew Time') del ticket.
    - Tipo de Clave de Sesión: 'AES-256-CTS-HMAC-SHA1-96'.
    - Flags de Caché: '0x1 -> PRIMARY' indica que es el ticket primario en la caché.
    - KDC Llamado: 'First-DC.domain1.corp'.
    
4. Ticket #1:
    - Similar al Ticket #0, pero este es específico para el servicio LDAP.
    - Este ticket tiene el flag 'ok_as_delegate', indicando que puede ser utilizado para delegación de credenciales.
```