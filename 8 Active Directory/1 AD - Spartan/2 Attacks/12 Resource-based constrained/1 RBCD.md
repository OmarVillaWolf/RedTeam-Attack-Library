# (RBCD) Resource-based constrained

Tags: #AD #Windows

El ataque de delegación restringida basada en recursos (RBCD) en Active Directory es una técnica avanzada que aprovecha los privilegios de una cuenta específica, generalmente una cuenta de máquina, para modificar el atributo **msDS-AllowedToActOnBehalfOfOtherIdentity** en un objeto de Active Directory, como un equipo o servicio. Esto permite que dicha cuenta actúe en representación de cualquier otro usuario dentro del dominio.

```bash 
Los pasos técnicos clave para realizar un ataque RBCD son:

1. Compromiso Inicial: El atacante necesita comprometer inicialmente una cuenta con permisos suficientes para modificar atributos de objetos en Active Directory.
    
2. Modificación del Atributo 'msDS-AllowedToActOnBehalfOfOtherIdentity': Utilizando herramientas de administración de AD o scripts, el atacante establece este atributo en el objeto objetivo (como una computadora) para permitir que una cuenta específica actúe en su nombre.
    
3. Creación de un Ticket Falsificado: El atacante crea un ticket Kerberos falsificado (TGT) para el usuario que desea impersonar (como un administrador).
    
4. Uso de la Delegación**: Con el TGT falsificado, el atacante utiliza la delegación restringida para solicitar tickets de servicio (TGS) para acceder a otros recursos o servicios en el dominio como si fuera el usuario impersonado.
```

```bash 
El ataque de delegación restringida basada en recursos (RBCD) está relacionado con los permisos 'GenericAll' en el sentido de que, si un atacante obtiene derechos 'GenericAll' sobre un objeto de Active Directory, puede modificar el atributo 'msDS-AllowedToActOnBehalfOfOtherIdentity' del objeto. Los derechos 'GenericAll' otorgan control total sobre un objeto, incluida la capacidad de cambiar sus atributos. Por lo tanto, si un atacante tiene 'GenericAll' en un objeto de computadora, puede configurar ese objeto para permitir la delegación restringida, lo cual es clave en la ejecución de un ataque RBCD.
```