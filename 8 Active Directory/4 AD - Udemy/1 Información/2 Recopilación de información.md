# Qué información recopilar?

Tags: #AD 

```bash 
1 Usuarios y cuentas locales 
2 Grupos locales 
3 Usuarios y cuentas de dominio
4 Grupos de dominio 
5 OUs
6 GPOs
7 ACLs
8 Relaciones de confianza entres bosques y dominios
9 Atributos de los objetos de AD
```

## 1. Recopilación local de información 

```bash
SAM ('Security Account Manager') se corresponde con una base de datos en Windows que almacena información sobre usuarios, grupos, contraseñas... del sistema 
	- La base de datos SAM se comprueba por el LSA (Local Security Authority) para 'autenticar el acceso de los usuarios al sistema local'
	- Las contraseñas de los usuarios se almacenan hasheadas en el Registro de Windows (HKLM/SAM). El fichero se encuentra en:
		   %SystemRoot%/system32/config/SAM
	- La SAM 'se puede enumerar de forma local y remotamente'


Nota:
	- A partir de la versión Windows 10 1607 y Windows Server 2016 esta información solo puede obtenerla un administrador del sistema. En versiones anteriores 'cualquier usuario del dominio puede enumerarla'
```

## 2. Recopilación remota de información (Es la que más interesa)

```bash 
1 Interfaces (LDAP, REPL, MAPI, SAM): Las interfaces proporcionan una manera de comunicarse con la base de datos. 

2 DSA: Permite obtener acceso al directorio. Mantiene el esquema, garantiza la identidad de los objetos, fuerza los tipos de datos en los atributos...

3 Database layer: Es una API que sirve de interfaz entre las aplicaciones y el directorio, de manera que las aplicaciones no puedan interactuar directamente con la base de datos 

4 ESE: Se comunica directamente con los registros individuales que se encuentran en el directorio

5 Database files: La información del directorio se almacena en un único fichero de base de datos. Adicionalmente utiliza ficheros de log para transmisiones que no terminan adecuadamente 
```

```bash 
LDAP (Lightweight Directory Access Protocol) es un protocolo de aplicación que permite interactuar con servicios de directorio (como Active Directory) para almacenar, leer o modificar información
	- 'Cualquier usuario de dominio puede consultar NTDS.dit utilizando LDAP', independiente de sus privilegios   
```