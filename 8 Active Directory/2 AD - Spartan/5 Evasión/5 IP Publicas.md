# Utilizando IP publicas y dominios confiables para el almacenamiento de herramientas

Tags: #AD #Evasion #Windows 

```bash 
1. Evasión de la detección: Muchas soluciones de seguridad, como los sistemas de prevención y detección de intrusiones (IPS/IDS), emplean listas negras de direcciones IP y dominios conocidos por actividades maliciosas. Al usar direcciones IP y dominios legítimos, los atacantes pueden evitar ser detectados por estas listas negras.
    
2. Confianza del usuario: Un dominio confiable o reconocido puede hacer que un usuario sea más propenso a hacer clic en un enlace o descargar un archivo. Es una forma de ingeniería social: aprovechar la confianza en una entidad conocida para engañar al usuario.
    
3. Evitar bloqueos geográficos: Algunos sistemas de seguridad bloquean tráfico de ciertas regiones que son conocidas por ser fuentes de actividad maliciosa. Al utilizar servidores en regiones "confiables", los atacantes pueden eludir estos bloqueos.
    
4. Infraestructura como Servicio (IaaS): Con la popularidad y accesibilidad de servicios en la nube como AWS, Azure y Google Cloud, es fácil para los atacantes alquilar y utilizar estos recursos con fines maliciosos. Estos servicios a menudo tienen direcciones IP y dominios que se consideran "confiables".
    
5. Uso de servicios de redirección: Hay servicios que permiten la redirección de tráfico, lo que puede ocultar la verdadera fuente del contenido malicioso.
    
6. Compromiso de sitios legítimos: En lugar de crear su propio dominio malicioso, un atacante puede comprometer un sitio web legítimo e inyectar su propio código o contenido malicioso. Esto no solo les da una fachada de legitimidad sino que también aprovecha la reputación del sitio comprometido.
    
7. SSL/TLS y certificados: Algunos atacantes utilizan certificados SSL/TLS para sus sitios maliciosos, lo que agrega un indicador visual de "seguridad" en los navegadores. Si combinan esto con un dominio confiable, puede ser muy persuasivo para las víctimas potenciales.
```