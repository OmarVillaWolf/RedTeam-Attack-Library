# Imap 

Tags: #Imap 

## Comandos 

```bash 
# Enumeración 
❯ openssl s_client -connect IP_Server -starttls imap -quiet 
❯ openssl s_client -connect IP_Server:imaps 
❯ openssl s_client -connect IP_Server:143 -starttls imap -crlf

❯ telnet <IP> 110

	❯ a1 CAPABILITY
	❯ A001 CAPABILITY
	❯ 1 LOGIN sysadmin <passwd>
	❯ LIST "" "*"
	❯ SELECT INBOX
	❯ FETCH 1:* BODY[Text]
	❯ FETCH 1 BODY[Text]
	❯ A002 LOGOUT 
```