# Bypass básico de 2FA

Tags: #GestionAutenticacion #BurpSuite 

```bash 
En este lab se ve cómo un sistema de 2FA mal implementado puede ser trivialmente eludido. Tras la autentición con las credenciales, el sistema solicita el código de verificación.

Sin embargo, al modificar manualmente la URL hacia la sección de perfil, se consegue saltar la segunda verificación y acceder a la cuenta. Un fallo grave derivado de no proteger rutas sensibles tras el login.
```

```bash 
	https://web.com/my-account      # Url despues de colocar el 2FA
```