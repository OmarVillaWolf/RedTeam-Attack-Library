# Inyecciones LDAP 

Tags: #LDAP #OWASP 

Las inyecciones LDAP (Protocolo de Directorio Ligero) son un tipo de ataque que explota vulnerabilidades en aplicaciones web que interactúan con servidores LDAP, los cuales almacenan información de usuarios y recursos en una red.
### **Funcionamiento**

El ataque consiste en insertar comandos LDAP maliciosos en campos de entrada de una aplicación web. Si la aplicación no valida correctamente estos datos, el atacante puede manipular las consultas LDAP y obtener acceso indebido o ejecutar acciones no autorizadas en el servidor.
### **Riesgos**

- Acceso a información restringida (usuarios o recursos).
- Modificaciones no autorizadas (agregar o eliminar usuarios).
- Ejecución de actividades maliciosas, como phishing o instalación de malware.

A continuación, se proporciona el enlace directo al proyecto de Github para desplegar un laboratorio práctico donde se puede ejecutar esta vulnerabilidad:

- [https://github.com/motikan2010/LDAP-Injection-Vuln-App](https://github.com/motikan2010/LDAP-Injection-Vuln-App)

```bash 

```