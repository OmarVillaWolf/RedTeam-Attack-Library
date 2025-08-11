# Email OSINT

Tags: #OSINT 

```bash 
❯ https://hunter.io/                        # Buscar correos empresariales 
❯ https://phonebook.cz/                     # Buscar dominios, correos y URLs
❯ https://www.voilanorbert.com/             # La extensión se encuentra abajo y busca correos empresariales 
❯ https://tools.emailhippo.com/             # Valida si el correo existe
❯ https://email-checker.net/                # Valida si el correo existe 

# Extensión 
❯ https://chromewebstore.google.com/detail/clearbit-connect-free-ver/pmnhcgfcafcnkbengdcanjablaabjplo?hl=en

# Otra forma de validar si un correo existe en gmail es tratando de iniciar la sesion solo colocando el correo, despues al decir que olvidaste la passwd te 'muestra' otro correo ligado, el cual si existe su passwd filtrada se podria ingresar. 
```

```bash 
❯ https://mxtoolbox.com/                    # Poder analizar el encabezado del email 
```

## Kali tools 

```bash 
❯ python infoga.py --domain domain.com --source all --breach -v 2 --report ../domain.txt  # Recolecta info de email de un dominio especifico 
```

```bash 
❯ https://github.com/hmaverickadams/DeHashed-API-Tool    # Descargar Dehashed para buscar usuarios, IP, emails y mas

❯ python3 dehashed_parser.py -e domain.com --only-passwords   # Muestra datos filtradas 
❯ python3 dehashed_parser.py -u user                          # Muestra info del usuario como email, passwd-hash, etc...
```