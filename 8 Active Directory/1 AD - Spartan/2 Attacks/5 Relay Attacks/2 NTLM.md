# NTLM 

Tags: #AD #Relay #NTLM 

NTLM, que significa NT LAN Manager, es un protocolo de autenticación utilizado en redes Microsoft Windows. Desde la perspectiva de un pentester, entender NTLM es crucial debido a su amplio uso y a las vulnerabilidades asociadas con su implementación y diseño. Aquí te presento una explicación detallada adaptada a las necesidades de un pentester:

## Descripción General de NTLM

```bash 
1. Historia y Uso: NTLM fue introducido originalmente con Windows NT. A pesar de que versiones más seguras (como Kerberos) están disponibles en versiones más recientes de Windows, NTLM todavía se usa ampliamente, particularmente en sistemas más antiguos o en situaciones donde Kerberos no puede ser implementado.
    
2. Funcionamiento del Protocolo: NTLM es un protocolo de autenticación basado en desafío-respuesta. Cuando un cliente intenta acceder a un recurso en la red, el servidor (o el servicio) desafía al cliente a autenticarse. El cliente responde con un hash de la contraseña del usuario, que el servidor verifica contra su propia copia del hash.
```

## Componentes Clave para Pentesters

```bash 
1. Hashes NTLM: Estos son los hashes de las contraseñas de los usuarios almacenados en los sistemas Windows. Son un objetivo clave en el pentesting, ya que pueden ser extraídos y, potencialmente, crackeados o reutilizados en ataques de paso de hash.
    
2. Autenticación NTLMv1 y NTLMv2: Existen dos versiones principales de NTLM - NTLMv1, que es menos segura y más susceptible a ataques, y NTLMv2, que es más robusta y presenta características adicionales para prevenir ciertos tipos de ataques.
    
3. Relay y Responder Attacks: Los ataques de retransmisión NTLM aprovechan la forma en que NTLM maneja la autenticación para interceptar y reutilizar credenciales. Herramientas como Responder se utilizan para capturar hashes NTLM en la red.
    
4. Pass-the-Hash (PtH): Este es un método para autenticarse en una red utilizando el hash NTLM de un usuario en lugar de su contraseña en texto claro.
```