# BurpSuite 

Tags: #BurpSuite #Escaneo  #Web #Enumeracion #Proxy 

**BurpSuite** es una herramienta de prueba de penetración utilizada para encontrar vulnerabilidades de seguridad en aplicaciones web. Es una de las herramientas de prueba de penetración más populares y utilizadas en la industria de la seguridad informática. BurpSuite se compone de varias herramientas diferentes que se pueden utilizar juntas para identificar vulnerabilidades en una aplicación web.

Las principales herramientas que componen BurpSuite son las siguientes:

-   **Proxy**: Es la herramienta principal de BurpSuite y actúa como un intermediario entre el navegador web y el servidor web. Esto permite a los usuarios interceptar y modificar las solicitudes y respuestas HTTP y HTTPS enviadas entre el navegador y el servidor. El Proxy también es útil para la identificación de vulnerabilidades, ya que permite a los usuarios examinar el tráfico y analizar las solicitudes y respuestas. Además de, abrir su propio navegador para ir capturando las peticiones. 
-   **Scanner**: Es una herramienta de prueba de vulnerabilidades automatizada que se utiliza para identificar vulnerabilidades en aplicaciones web. El Scanner utiliza técnicas de exploración avanzadas para detectar vulnerabilidades en la aplicación web, como inyecciones SQL, cross-site scripting (XSS), vulnerabilidades de seguridad de la capa de aplicación (OSWAP Top 10) y más.
-   **Repeater**: Es una herramienta que permite a los usuarios reenviar y repetir solicitudes HTTP y HTTPS. Esto es útil para probar diferentes entradas y verificar la respuesta del servidor. También es útil para la identificación de vulnerabilidades, ya que permite a los usuarios probar diferentes valores y detectar respuestas inesperadas.
-   **Intruder**: Es una herramienta que se utiliza para automatizar ataques de fuerza bruta. Los usuarios pueden definir diferentes payloads para diferentes partes de la solicitud, como la URL, el cuerpo de la solicitud y las cabeceras. Posteriormente, Intruder automatiza la ejecución de las solicitudes utilizando diferentes payloads y los usuarios pueden examinar las respuestas para identificar vulnerabilidades.
-   **Comparer**: Es una herramienta que se utiliza para comparar dos solicitudes HTTP o HTTPS. Esto es útil para detectar diferencias entre las solicitudes y respuestas y analizar la seguridad de la aplicación.

## Iniciar BurpSuite 

```bash
❯ burpsuite &> /dev/null & disown                   # Iniciamos el BurpSuite y lo independizamos
❯ BurpSuiteCommunity &> /dev/null & disown
```

## Comandos

```bash 
# 'Click derecho > Copy to file' Para guardar la intercepción del **Proxy** en un archivo **.req** y así usarlo con la herramienta SqlMap'

❯ 'Ctrl + I' Para mandar lo del proxy al intruder (I=i)
❯ 'Ctrl + r' Para mandar lo del proxy al repeater
❯ 'Ctrl + u' Para url encodear la data de las peticiones
❯ 'Ctrl + Shift + u' Para url decodear la data de las peticiones

'/usr/share/wordlists/fasttrack.txt'  # Diccionario para passwd que generalmente traen los sitios Web 
```

## Rastreo pasivo

```bash 
1. Necesitamos tener el 'Proxy' activado en la web 
2. Debemos de activar en 'Burpsuite' en la pestaña de 'Dashboard' la parte de -> 'Capturing (Live passive crawl from Proxy (all trafic))'
3. Toda la data la podremos ver en la parte del 'Proxy' -> 'HTTP History' 
4. Eso funcionara aunque tengamos el 'Proxy' desactivado
```

# Enumeración con Fuerza Bruta

## Intruder: Sniper

```bash 
# Realiza ataques de fuerza bruta o diccionario. El Sniper nos permite agregar un Payload y probar con un solo parámetro. 
.
	Payload set: 1
	Payload Type: Simple list

# Debemos de agregar nuestras propias palabras o cargar una lista.
# Este ataque lo podemos hacer en el método 'GET' de Burpsuite

GET /$name$ HTTP/1.1        # Agregamos al directorio principal el parametro a usar, en este caso 'name'
```

## Intruder: Battering Ram

```bash 
# Permite tomar tantas posiciones como queramos, pero cada uno de los espacios serán probados con la misma palabra.
.
	Payload set: 1
	Payload Type: Simple list

# Aquí podemos cargar el diccionario llamado fasttrack.txt
# Lo que hace es que toma la misma palabra y la coloca en todos las posiciones que habilitamos. 
```
## Intruder: Pitchfork

```bash 
# Permite tener dos posiciones en las cuales podemos tener una lista en cada una o generar un ataque de fuerza bruta cada una.
.
	Payload set: 1
	Payload Type: Simple list

# El primer payload se refiere a que ahí podemos colocar una lista de usuarios

.
	Payload set: 2
	Payload Type: Simple list

# El segundo payload se refiere a que ahí podemos colocar una lista que podrían ser de passwd.
# Probara las posiciones al mismo tiempo, por lo que el usuario y la passwd correctas se deben de encontrar en la misma posición, de lo contrario no podría encontrarlo aunque si existan en el diccionario.
```
## Intruder: Cluster Bomb

```bash 
# Permite escoger dos posiciones (2 payloads).
.
	Payload set: 1
	Payload Type: Simple list

# El primer payload se refiere a que ahí podemos colocar una lista de usuarios

.
	Payload set: 2
	Payload Type: Simple list

# El segundo payload se refiere a que ahí podemos colocar una lista que podrían ser de passwd.
# En este caso probara primero el primer elemento de la lista 1 y después probara todos los elementos de la lista 2. Pasara a otro elemento de la lista 1 y después probara todos los elementos de la lista 2 y así sucesivamente. 
```

