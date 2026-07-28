# Abuso de servicios internos del sistema

Tags: #Linux #ServiciosInternos  #Escalada #Root #Privilegios 

Los **servicios internos** son componentes esenciales que operan en segundo plano dentro de un sistema operativo, encargándose de funciones críticas como la gestión de red, impresión, actualización de software y monitoreo del sistema, entre otros.

No obstante, si estos servicios **no están configurados adecuadamente** y se encuentran activos, pueden representar una brecha de seguridad significativa. Los atacantes podrían explotar estos servicios para obtener acceso no autorizado al sistema y llevar a cabo actividades malintencionadas.

Un ejemplo concreto sería un servicio de red mal configurado con permisos elevados. Si un atacante logra identificarlo y hallar una forma de aprovecharlo, podría utilizarlo para escalar privilegios y obtener acceso de administrador.

En esta clase, analizaremos un caso ilustrativo de cómo un atacante podría, en primer lugar, detectar un servicio activo en el sistema y, posteriormente, explotarlo para incrementar sus privilegios de usuario.


## Servicios Internos 

```bash
❯ netstat -nat                          # Podemos ver los puertos abiertos internos 

	# 27017 = Mongo el cual podemos usar para buscar usuario y passwd
	# 8000 = Posible web 

❯ ps -faux                              
```