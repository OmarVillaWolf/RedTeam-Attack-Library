# IDOR (Insecure Direct Object References)

Tags: #OWASP #IDOR  

## Descripción 

```bash 
El IDOR ocurre cuando una aplicación permite acceder directamente a objetos (como archivos, registros, cuentas, etc.) usando identificadores predecibles en la URL o en los parámetros, 'sin verificar si el usuario tiene permiso para acceder a ese recurso'

	'https://app.com/profile?id=1001' → 'id=1002'
```

## Impacto 

```bash 
Un atacante podría modificar los identificadores expuestos y acceder a la exposición de información sensible (datos personales, archivos, historial) restringidas de diferentes usuarios
- Acceso no autorizado a cuentas de otros usuarios.
- Modificación o eliminación de datos que no pertenecen al atacante.
- En casos graves: acceso a funciones administrativas o datos confidenciales de toda la empresa.

El impacto depende del tipo de objeto expuesto y del control que el atacante logre obtener.

# Riesgo técnico 
	- Impacto: Alto 
	- Probabilidad: Media 
	- Riesgo: Alto 
```

## Mitigación 

```bash 
1. Control de acceso restringido 
	- Verificar siempre si el usuario 'tiene permisos para acceder o modificar' el recurso solicitado
	- Nunca confiar solo en el identificador enviado por el cliente
```