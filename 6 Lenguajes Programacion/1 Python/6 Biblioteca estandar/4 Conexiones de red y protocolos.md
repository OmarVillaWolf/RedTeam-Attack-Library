# Conexiones de red y protocolos 

Tags: #Python3 #Socket

Los protocolos **TCP** (Transmission Control Protocol) y **UDP** (User Datagram Protocol) son fundamentales en la comunicación de red, y la librería ‘**socket**‘ en Python proporciona las herramientas necesarias para interactuar con ellos. Aquí tienes una descripción detallada de ambos protocolos y el uso de ‘**socket**‘:

**Protocolo TCP**
- **Orientado a la Conexión**: TCP es un protocolo orientado a la conexión, lo que significa que establece una conexión segura y confiable entre el emisor y el receptor antes de la transmisión de datos.
- **Fiabilidad y Control de Flujo**: Garantiza la entrega de datos sin errores y en el mismo orden en que se enviaron. También gestiona el control de flujo y la corrección de errores.
- **Uso en Aplicaciones**: Es ampliamente utilizado en aplicaciones que requieren una entrega fiable de datos, como navegadores web, correo electrónico, y transferencia de archivos.

**Protocolo UDP**
- **No Orientado a la Conexión**: A diferencia de TCP, UDP es un protocolo no orientado a la conexión. Envía datagramas (paquetes de datos) sin establecer una conexión previa.
- **Rápido y Ligero**: UDP es más rápido y tiene menos sobrecarga que TCP, ya que no verifica la llegada de paquetes ni mantiene el orden de los mismos.
- **Uso en Aplicaciones**: Ideal para aplicaciones donde la velocidad es crucial y se pueden tolerar algunas pérdidas de datos, como juegos en línea, streaming de video y voz sobre IP (VoIP).

**Librería ‘socket’ en Python**
La librería ‘**socket**‘ en Python es una herramienta esencial para la programación de comunicaciones en red. Permite a los desarrolladores crear aplicaciones que pueden enviar y recibir datos a través de la red, ya sea en una red local o a través de Internet. Aquí tienes una visión general de la librería ‘**socket**‘:

- **Creación de sockets**: La librería ‘**socket**‘ proporciona clases y funciones para crear sockets, que son puntos finales de comunicación. Puedes crear sockets tanto para el protocolo TCP (Transmission Control Protocol) como para UDP (User Datagram Protocol).
- **Conexiones TCP**: Puedes utilizar ‘**socket**‘ para establecer conexiones TCP, que son conexiones confiables y orientadas a la conexión. Esto es útil para aplicaciones que requieren transferencia de datos confiable, como la transmisión de archivos o la comunicación cliente-servidor.
- **Comunicación UDP**: La librería ‘**socket**‘ también admite la comunicación mediante UDP, que es un protocolo de envío de mensajes sin conexión. Es adecuado para aplicaciones que necesitan una comunicación rápida y eficiente, como juegos en línea o aplicaciones de transmisión de video en tiempo real.
- **Funciones de envío y recepción**: Puedes utilizar métodos como ‘**send()**‘ y ‘**recv()**‘ para enviar y recibir datos a través de sockets. Esto te permite transferir información entre dispositivos de manera eficiente.
- **Gestión de conexiones**: La librería ‘**socket**‘ incluye métodos como ‘**bind()**‘ para asociar un socket a una dirección y puerto específicos, y ‘**listen()**‘ para poner un socket en modo de escucha, lo que le permite aceptar conexiones entrantes.
- **Conexiones cliente-servidor**: Con ‘**socket**‘, puedes crear aplicaciones cliente-servidor, donde un programa actúa como servidor esperando conexiones entrantes y otro actúa como cliente para conectarse al servidor.

**Manejadores de contexto con conexiones**
Los manejadores de contexto (‘**with**‘ en Python) se utilizan para garantizar que los recursos se gestionen de manera adecuada. En el contexto de las conexiones de socket, un manejador de contexto se encarga de abrir y cerrar el socket de manera segura. Esto evita que los recursos del sistema se queden en uso indefinidamente y asegura una gestión adecuada de las conexiones.

**Diferencias entre send y sendall**
- **send(data)**: El método ‘**send()**‘ se utiliza para enviar una cantidad específica de datos a través del socket. Puede no enviar todos los datos en una sola llamada y puede ser necesario llamarlo múltiples veces para enviar todos los datos.
- **sendall(data)**: El método ‘**sendall()**‘ se utiliza para enviar todos los datos especificados a través del socket. Realiza llamadas repetidas a ‘**send()**‘ internamente para garantizar que todos los datos se envíen por completo sin pérdidas.

La elección entre ‘**send**‘ y ‘**sendall**‘ depende de si se necesita garantizar la entrega completa de los datos o si se permite que los datos se envíen en fragmentos. send puede enviar datos en fragmentos, mientras que sendall garantiza que todos los datos se envíen sin pérdida.

