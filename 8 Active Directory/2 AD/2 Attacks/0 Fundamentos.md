# Fundamentos de vulnerabilidades en AD

Tags: #AD #Windows 

Comprender las vulnerabilidades y ataques en Active Directory (AD) implica analizar su diseño, funcionamiento y los protocolos que lo sustentan. AD es un servicio de directorio de red que facilita la administración y seguridad en entornos Windows, pero su arquitectura y dependencias también presentan oportunidades para los atacantes. Una evaluación técnica de sus componentes y configuraciones es clave para identificar sus puntos débiles y mitigar riesgos asociados.

```bash 
1. Arquitectura y Diseño de AD: AD utiliza una estructura jerárquica que incluye elementos como dominios, árboles y bosques. Esta estructura, aunque eficiente para la gestión, puede presentar puntos de fallo únicos y caminos de propagación para ataques si no se configura o se mantiene adecuadamente.
    
2. Protocolos y Servicios de Autenticación: AD depende de protocolos como Kerberos y NTLM para la autenticación y autorización. Estos protocolos, aunque robustos, tienen características inherentes que pueden ser explotadas si no se gestionan correctamente (como la reutilización de tickets o hashes de autenticación).
    
3. Políticas y Control de Acceso: AD administra políticas de control de acceso y permisos a través de listas de control de acceso (ACL). Una configuración inadecuada de estas políticas puede permitir a los atacantes elevar privilegios o acceder a recursos restringidos.
    
4. Delegación y Trusts: AD permite la delegación de ciertos privilegios y la creación de relaciones de confianza entre diferentes dominios y bosques. Estas características, si se configuran incorrectamente, pueden ser abusadas para escalar privilegios o para movimientos laterales en la red.
    
5. Gestión de Identidades y Políticas de Contraseñas: Las políticas de contraseñas y la gestión de identidades en AD son cruciales para la seguridad. Las contraseñas débiles o las cuentas de usuario mal gestionadas pueden ser vectores de ataque.
    
6. Integraciones y Extensiones de Servicios: AD a menudo se integra con otras aplicaciones y servicios, lo que puede introducir vulnerabilidades adicionales, especialmente si estas integraciones no se manejan con principios de seguridad adecuados.
    
7. Registro y Monitoreo: La capacidad de AD para registrar eventos y la eficacia con la que se monitorean estos registros juegan un papel crucial en la detección de actividades sospechosas. Una supervisión insuficiente puede permitir que las actividades maliciosas pasen desapercibidas.
    
8. Actualizaciones y Parches: Como cualquier software, AD requiere actualizaciones regulares y parches para abordar nuevas vulnerabilidades descubiertas. La falta de un mantenimiento regular puede dejar sistemas expuestos a ataques conocidos.
```

## Requisitos para realizar ataques

| Ataque                        | Requisitos de Privilegio Técnico                                   |
| ----------------------------- | ------------------------------------------------------------------ |
| Kerbrute Enumeration          | No se requiere acceso al dominio                                   |
| Pass the Ticket               | Acceso como usuario al dominio requerido                           |
| Kerberoasting                 | Acceso como cualquier usuario requerido                            |
| AS-REP Roasting               | Acceso como cualquier usuario requerido                            |
| Golden Ticket                 | Compromiso total del dominio (administrador del dominio) requerido |
| Silver Ticket                 | Hash del servicio requerido                                        |
| Password Spray                | Conocimiento de nombres de usuario, sin acceso específico          |
| Credentials in Description    | Acceso limitado para leer descripciones de objetos en AD           |
| SMB Signing Disabled and IPv4 | Acceso a la red, idealmente en el mismo segmento de red            |
| Constrained Delegation        | Acceso a una cuenta con delegación restringida configurada         |
| Unconstrained Delegation      | Acceso a una cuenta con delegación no restringida                  |
| DNSAdmins                     | Membresía en el grupo DNSAdmins o capacidad para añadir miembros   |
| Abuse of ACL                  | Permisos para modificar listas de control de acceso (ACL)          |
| Abuse of GPO                  | Permisos para crear o modificar Objetos de Política de Grupo (GPO) |
| DcSync                        | Privilegios de administrador del dominio o similar                 |
