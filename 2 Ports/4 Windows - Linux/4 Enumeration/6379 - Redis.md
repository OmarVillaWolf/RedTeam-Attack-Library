# Redis

Tags: #Redis 


```bash 
❯ redis-cli -a <Password> -h <IP> -p <Port>    # Nos conectamos a Redis 

	❯ select 0               # Seleccionas la base de datos
	❯ keys *                 # Listamos todas las keys
	❯ type authlist          # Mirar de que tipo de keys es 'lista'
	❯ lrange authlist 0 5    #               
```