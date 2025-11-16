# Ataques 

Tags: #Pyrit #Attack #WPA/WPA2 

##  Validación del handshake con Pyrit

* [Pyrit ](https://github.com/JPaulMora/Pyrit) 
* [Uso de Pyrit](https://laguialinux.es/pyrit-descifrar-clave-wpa-con-gpu/)

```bash 
1. Descargamos el Github
2. Para hacer que funcione con el error, hacemos los siguiente:
❯ nano cpyrit/cpufeatures.h 
	Eliminamos donde inicia el 'Compile_AESNI'

3. nano cpyrit/_cpyrit_cpu.c
	Eliminamos la linea 1080-1142 y buscar todas las coincidencias de 'Compile_AESNI'

4. Instalamos Pyrit
❯ python2.7 setup.py clean 
❯ python2.7 setup.py build 
❯ python2.7 setup.py install
```

```bash 
❯ pyrit -r captura.cap analyze             # Analizamos la captura para que nos detecte los 'Handshake' capturados 

# Por si sale error
❯ wget https://old-releases.ubuntu.com/ubuntu/pool/universe/s/scapy/python-scapy_2.3.2-0.1_all.deb
❯ dpkg -i python-scapy_2.3.2-0.1_all.deb

# Volvemos a ejecutar el comando de Pyrit y ya deberia de funcionar 
```