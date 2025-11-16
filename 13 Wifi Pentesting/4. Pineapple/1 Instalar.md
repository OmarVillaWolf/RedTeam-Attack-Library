# Pineapple 

Tags: #Pineapple #Wifi #EvilPortal 

## Instalar Pineapple Wifi 

* [Reset-password-Pineapple-Wifi](https://docs.hak5.org/wifi-pineapple/faq/password-reset)

```bash 
❯ http://172.16.42.1:1471/#/Login                  # Dirección IP del Pineapple
```


## Agregar los 'EvilPortal' a Pineapple 

* [Evil-Portals-Pienapple](https://github.com/SgtFoose/Evil-Portals)
* [Evil-Portal](https://github.com/kleo/evilportals)

```bash 
1. Descargamos el ZIP que se encuentra en '<> Code'

2. Abrimos la CMD de windows
❯ ssh root@172.16.42.1                # Ingresar por ssh a nuestro Pineapple 
	❯ cd portals 

3. Abrimos otro CMD para transferir los archivos usando 'scp' desde la ruta en donde contiene la carpeta 'portals' que descargamos
❯ scp -r portals root@172.16.42.1:root/

4. Movemos los archivos que copiamos a la carpeta 'portals' en nuestro Pineapple 
❯ cd root                             # Nos movemos a la carpeta llamada 'root' en donde estan los archivos cargados
❯ mv google/ /root/portals            # Movemos el 'Evil Portal' desde la carpeta root a la carpeta portals de Pineapple 

5. Recargamos nuestro portal en la web y deberian salir los 'Evil Portals' que hemos agregado
```