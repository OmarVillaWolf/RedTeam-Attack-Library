# Protección rota: bloqueo por IP

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En este lab se explotará una debilidad lógica en la protección contra fuerza bruta basada en IP. La aplicación bloquea temporalmente una IP tras tres intentos fallidos consecutivos, pero este contador se reinicia si se realiza un inicio de sesión válido antes de alcanzar el límite.

Se aprovecha esta lógica para realizar un ataque tipo pitchfork en Burp Intruder, alternando peticiones con un usuario y contraseñas correctas, con intentos de acceso al usuario objetivo usando contraseñas de un diccionario. Este patrón evita que el sistema bloquee tu IP y te permite forzar la contraseña del usuario sin interrupciones. Al identificar la contraseña correcta, se inicia sesión en la cuenta. 
```

```bash 

```