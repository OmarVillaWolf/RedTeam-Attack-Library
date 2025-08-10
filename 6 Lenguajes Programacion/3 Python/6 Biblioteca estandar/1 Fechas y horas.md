# Manejo de fechas y horas

Tags: #Python3 

La biblioteca ‘**datetime**‘ en Python es una de las principales herramientas para trabajar con fechas y horas. Aquí hay algunos aspectos clave sobre esta biblioteca:

- **Tipos de Datos Principales**: ‘**datetime**‘ incluye varios tipos de datos, como ‘**date**‘ (para fechas), ‘**time**‘ (para horas), ‘**datetime**‘ (para fechas y horas combinadas), y ‘**timedelta**‘ (para representar diferencias de tiempo).
- **Manipulación de Fechas y Horas**: Permite realizar operaciones como sumar o restar días, semanas, o meses a una fecha, comparar fechas, o extraer componentes como el día, mes, o año de una fecha específica.
- **Zonas Horarias**: A través del módulo ‘**pytz**‘ que se integra con ‘**datetime**‘, se pueden manejar fechas y horas en diferentes zonas horarias, lo que es crucial para aplicaciones que requieren precisión a nivel global.
- **Formateo y Análisis**: ‘**datetime**‘ permite convertir fechas y horas a strings y viceversa, utilizando códigos de formato específicos. Esto es útil para mostrar fechas y horas en formatos legibles o para parsear strings que representan fechas/horas.
- **Facilidad de Uso**: A pesar de su potencia y flexibilidad, datetime es relativamente fácil de usar, lo que la hace accesible incluso para programadores principiantes.
- **Amplia Aplicación**: Desde registros de eventos hasta cálculos de períodos de tiempo, datetime es indispensable en una variedad de aplicaciones, como sistemas de reservas, análisis de datos temporales, y más.

En resumen, datetime es una biblioteca integral y robusta para el manejo de fechas y horas en Python, ofreciendo una amplia gama de funcionalidades esenciales para el manejo de datos temporales en la programación.

```python 
#!/usr/bin/env python3 

import datetime

ahora = datetime.datetime.now()            # Saber la fecha y hora actual
fecha = datetime.date(2024, 8, 31)         # Indicas la fecha 
hora = datetime.time(14, 15, 15)           # Indicas la hora, min y seg
fecha_hora = = datetime.datetime(2024, 8, 31, 14, 15, 15)

print(ahora)
print(fecha)
print(hora)
print(fecha_hora)
```

```python 
#!/usr/bin/env python3 

import datetime

ahora = datetime.datetime.now()  
año = ahora.year                           # De la fecha completa solo mostraremos el año
mes = ahora.month
dia = ahora.day
horas = ahora.hour
minutos = ahora.minute
segundo = ahora.second

print(año)  
```