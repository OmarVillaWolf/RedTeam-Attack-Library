# Cifrar un disco 

Tags: #Hacking #Criptografia #Ñ

## VeraCrypt 

```bash 
# Windows Tool para esconder o cifrar la partición de discos 
❯ VeraCrypt.exe    # Se puede crear una partición externa y una interna, cada una con su propia password

Notas:
	1. Creamos un volumen en 'Create Volume' y dar click en 'Create an encrypted file container'
	2. Seleccionar 'Standar VeraCrypt volume'
	3. Seleccionar un archivo colocando la ruta y el nombre del archivo 
	4. Dejar la misma parte de 'Encryption Options'
	5. Crear un volumen de 50 Mb y agregar una password 
	6. Seleccionar 'Random Pool' y mover el raton muchas veces por 30 segundos y dar click en 'Format'
	7. Dar click en 'Exit'
	8. Seleccionar un disco, en este caso 'I', buscar el archivo y dar click en 'Mount'
	9. Si el archivo contiene una password la debemos de colocar 

Nota2:
	1. Para ver el contenido de un archivo, se debe de montar el volumen seleccionando el disco y el archivo 
		1. Si tiene password colocarla 
	2. El volumen montado se puede ver en Windows 
```
