# Inyección de comandos, caso simple

Tags: #CommandInjection #RCE #BurpSuite 

```bash 
En este lab se aprovecha una vulnerabilidad de inyección de comandos del sistema operativo en el verificador de stock. Al modificar el parámetro ‘storeID’ con '1|whoami', logramos que el servidor ejecute el comando 'whoami', revelando así el usuario actual del sistema.
```

```bash 
Separador de comandos:
1. ';' = Fin de un comando, inicio de otro comando
2. '|' = Pasa el output del primer comando como input del segundo 
3. '&&' = Ejecuta el segundo comando si el primero termina con éxito 
4. '||' = Ejecuta el segundo solo si el primero falla
5. '$()' = Ejecutar un comando dentro de otro y sustituye el resultado en el lugar donde está
```

```bash 
❯ cat /etc/shells       # Mirar todas las shells validas 

❯ test ; whoami         # Ejecutar el primer y luego el segundo comando   
```