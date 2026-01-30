# LLMNR Poisoning 

Tags: #AD #Attacks #Responder #LLMNRPoisoning 

**Responder** es una herramienta de análisis de redes que se utiliza principalmente para llevar a cabo ataques de envenenamiento de caché de protocolos de red, como SMB, LLMNR, NetBIOS, y otros. Cuando se ejecuta en un Active Directory (AD), su objetivo principal es capturar y manipular las solicitudes de autenticación de la red para obtener credenciales de usuarios, como nombres de usuario y contraseñas.

Específicamente, **Responder** realiza los siguientes ataques en un entorno de Active Directory (AD):

1. **Envenenamiento LLMNR (Link-Local Multicast Name Resolution)**: Este protocolo es utilizado por las máquinas Windows para resolver nombres de host en redes locales cuando no pueden contactar un servidor DNS. Responder puede engañar a los equipos en la red para que hagan solicitudes de resolución de nombres a la máquina que ejecuta Responder, permitiéndole capturar hashes de contraseñas.
    
2. **Envenenamiento NetBIOS (NBT-NS)**: Al igual que LLMNR, NetBIOS se utiliza para resolver nombres de red en sistemas Windows. Responder también puede responder a las solicitudes de resolución de nombres NetBIOS y capturar las credenciales de autenticación, como los hashes de contraseñas.
    
3. **Captura de autenticaciones SMB (Server Message Block)**: SMB es un protocolo utilizado para compartir archivos e impresoras en redes. Si un equipo intenta autenticar una conexión SMB y no tiene una conexión previa establecida, Responder puede capturar las credenciales de autenticación enviadas en la red.
    
4. **Ataque de NTLM relay**: Si Responder captura los hashes de NTLM (utilizados para la autenticación en redes Windows), puede intentar realizar un ataque de NTLM relay, donde los hashes capturados se reenvían a otros servicios para autenticar usuarios sin necesidad de descifrar las contraseñas.


```bash 
❯ responder help                # Manual de ayuda para ejecutar el comando 
❯ responder -I eth0 -dwPv        # Ejecutar el responder para recibir las solicitudes de la red (Shares)
	# d --DHCP-DNS = Inyecta un servidor DNS en el DHCP reponder
	# w --wpad = Inicia el WPAD proxy server
	# P --ProxyAuth = Fuerza la autenticacion NTLM para el proxy.   
	# v = Verbosidad  
```

