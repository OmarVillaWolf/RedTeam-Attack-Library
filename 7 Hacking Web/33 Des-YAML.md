# Python – Ataque de Deserialización Yaml (DES-Yaml)

Tags: #Des-YAML #OWASP  #Explotacion 

Un **Ataque de Deserialización Yaml** (**DES-Yaml**) es un tipo de vulnerabilidad que puede ocurrir en aplicaciones Python que usan **YAML** (**Yet Another Markup Language**) para serializar y deserializar objetos.

La vulnerabilidad se produce cuando un atacante es capaz de controlar la entrada YAML que se pasa a una función de deserialización en la aplicación. Si el código de la aplicación no valida adecuadamente la entrada YAML, puede permitir que un atacante inyecte código malicioso en el objeto deserializado.

Una vez que el objeto ha sido deserializado, el código malicioso puede ser ejecutado en el contexto de la aplicación, lo que puede permitir al atacante tomar el control del sistema, acceder a datos sensibles, o incluso ejecutar código remoto.

Los atacantes pueden aprovecharse de las vulnerabilidades de DES-Yaml para realizar ataques de denegación de servicio (DoS), inyectar código malicioso, o incluso tomar el control completo del sistema.

El impacto de un Ataque de Deserialización Yaml depende del tipo y la sensibilidad de los datos que se puedan obtener, pero puede ser muy grave. Por lo tanto, es importante que los desarrolladores de aplicaciones Python validen y filtren adecuadamente la entrada YAML que se pasa a las funciones de deserialización, y que utilicen técnicas de seguridad como la limitación de recursos para prevenir ataques DoS y la desactivación de la deserialización automática de objetos no confiables.

A continuación, se os proporciona el proyecto de Github correspondiente al laboratorio que estaremos desplegando en esta clase para practicar esta vulnerabilidad:

- **SKF-LABS DES-Yaml**: [https://github.com/blabla1337/skf-labs/tree/master/python/DES-Yaml](https://github.com/blabla1337/skf-labs/tree/master/python/DES-Yaml)


## YAML 

En este lab podremos hacer ataques de Deserialización YAML. Podemos observar que en la URL tenemos una cadena en base64.

![](Pasted%20image%2020230528173127.png)

Si le hacemos un decode al código en base64 obtendremos lo que sale en la pagina web. 

![](Pasted%20image%2020230528173558.png)

Ahora podríamos colocar algo con la estructura en YAML y codificarlo en base64 para después colocarlo en la URL.

![](Pasted%20image%2020230528173659.png)


Para este tipo de ataques, nosotros podremos crear una estructura YAML que al ser de-serializada e interpretada nos permita ejecutar comandos.  Esto siempre y cuando no exista una sanitización. 

* [Yaml-Attack](https://www.pkmurphy.com.au/isityaml/)

Colocamos lo siguiente en la estructura en YAML y después lo codificamos en base64 para colocarlo en la URL.

![](Pasted%20image%2020230528175005.png)

Si todo sale bien después de codificarlo en base64 y colocarlo en la URL podríamos ver el comando id en la pagina web. 

![](Pasted%20image%2020230528175209.png)

