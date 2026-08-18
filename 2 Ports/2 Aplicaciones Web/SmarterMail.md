# SmarterMail

Tags: #SmarterMail #Windows 

SmarterMail es un servidor de correo electrónico y una plataforma de colaboración empresarial creado por SmarterTools como una alternativa económica y de alto rendimiento a Microsoft Exchange. Ofrece funciones avanzadas de correo, calendarios, contactos y chat.

## RCE Build 6985

* [RCE](https://www.exploit-db.com/exploits/49216)

```bash 
# Recomendaciones: 

0. Conocer la 'Build': 
	- A veces sale en el código fuente como: 
		  var stProductBuild = "6919 (Dec 11, 2018)"
	- Developer Tools: 
		  Network > app.js / config.js / version.js 

1. Como es un ataque de deserialización colocar en 'PORT' el puerto del servicio de 'MS .NET Remoting services', la mayoría de las veces es '17001'
2. En 'HOST' solo colocar la 'IP' sin importar el puerto donde se este ejecutando el SmarterMail 
```

```bash 
❯ rlwrap nc -nlvp 4444    # En escucha para recibir la Revershell del exploit 
```