El uso de hilos con ‘**threading**‘ en Python es crucial para gestionar múltiples clientes en aplicaciones de red que utilizan sockets, especialmente en servidores.

Aquí te explico en detalle por qué es necesario:

- **Concurrencia y Manejo de Múltiples Conexiones**: Los servidores de red a menudo necesitan manejar múltiples conexiones de clientes simultáneamente. Sin hilos, un servidor tendría que atender a un cliente a la vez, lo cual es ineficiente y no escalable. Con ‘**threading**‘, cada cliente puede ser manejado en un hilo separado, permitiendo al servidor atender múltiples solicitudes al mismo tiempo.
- **Bloqueo de Operaciones de Red**: Las operaciones de red, como ‘**recv**‘ y ‘**accept**‘, suelen ser bloqueantes. Esto significa que el servidor se detendrá en estas operaciones hasta que se reciba algo de la red. Si un cliente se demora en enviar datos, esto puede bloquear todo el servidor, impidiendo que atienda a otros clientes. Con hilos, cada cliente tiene su propio hilo de ejecución, por lo que la lentitud o el bloqueo de uno no afecta a los demás.
- **Escalabilidad**: Los hilos permiten a los desarrolladores crear servidores que escalan bien con el número de clientes. Al asignar un hilo a cada cliente, el servidor puede manejar muchos clientes a la vez, ya que cada hilo ocupa relativamente pocos recursos del sistema.
- **Simplicidad en el Diseño de la Aplicación**: Aunque existen modelos alternativos para manejar la concurrencia (como la programación asíncrona), el uso de hilos puede simplificar el diseño y la lógica de la aplicación. Cada hilo puede ser diseñado como si estuviera manejando solo un cliente, lo que facilita la programación y el mantenimiento del código.
- **Uso Eficiente de Recursos de CPU en Sistemas Multi-Core**: Los hilos pueden ejecutarse en paralelo en diferentes núcleos de un procesador multi-core, lo que permite a un servidor aprovechar mejor el hardware moderno y manejar más eficientemente varias conexiones al mismo tiempo.
- **Independencia y Aislamiento de Clientes**: Cada hilo opera de manera independiente, lo que significa que un problema en un hilo (como un error o una excepción) no necesariamente afectará a los demás. Esto proporciona un aislamiento efectivo entre las conexiones de los clientes, mejorando la robustez del servidor.

## Server para TCP

```python 
#!/usr/bin/env python3 

import socket 

def start_server():
	host = 'localhost'
	port = 1234

	with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s: # Para cerrar el socket automaticamente 
		# AF_INET = Controla o define la familia de direcciones que acepta con IPv4
		# SOCK_STREAM = Indica que usaremos conexiones TCP
		s.bind((host, port))   # Nos ponemos en escucha en local por el puerto '1234'
		print(f"Servidor en escucha en: {host}:{port}")
		s.listen(1)            # Limite de conexiones simultaneas
		conn, addr = s.accept()

		with conn:
			print(f"\n[+] Se ha conectado un nuevo cliente: {addr}")
			while True:
				data = conn.recv(1024) # Bytes a recibir del lado del cliente 
				if not data:
					break
				conn.sendall(data)

start_server()
```

## Client TCP

```python 
#!/usr/bin/env python3 

import socket 

def start_client():
	host = 'localhost'
	port = 1234

	with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s: 
		s.connect((host, port))
		s.sendall(b"Hola, servidor!")        # Mandamos el mensaje en formato bytes 'b'
		data = s.recv(1024)                  # Recibimos el mensaje del servidor

	print(f"\n[+] Mensaje recibido del servidor: {data.decode()}")  # Debemos de encodear en bytes 

start_client()
```

## Server para UDP

```python 
#!/usr/bin/env python3 

import socket 

def start_udp_server():
	host = 'localhost'
	port = 1234

	with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
	# SOCK_DGRAM = Indica que usaremos conexiones UDP
		s.bind((host, port))   # Nos ponemos en escucha en local por el puerto '1234'
		print(f"\n[+] Servidor UDP en escucha en: {host}:{port}")

		while True:
			data, addr = s.recvfrom(1024)    # Recibir datos del cliente 
			print(f"\n[+] Mensaje enviado por el cliente: {data.decode()}")
			print(f"[+] Informacion del cliente que nos ha enviado el mensaje: {addr}")

start_udp_server()
```

## Client UDP

```python 
#!/usr/bin/env python3 

import socket 

def start_udp_client():
	host = 'localhost'
	port = 1234

	with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s: 
		message = "Hola se está tensando muchísimo".encode("utf-8") # Indicar caracteres especiales con tilde
		s.sendto(message, (host, port))         # Mandamos el mensaje en formato bytes 'b'

start_udp_client()
```