# Searchsploit

Tags: #Enumeracion #SearchSploit

* [Exploit-db](https://www.exploit-db.com/)
* [Rapid7](https://www.rapid7.com/db/)

Podemos usar esta herramienta para buscar exploit y ver si alguno nos puede ayudar a la explotación del sistema

```bash
❯ searchsploit -u                             # Actualizar la base de datos 
❯ searchsploit ❮exploit_name❯ -w              # Muestra la url de donde obtiene el exploit
❯ searchsploit -c ❮exploit_name❯              # Podemos escribir un exploit con 'Case Sensitive'
❯ searchsploit ❮exploit_name❯                 # Para buscar un exploit

❯ searchsploit -x ❮exploit_name❯              # Para ver el contenido del exploit 
	# x = examin

❯ searchsploit -m ❮exploit_name❯              # Para descargarnos o copiarnos el exploit .py/.txt 
	# m = move

❯ searchsploit -t ❮exploit_name❯              # Para buscar un exploit que contenga ese titulo 
❯ searchsploit -e "exploit_name"              # Para hacer una busqueda exacta
```

## Compilar en C, C++, C# 

* [Exploits-database](https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/tree/main/bin-sploits?ref_type=heads)
```bash 
❯ apt install mingw-w64 
❯ apt install gcc

❯ i686-w64-mingw32-gcc <exploit.c> -o exploit               # Compilación para exploits Windows 64 bits desde Linux
	# o = Output
❯ i686-w64-mingw32-gcc <exploit.c> -o exploit -lws2_32      # Compilación para exploits Windows 32 bits desde Linux

❯ gcc -pthread <exploit.c> -o exploit -lcrypt               # Compilación para exploits Linux desde Linux
	# o = Output
```