# Secuencias traversal eliminadas sin recursión

Tags: #DirectoryPathTraversal #BurpSuite 

```bash 
En este lab la aplicación intenta mitigar el path traversal eliminando las secuencias '../', pero lo hace de forma no recursiva. Esto permite evadir el filtro usando variantes como '….//', que tras la normalización del sistema de archivos siguen interpretándose como una subida de directorio.

Usando '….//….//etc/passwd', logramos acceder a archivos sensibles como ‘**/etc/passwd**‘, demostrando la debilidad de este enfoque de filtrado.
```

```bash 
	?file=....//....//....//....//etc/passwd      # Mirar un archivo en específico 
```