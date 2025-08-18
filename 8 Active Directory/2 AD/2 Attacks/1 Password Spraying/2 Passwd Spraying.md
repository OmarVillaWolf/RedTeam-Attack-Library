# Password Spraying 

Tags: #AD #PasswordSpraying #Kali #Parrot 

El "Password Spraying" es una técnica de ataque cibernético que prueba una única contraseña débil o común en múltiples cuentas de usuario. A diferencia de los ataques de fuerza bruta tradicionales que intentan numerosas contraseñas en una sola cuenta, este método evita activar políticas de bloqueo al distribuir los intentos entre varios usuarios. Es particularmente eficaz contra sistemas de autenticación en línea, como correos electrónicos o servicios en la nube, aprovechándose de contraseñas comúnmente usadas y configuraciones de seguridad insuficientes.

La herramienta Kerbrute puede generar intentos de autenticación fallidos que podrían ser detectados y provocar el bloqueo de cuentas, lo que puede interrumpir operaciones en redes de producción. Por este motivo, su uso debe limitarse a entornos controlados y autorizados, garantizando que no cause impactos negativos en sistemas en funcionamiento.

*  [Kerbrute](https://github.com/ropnop/kerbrute)

```bash 
❯ ./kerbrute_linux_amd64 passwordspray --dc 3.149.246.12 -d domain1.corp ./users.txt Password@1

	# passwordspray = Tipo de ataque 'PasswordSpraying'
	# dc = IP del dominio 
	# d = Nombre del dominio 
	# users = Lista de usuarios 
	# Password@1 = Contraseña que se utiliza para ser probada con toda la lista de usuarios validos 
```