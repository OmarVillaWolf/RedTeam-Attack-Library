# Relay Attacks 

Tags: #AD #Windows #Relay 

Los "Relay Attacks", conocidos también como ataques de retransmisión, son una forma de ataque de intermediario ("Man-in-the-Middle" o MitM) que ocurre cuando un atacante intercepta (y a menudo modifica) la comunicación entre dos partes sin que ninguna de ellas sea consciente de la interferencia.

## Detalles técnicos del Relay Attack

```bash 
En el contexto de Active Directory (AD) y la autenticación NTLM, un ataque de retransmisión se aprovecha de la forma en que NTLM maneja la autenticación de dos partes sin necesidad de que el cliente conozca la contraseña en texto claro del usuario. Aquí está el alto nivel de detalle:

1. Autenticación NTLM:
    
    - Negociación (Negotiate): El cliente inicia la autenticación y expresa su capacidad para comunicarse en NTLM.
    - Desafío (Challenge): El servidor responde con un "desafío", que es un nonce (número que se usa una sola vez).
    - Respuesta (Response): El cliente responde con una "respuesta al desafío", que incluye el hash NTLM del usuario y datos del desafío encriptados con ese hash.
  

2. Intercepción:
    - En un ataque de retransmisión, el atacante se coloca entre el cliente y el servidor.
    - El atacante intercepta el paquete de negociación y el paquete de respuesta al desafío.
 

3. Retransmisión:
    - El atacante retransmite la respuesta al desafío a otro servidor que también acepta la autenticación NTLM.
    - El atacante no necesita conocer la contraseña; simplemente está usando la respuesta al desafío válida que recibió del cliente.
```

## Escenario de un Relay Attack

```bash 
Imaginar un escenario en una empresa donde un atacante ha logrado posicionar un dispositivo en la misma red que un servidor de archivos y un cliente (la víctima) que regularmente accede a ese servidor.

1. Preparación:
    - El atacante configura una herramienta de escucha para interceptar las solicitudes de autenticación NTLM, como `Responder` o `Inveigh`.
    - También configura una herramienta como `ntlmrelayx` para retransmitir las credenciales interceptadas.


2. Ejecución:
    - El atacante engaña al cliente (quizás a través de un correo de phishing o una inyección en una web visitada por la víctima) para que realice una solicitud a una máquina controlada por el atacante.
    - Cuando el cliente intenta autenticarse, el atacante intercepta la comunicación y captura el paquete de respuesta al desafío.
  

3. Retransmisión y Compromiso:
    - De inmediato, el atacante retransmite esa respuesta al servidor de archivos al que el cliente estaba tratando de acceder originalmente.
    - El servidor de archivos, al recibir una respuesta al desafío válida, asume que la solicitud es legítima y concede al atacante acceso.
    - El atacante ahora tiene acceso al servidor de archivos con los mismos derechos que la víctima, lo que podría incluir el acceso a archivos sensibles, la capacidad de instalar software malicioso o incluso robar más credenciales.
```

## Condiciones para la Ejecución del Ataque

```bash 
1. SMB Signing Disabled: Para retransmitir exitosamente, SMB Signing debe estar deshabilitado en el servidor destino, ya que de lo contrario, no aceptará una respuesta retransmitida.

❯ nxc smb IP      # Verificar si 'signing:False'


2.LLMNR/NBT-NS Poisoning: Utilizando técnicas de envenenamiento de LLMNR y NBT-NS, el atacante puede hacer que las solicitudes de autenticación se dirijan a su máquina.

❯ nvim /etc/responder.Responder.conf     # Modificar el 'Responder.conf', cambiar el SMB y HTTP a 'Off'

❯ responder -I eth0 -dwPvF               # Ejecutar el responder y verificar los cambios

	# d --DHCP-DNS = Inyecta un servidor DNS en el DHCP reponder
	# w --wpad = Inicia el WPAD proxy server
	# P --ProxyAuth = Fuerza la autenticacion NTLM para el proxy.   
	# v = Verbosidad  
	# F = Forzar la autenticación del cliente 
```