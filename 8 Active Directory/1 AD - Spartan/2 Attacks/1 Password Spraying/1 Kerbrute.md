# Kerbrute 

Tags: #AD #Kerbrute #Kali #Parrot 

Kerbrute es una herramienta de seguridad diseñada para realizar ataques de fuerza bruta y enumeración sobre el protocolo Kerberos en entornos de Active Directory (AD). Su objetivo es apoyar evaluaciones de seguridad al identificar cuentas de usuario válidas y detectar contraseñas potencialmente débiles, contribuyendo al análisis de vulnerabilidades en redes AD.

```bash 
Kerbrute realiza su funcionalidad principalmente a través de dos métodos: Ataques de fuerza bruta y enumeración de usuarios.

1. Ataques de Fuerza Bruta:
    - Kerbrute puede probar combinaciones de nombres de usuario y contraseñas para intentar autenticarse contra un dominio de AD utilizando Kerberos.
    - La herramienta intenta autenticarse con diferentes contraseñas para cada cuenta de usuario, lo que puede revelar contraseñas débiles o comunes.
        
2. Enumeración de Usuarios:
    - Kerbrute también puede realizar la enumeración de usuarios. En este modo, la herramienta intenta autenticarse utilizando nombres de usuario válidos con contraseñas incorrectas.
    - Kerberos, en algunos casos, responderá de manera diferente a una solicitud de autenticación si el nombre de usuario es válido, incluso si la contraseña es incorrecta. Esto puede permitir a Kerbrute identificar cuentas de usuario válidas sin conocer la contraseña.
```

## Sincronizar el reloj 

```bash 
Nota: Antes de iniciar al ataque debemos de sincronizar el reloj de la maquina de atacante con el AD

❯ ntpdate IP     # Sincronizar el reloj 

	# IP = Dirección IP del DC

❯ date -s "2025-01-04 15:30:00"   # Restablecer la fecha y hora
```

## Kerbrute

La herramienta Kerbrute puede generar intentos de autenticación fallidos que podrían ser detectados y provocar el bloqueo de cuentas, lo que puede interrumpir operaciones en redes de producción. Por este motivo, su uso debe limitarse a entornos controlados y autorizados, garantizando que no cause impactos negativos en sistemas en funcionamiento.

* [Kerbrute](https://github.com/ropnop/kerbrute)
* [Hunter.io-Usernames-Company](https://hunter.io/)
* [Seclists-usernames-CTF](https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames.txt)

```bash 
❯ ./kerbrute_linux_amd64 userenum --dc 10.0.1.100 -d domain1.corp ./users.txt 

	# userenum = Tipo de ataque 'Enumeración de usuarios'
	# d = Nombre del dominio 
	# dc = IP del dominio 
	# users = Lista de usuarios
```

