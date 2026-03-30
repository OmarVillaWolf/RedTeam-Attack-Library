# Unconstrained Delegation

Tags: #AD #Windows 

La delegación no restringida (Unconstrained Delegation) es una funcionalidad de seguridad en Active Directory que habilita a un servicio o servidor para solicitar tickets Kerberos en nombre de un usuario autenticado en dicho servicio. En esencia, esto permite que el servicio actúe como el usuario, utilizando sus credenciales para acceder a otros recursos en la red. Este mecanismo es relevante en el ámbito del pentesting y la seguridad informática debido a los riesgos asociados con su mal uso, ya que puede facilitar movimientos laterales y compromisos en la red.

```bash 
#Funcionamiento de la Delegación No Restringida:

1. Autenticación Inicial: Un usuario se autentica ante un servidor que tiene habilitada la delegación no restringida. Durante este proceso, el Ticket Granting Ticket (TGT) del usuario se almacena en la memoria del servidor.
    
2. Acceso a Otros Servicios: El servidor ahora puede usar ese TGT para solicitar tickets de acceso a otros servicios (service tickets) en nombre del usuario, sin ninguna restricción específica.
    
3. Potencial de Movimiento Lateral: Este servidor podría acceder a cualquier otro servicio dentro del mismo dominio (o en algunos casos, incluso en dominios de confianza) como si fuera el usuario, lo que puede permitir a un atacante moverse lateralmente en la red.


#Riesgos de Seguridad:

La delegación no restringida presenta riesgos significativos:

- Exposición de TGTs: Los TGTs de los usuarios pueden ser robados por atacantes que comprometen el servidor, lo que permite al atacante solicitar tickets de servicio para cualquier servicio como si fueran el usuario comprometido.
    
- Movimiento Lateral: Un atacante puede utilizar los TGTs capturados para moverse lateralmente a través de la red y acceder a otros servicios y datos.
    
- Escalada de Privilegios: Si el TGT pertenece a un usuario con altos privilegios, como un administrador de dominio, el atacante puede obtener un control significativo sobre la red.
```

# TrustedToAuthForDelegation vs TrustedForDelegation

```bash 
Es esencial no confundir estos dos, ya que cada uno implica un nivel diferente de acceso y riesgo. El Unconstrained Delegation otorga capacidades más amplias y, por lo tanto, es potencialmente más peligrosa si se abusa de ella.

- TrustedToAuthForDelegation: Este parámetro se utiliza para la delegación restringida (constrained delegation). Cuando está configurado, permite que una cuenta específica (como una cuenta de servicio) delegue credenciales a otros servicios específicos, pero con restricciones claras sobre a qué servicios puede realizar esta delegación.
    
- TrustedForDelegation: Este parámetro se aplica a la delegación sin restricciones (unconstrained delegation). Cuando una cuenta tiene este parámetro configurado, puede delegar credenciales a cualquier servicio, lo que representa un mayor riesgo de seguridad.
```