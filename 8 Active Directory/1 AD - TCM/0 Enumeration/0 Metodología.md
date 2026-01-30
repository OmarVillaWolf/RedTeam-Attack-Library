# Metodología PT a AD 

Tags: #AD 

## Metodología 

```bash 
1. Initial Compromise 
2. Host Reconnaissance
3. Domain Enumeration 
4. Local Privilege Escalation 
5. Administrator Enumeration 
6. Lateral Movement 
7. Domain Admin Priv 
8. Cross Trust Attacks 
9. Domain Persistence 
10. Exfiltrate 
```

## Técnicas

```bash 
# Breaching AD
1. Password Spraying: Intentar autenticarse usando contraseñas comunes o filtradas en varias cuentas de usuario.
2. Brute Force Attacks: Utilizar herramientas automatizadas para adivinar la contraseña de las cuentas de los usuarios con passwords debiles o predeterminadas. 
3. Phishing: Crear emails convincentes para engañar a los usuarios para que revelen sus credenciales o ejecuten archivos maliciosos. 
4. Poisoning: Envenenamiento LLMNR / NBT-NS.

# AD Enumeration 
1. PowerView: Enumerar los objetos, atributos y permisos de AD e identificar posibles debilidades de seguridad. 
2. BloodHound: Visualizar y analizar permisos de anuncios, agrupar membresias y rutas de ataque.
3. LDAP Enumeration: Consultar el servicio de AD LDAP para recuperar informacion detallada sobre usuarios, grupos equipos y unidades organizativas (OUs). 

# Privilege Escalation 
1. Kerberoasting: Es un ataque a Kerberos que se dirige al servicio de cuentas para obtener sus contraseñas de cuenta de servicio cifradas (SPN), que se puede descifrar sin conexion para obtener contraseñas en texto claro. 
2. AS-REP Roasting: Es un ataque de autenticacion Kerberos que se dirige a las cuentas de usuario con el mensaje 'No requiere preautenticacion de Kerberos'.

# Lateral Movement 
1. Pass-the-hash: Usar el hash de contraseñas robadas para autenticarse y obtener acceso a otors sistemas sin conocer las contraseñas en texto plano.
2. Pass-the-ticket (PtT): Es una tecnica utlizada en entornos de AD para autenticarse a otros sistemas o servicios usando un 'TGTs' robado o servicio de ticktes sin saber la contraseña del usuario en texto plano. 

# Persistence 
1. Solver Ticket: Es una tecnica utilizada en entornos AD para suplantar cualquier servicio o computadora por falsificar tickets de servicio de Kerberos sin necesidad de obtener la contraseña del usuario en texto plano.
2. Golden Ticket: Implican falsificar tickets de Kerberos para usuarios arbitrarios, lo que permite a los atacantes hacerse pasar por cualquier usuario y acceder cualquier recurso al entorno del AD.
```