# Burpsuite

Tags: #Burpsuite #Proxy #Repeater #Intruder #Web #Enumeracion #Intercept #SQLi #XSS

## OBJETIVO

- Interceptar y modificar peticiones HTTP/HTTPS
- Identificar vulnerabilidades en aplicaciones web
- Automatizar ataques de fuerza bruta en formularios
- Analizar el tráfico entre el navegador y el servidor

## TIPS

1. **Repeater → la herramienta más usada en el examen → modificar y reenviar requests**
2. **Guardar request como .req → usarlo con sqlmap directamente**
3. **Ctrl+R desde el Proxy → manda al Repeater → flujo más rápido**
4. **Intruder en Community Edition es lento → ffuf es más rápido para fuerza bruta**
5. **Siempre instalar el certificado de Burp → sin esto no interceptas HTTPS**
6. **Rastreo pasivo → activar al inicio → captura todo sin interceptar manualmente**

## RECURSOS

```
Wordlist útil para formularios web:
/usr/share/wordlists/fasttrack.txt
```

---

## 1. INICIAR BURP SUITE

```bash
❯ burpsuite &> /dev/null & disown
# Iniciar Burp y desvincularlo de la terminal

❯ BurpSuiteCommunity &> /dev/null & disown
# Alternativa → nombre completo
```

---

## 2. CONFIGURACIÓN INICIAL

### Configurar proxy en el navegador

```
Firefox → Ajustes → Red → Configuración manual del proxy
  HTTP Proxy: 127.0.0.1   Puerto: 8080
  ✓ Usar este proxy también para HTTPS

# O usar FoxyProxy (extensión) → más cómodo para alternar
```

### Instalar certificado SSL de Burp (OBLIGATORIO para HTTPS)

```
1. Con Burp abierto y proxy activado en el navegador
2. Ir a: http://burpsuite → descargar certificado
   (o http://burp → CA Certificate)
3. Firefox → Ajustes → Privacidad → Ver certificados
   → Importar → seleccionar cacert.der
   → Marcar: "Confiar para identificar sitios web"
4. Ahora Burp intercepta HTTPS correctamente
```

### Abrir navegador integrado de Burp

```
Proxy → Open Browser
# Navegador Chromium preconfigurado con el proxy de Burp
# No necesita configuración adicional ni certificado
```

---

## 3. PROXY — INTERCEPTAR PETICIONES

```bash
# Atajos de teclado principales
Ctrl + I    → Mandar petición interceptada al Intruder
Ctrl + R    → Mandar petición interceptada al Repeater
Ctrl + U    → URL encodear la data seleccionada
Ctrl + Shift + U  → URL decodear la data seleccionada
```

### Flujo básico de intercepción

```
1. Activar "Intercept is on" en Proxy → Intercept
2. Hacer la acción en el navegador (login, buscar, subir archivo)
3. La petición queda pausada en Burp → modificar lo que necesitas
4. Forward → enviar la petición modificada al servidor
5. Drop → descartar la petición sin enviarla
```

### Guardar request para sqlmap

```
Proxy → HTTP History → click derecho sobre la petición → Save item
# Guardar como archivo.req → usar con:
❯ sqlmap -r archivo.req --dbs
❯ sqlmap -r archivo.req -p <parametro> --level=5 --risk=3
```

### HTTP History — Rastreo pasivo

```
1. Activar proxy en el navegador
2. Burp → Dashboard → Capturing → activar "Live passive crawl from Proxy"
3. Navegar por la aplicación normalmente
4. Toda la data se acumula en Proxy → HTTP History
5. Funciona aunque Intercept esté desactivado
6. Revisar: endpoints, parámetros, tokens, cookies
```

---

## 4. REPEATER — MODIFICAR Y REENVIAR REQUESTS

### Flujo de uso

```
1. Capturar petición en Proxy
2. Ctrl+R → mandar al Repeater
3. Modificar el valor del parámetro que quieres probar
4. Send → ver la respuesta en el panel derecho
5. Repetir con diferentes valores
```

### Casos de uso en el examen

**Probar SQLi manualmente:**

```
GET /search?q=test HTTP/1.1
→ cambiar test por test' → ver si hay error SQL
→ test' OR '1'='1 → ver si devuelve más datos
→ test' ORDER BY 1-- → detectar número de columnas
```

**Probar XSS:**

```
GET /search?q=test HTTP/1.1
→ cambiar test por <script>alert(1)</script>
→ ver si se refleja en la respuesta sin sanitizar
```

**Probar LFI:**

```
GET /page?file=about HTTP/1.1
→ cambiar about por ../../../etc/passwd
→ ../../../../etc/passwd (más niveles si falla)
→ ..%2F..%2F..%2Fetc%2Fpasswd (URL encoded)
```

**Probar IDOR:**

```
GET /api/users/1 HTTP/1.1
→ cambiar 1 por 2, 3, 0, -1
→ ver si devuelve datos de otros usuarios
```

**Modificar cabeceras:**

```
→ Agregar: X-Forwarded-For: 127.0.0.1
→ Cambiar: User-Agent por payload de SQLi
→ Modificar: Cookie: role=admin
→ Agregar: Authorization: Basic <base64>
```

---

## 5. INTRUDER — FUERZA BRUTA Y FUZZING

