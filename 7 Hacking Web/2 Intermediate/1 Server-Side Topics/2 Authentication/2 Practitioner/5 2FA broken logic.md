# Lógica rota en 2FA

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En este lab se demuestra cómo un sistema de autenticación en dos pasos puede ser comprometido si la lógica de verificación no está correctamente implementada. Aunque la aplicación genera un código 2FA único para cada usuario, el endpoint que lo valida permite especificar explícitamente para qué usuario se verifica el código, lo que abre la puerta a ataques dirigidos.

Primero, se genera un código 2FA válido para un usuario manipulando el parámetro 'verify'. Luego, se envia un código inválido en tu propio flujo de autenticación para reutilizar el endpoint de validación, y mediante Burp 'Intruder' se realiza un ataque de fuerza bruta sobre el código del usuario. Una vez se encuentra el código correcto, se obtiene acceso a su cuenta y se podrá visitar su panel.
```

```bash 
# Generar el diccionario desde 0000 - 9999

❯ seq -w 0 9999 > codes 


Notas:
	1. En 'Intruder' de agrega una regex con la parte de 'Incorrect security code'
	2. Se deselecciona el campo 'URL-encode these charactes'
```