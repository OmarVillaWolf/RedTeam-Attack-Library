# Cupp

Tags: #Cupp #Diccionario #Cewl #Crunch 

* [Cupp](https://esgeeks.com/como-utilizar-cupp/)

La herramienta **CUPP** (_Common User Passwords Profiler_) es una utilidad en Kali Linux diseñada para generar diccionarios de contraseñas personalizadas. Su objetivo es ayudar a los atacantes éticos o investigadores de seguridad a crear listas de posibles contraseñas basadas en la información personal de una víctima (como nombre, cumpleaños, hobbies, etc.), facilitando así ataques de fuerza bruta o de diccionario.

## Características principales de CUPP:

1. **Generación de diccionarios personalizados**: CUPP permite crear un archivo de diccionario basado en la información específica que el usuario proporciona sobre su objetivo.
2. **Posibilidad de agregar datos adicionales**: Además de los datos básicos, se pueden agregar variaciones, combinaciones o patrones comunes que podrían estar en las contraseñas de las personas.
3. **Integración con bases de datos públicas**: CUPP también puede descargar diccionarios de contraseñas populares de fuentes públicas para ampliar las posibilidades del ataque.

```bash 
❯ cupp -i      # Crear el diccionario con la recopilacion de palabras 
```

## Cewl 

```bash 
❯ cewl -w <passwd> http://<IP> --with-numbers      # Nos crea un diccionario con las palabras existentes de una url

	# w = Queremos un diccionario que se llamara 'passwd'
	# with-numbers = Acepta palabras con numeros, ademas de palabras con solo letras
```

## Crunch 

```bash 
❯ crunch 5 15 abcde12345            # Crea una diccionario desde 5 hasta 15 caracteres haciendo todas las posibles combinaciones, cabe mencionar que los diccionarios son muy pesados
```