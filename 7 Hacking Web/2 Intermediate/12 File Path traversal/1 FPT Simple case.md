# Directory Path traversal

Tags: #DirectoryPathTraversal #BurpSuite 

```bash 
Un directory traversal consiste en explotar una vulnerabilidad informática que ocurre cuando no existe suficiente seguridad en cuanto a la validación de un usuario, permitiéndole acceder a cualquier tipo de directorio superior sin ningún control.
```

## Path traversal, caso simple

```bash 
La aplicación permite cargar imágenes de productos a través de un parámetro 'filename'. Interceptamos esta petición y abusamos de la falta de validación para escalar directorios utilizando secuencias '../'.

De esta forma, conseguimos acceder a archivos sensibles del sistema, como '/etc/passwd', demostrando una vulnerabilidad de path traversal en su forma más básica.
```

```bash 
	?file=../../../../etc/passwd      # Mirar un archivo en específico 
```