### Configuración general

```
1. Capturar petición en Proxy → Ctrl+I → Intruder
2. Pestaña Positions → marcar los campos a fuzzear con §
3. Pestaña Payloads → cargar wordlist
4. Start Attack
```

> ⚠️ Community Edition tiene velocidad limitada → usar ffuf para fuerza bruta grande

### Modo Sniper — Un parámetro, una lista

```
Uso: fuzzing de directorios, LFI, un parámetro de formulario

GET /§FUZZ§ HTTP/1.1
→ Payload set 1: lista de directorios/valores
→ Prueba cada valor en la posición marcada

Ejemplo práctico — fuzzing de parámetro LFI:
GET /page?file=§about§ HTTP/1.1
→ Payload: ../etc/passwd, ../../../etc/passwd, etc.
```

### Modo Battering Ram — Múltiples posiciones, misma lista

```
Uso: cuando el mismo valor debe ir en varias posiciones

POST /login HTTP/1.1
username=§admin§&password=§admin§
→ Prueba la misma palabra en todas las posiciones simultáneamente
→ Útil cuando usuario y contraseña son iguales

Payload set 1: fasttrack.txt
```

### Modo Pitchfork — Múltiples listas sincronizadas

```
Uso: cuando tienes pares usuario:contraseña conocidos

POST /login HTTP/1.1
username=§user§&password=§pass§

Payload set 1: lista de usuarios (línea 1 → user1)
Payload set 2: lista de passwords (línea 1 → pass1)
→ Prueba user1:pass1, user2:pass2, user3:pass3
→ Los pares deben estar en la misma posición en ambas listas
```

### Modo Cluster Bomb — Todas las combinaciones posibles

```
Uso: fuerza bruta real de credenciales (usuario × contraseña)

POST /login HTTP/1.1
username=§user§&password=§pass§

Payload set 1: usuarios (ej: 10 usuarios)
Payload set 2: passwords (ej: 100 passwords)
→ Prueba todas las combinaciones: 10 × 100 = 1000 requests
→ Más lento pero más completo
```

### Identificar respuesta válida en Intruder

```
Columna Length → respuesta diferente = posible éxito
Columna Status → código diferente (302 en login = éxito)
Add → marcar texto de error → filtrar por Grep Match
→ Ejemplo: agregar "Invalid password" → los que NO lo tengan = éxito
```

---

## 6. DECODER — ENCODING Y DECODING

```
Burp → Decoder (o pestaña Decoder)

Pegar texto → seleccionar acción:
  Decode as → URL, HTML, Base64, Hex, ASCII hex, Gzip
  Encode as → URL, HTML, Base64, Hex, ASCII hex
  Hash → MD5, SHA1, SHA256, SHA512

Uso típico:
  1. Copiar valor de cookie/parámetro
  2. Pegar en Decoder
  3. Decode as Base64 → ver contenido
  4. Modificar → Encode as Base64
  5. Pegar de vuelta en Repeater

Atajos directos en Repeater/Proxy:
  Ctrl+U       → URL encode selección
  Ctrl+Shift+U → URL decode selección
```

---

## 7. COMPARER — COMPARAR RESPUESTAS

```
Uso: detectar diferencias sutiles entre respuestas

1. En HTTP History/Repeater → click derecho → Send to Comparer
2. Enviar dos respuestas diferentes al Comparer
3. Compare → By words o By bytes
4. Las diferencias quedan resaltadas

Casos de uso:
→ Respuesta con usuario válido vs inválido (user enumeration)
→ Respuesta con SQLi vs sin SQLi
→ Dos versiones de la misma página para detectar cambios
```

---

## 8. SCANNER (BURP PRO)

```
Solo disponible en Burp Suite Professional

Active Scan → escanea automáticamente en busca de:
  → SQLi, XSS, XXE, SSRF, IDOR, Path Traversal
  → Vulnerabilidades del OWASP Top 10

En Community Edition → no disponible
Alternativa gratuita → nikto, nuclei
```

---

## 9. EXTENSIONES ÚTILES (BApp Store)

```
Burp → Extensions → BApp Store

Recomendadas:
→ Active Scan++      → mejora el scanner activo
→ Autorize           → detectar fallos de autorización automáticamente
→ Logger++           → mejor logging de peticiones
→ Param Miner        → descubrir parámetros ocultos
→ Turbo Intruder     → Intruder sin límite de velocidad
→ JSON Web Tokens    → analizar y modificar JWTs
→ Upload Scanner     → detectar bypass en file uploads
→ SQLiPy             → integrar sqlmap con Burp
```

---

## 10. FLUJO DE USO EN EL EXAMEN

```
1. Abrir Burp → iniciar navegador integrado o configurar proxy
2. Activar rastreo pasivo → navegar la aplicación completa
3. Revisar HTTP History → identificar endpoints y parámetros
4. Para cada parámetro interesante:
   → Ctrl+R al Repeater → probar SQLi, XSS, LFI manualmente
5. Si hay formulario de login:
   → Ctrl+I al Intruder → Cluster Bomb → usuarios × passwords
6. Si hay parámetro vulnerable a SQLi:
   → Click derecho → Save item → sqlmap -r archivo.req
7. Si hay HTTPS y no intercepta:
   → Instalar certificado de Burp en el navegador
```