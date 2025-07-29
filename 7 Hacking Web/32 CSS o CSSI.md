# Inyecciones CSS (CSSI)

Tags: #CSS #CSSI #OWASP #Explotacion 

Las **Inyecciones CSS** (**CSSI**) son un tipo de vulnerabilidad web que permite a un atacante inyectar código CSS malicioso en una página web. Esto ocurre cuando una aplicación web confía en entradas no confiables del usuario y las utiliza directamente en su código CSS, sin realizar una validación adecuada.

El código CSS malicioso inyectado puede alterar el estilo y diseño de la página, permitiendo a los atacantes realizar acciones como la **suplantación de identidad** o el **robo de información confidencial**.

Las Inyecciones CSS (CSSI) pueden ser utilizadas por los atacantes como un vector de ataque para explotar vulnerabilidades de **Cross-Site Scripting** (**XSS**). Imagina que una aplicación web permite a los usuarios introducir texto en un campo de entrada que se muestra en una página web. Si el desarrollador de la aplicación no valida y filtra adecuadamente el texto introducido por el usuario, un atacante podría inyectar código malicioso en el campo de entrada, incluyendo código Javascript.

Si el código CSS inyectado es lo “suficientemente complejo”, puede hacer que el navegador web interprete el código como si fuera código JavaScript. Esto significa que el código CSS malicioso puede ser utilizado para inyectar código JavaScript en la página web, lo que se conoce como una inyección de JavaScript inducida por CSS (CSS-Induced JavaScript Injection).

Una vez que el código JavaScript ha sido inyectado en la página, este puede ser utilizado por el atacante para realizar un ataque de Cross-Site Scripting (XSS). Una vez en este punto, el atacante podría ser capaz de inyectar un script malicioso que robe las credenciales del usuario o que los redirija a una página web falsa, entre otros muchos posibles vectores.

A continuación, se os proporciona el enlace directo del proyecto de Github, el cual utilizamos en esta clase para desplegar el entorno vulnerable con el objetivo de practicar las inyecciones CSS:

- **SKF-LABS CSSI**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/CSSI](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/CSSI)


## CSS o CSSI

Tenemos este laboratorio en el cual, si colocamos un color, al momento de darle 'Submit botton' nos cambiara el color a la parte de 'COLOR'.

![](Pasted%20image%2020230528171414.png)


En el código fuente (Crtl + u), podemos observar que el color que coloquemos nos los muestra ahí. 

![](Pasted%20image%2020230528171722.png)


Por lo que podríamos colocar lo siguiente
```html
❯ blue}</style><script>alert("XSS")</script>

	# Cerramos la etiqueta Style
	# Colocamos nuevas etiquetas para acontecer un XSS, por lo que nos deberia de mostrar en la pantalla una alerta 
```

![](Pasted%20image%2020230528172252.png)

Si miramos el código fuente. 
![](Pasted%20image%2020230528172407.png)




