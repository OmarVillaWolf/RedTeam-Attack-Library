# Lista de Máquinas — OSCP+ 2026

## Progresión mixta: Linux + Windows + AD cada semana

### Fuente: LainKusanagi OSCP List (150 máquinas)

---

## 📅 MES 1 — HTB solo | Easy en todo + primeras Windows + AD básico

> Foco: enumeración web, Linux PrivEsc básico, primeras Windows, intro AD CRTP: terminar módulos del curso Domingo GOAD: setup BloodHound, enumerar dominio

```
1.  Lun | HTB | Sea           | Linux   | Easy   — Enumeración web, CMS
2.  Mar | HTB | Headless      | Linux   | Easy   — XSS, OS Injection 
3.  Mié | HTB | Nibbles       | Linux   | Easy   — Web, sudo abuse
4.  Jue | HTB | Jerry         | Windows | Easy   — Tomcat default creds
5.  Vie | HTB | Return        | Win/AD  | Easy   — Printer spooler, AD intro
─────────────────────────────────────────────────────────────────────
6.  Lun | HTB | Bashed        | Linux   | Easy   — Web shell, sudo
7.  Mar | HTB | Netmon        | Windows | Easy   — FTP anon, PRTG RCE
8.  Mié | HTB | OpenAdmin     | Linux   | Easy   — Web, SSH creds
9.  Jue | HTB | Servmon       | Windows | Easy   — NVMS LFI, NSClient
10. Vie | HTB | Active        | Win/AD  | Easy   — GPP creds, Kerberoasting
─────────────────────────────────────────────────────────────────────
11. Lun | HTB | Broker        | Linux   | Easy   — ActiveMQ CVE, PrivEsc
12. Mar | HTB | Buff          | Windows | Easy   — CloudMe RCE, PrivEsc
13. Mié | HTB | Analytics     | Linux   | Easy   — Metabase RCE, Docker
14. Jue | HTB | Love          | Windows | Med    — SSRF, AlwaysInstall
15. Vie | HTB | Cicada        | Win/AD  | Easy   — Assumed breach, RID brute
─────────────────────────────────────────────────────────────────────
16. Lun | HTB | Swagshop      | Linux   | Easy   — Magento CVE, sudo
17. Mar | HTB | Remote        | Windows | Med    — NFS creds, TeamViewer
18. Mié | HTB | Networked     | Linux   | Easy   — Web upload, cron
19. Jue | HTB | Bounty        | Windows | Med    — IIS .config upload
20. Vie | HTB | Forest        | Win/AD  | Med    — AS-REP Roasting, DCSync
─────────────────────────────────────────────────────────────────────
Sábado → Repaso sin writeups de las 5 de esa semana
Domingo → Practicar GOAD
```

---

## 📅 MES 2 — HTB + PG Practice | Medium todo + AD creciendo

> Activas PG Practice. Foco: web más complejo, Windows medium, AD básico-medio CRTP: activar lab semana 1 de este mes Domingo GOAD: Pass-the-Hash, movimiento lateral

```
21. Lun | HTB        | Precious      | Linux   | Med    — Ruby Pdfkit, sudo
22. Mar | PG Practice| ClamAV        | Linux   | Easy   — Sendmail exploit
23. Mié | HTB        | Artic         | Windows | Med    — ColdFusion LFI+RCE
24. Jue | PG Practice| Kevin         | Windows | Easy   — Servicios Windows
25. Vie | HTB        | Sauna         | Win/AD  | Med    — BloodHound, DCSync
─────────────────────────────────────────────────────────────────────
26. Lun | HTB        | Busqueda      | Linux   | Med    — Python injection, git
27. Mar | PG Practice| Pelican       | Linux   | Easy   — Exhibitor Zookeeper
28. Mié | HTB        | Chatterbox    | Windows | Med    — AChat RCE, token
29. Jue | PG Practice| Internal      | Windows | Easy   — Servicios Windows
30. Vie | HTB        | Monteverde    | Win/AD  | Med    — Azure AD Connect
─────────────────────────────────────────────────────────────────────
31. Lun | HTB        | Cozyhosting   | Linux   | Med    — SSRF, PostgreSQL
32. Mar | PG Practice| Payday        | Linux   | Easy   — Multi servicio
33. Mié | HTB        | Secnotes      | Windows | Med    — CSRF, WSL bash
34. Jue | PG Practice| Algernon      | Windows | Easy   — SolarWinds RCE
35. Vie | HTB        | Escape        | Win/AD  | Med    — MSSQL NetNTLM, AD
─────────────────────────────────────────────────────────────────────
36. Lun | HTB        | Devvortex     | Linux   | Med    — Joomla CVE, MySQL
37. Mar | PG Practice| Snookums      | Linux   | Easy   — Web, MySQL PrivEsc
38. Mié | HTB        | Sniper        | Windows | Med    — RFI PHP, .chm PrivEsc
39. Jue | PG Practice| Bratarina     | Linux   | Easy   — SMTP exploit
40. Vie | HTB        | Timelapse     | Win/AD  | Med    — LAPS, pfx certs
─────────────────────────────────────────────────────────────────────
Sábado → Repaso sin writeups
Domingo → Lab CRTP: sesión larga, AS-REP + ACL abuse
```

---

## 📅 MES 3 — HTB + PG Practice | Hard Linux + Windows hard + AD medio

> Foco: Linux complejo, Windows difícil, AD techniques profundas CRTP: cerrar flags y presentar examen semana 3-4 Domingo GOAD: DCSync, simulacro cronometrado

```
41. Lun | HTB        | Linkvortex    | Linux   | Med    — Git, symlink PrivEsc
42. Mar | PG Practice| ZenPhoto      | Linux   | Easy   — TimThumb, sudo
43. Mié | HTB        | Heist         | Windows | Med    — Cisco config, PSExec
44. Jue | PG Practice| Jacko         | Windows | Med    — Java JMX RCE
45. Vie | HTB        | Administrator | Win/AD  | Med    — Assumed breach AD
─────────────────────────────────────────────────────────────────────
46. Lun | HTB        | Solidstate    | Linux   | Med    — SMTP James, rbash
47. Mar | PG Practice| Cockpit       | Linux   | Easy   — Web, Linux PrivEsc
48. Mié | HTB        | Access        | Windows | Med    — Telnet, .mdb creds
49. Jue | PG Practice| Craft         | Windows | Med    — App, PrivEsc Windows
50. Vie | HTB        | Certified     | Win/AD  | Med    — Assumed breach, ESC
─────────────────────────────────────────────────────────────────────
51. Lun | HTB        | Pilgrimage    | Linux   | Med    — Git leak, ImageMagick
52. Mar | PG Practice| Twiggy        | Linux   | Med    — SaltStack RCE
53. Mié | HTB        | Mailing       | Windows | Med    — CVE Outlook, LibreOffice
54. Jue | PG Practice| Squid         | Win     | Med    — Squid proxy pivoting
55. Vie | HTB        | Puppy         | Win/AD  | Hard   — Assumed breach nuevo
─────────────────────────────────────────────────────────────────────
56. Lun | HTB        | Editorial     | Linux   | Med    — SSRF interno, git
57. Mar | PG Practice| Blackgate     | Linux   | Med    — Web, PrivEsc Linux
58. Mié | HTB        | Giddy         | Windows | Hard   — SQLi xp_dirtree
59. Jue | PG Practice| Hub           | Linux   | Med    — Gitea RCE, sudo
60. Vie | HTB        | Cascade       | Win/AD  | Med    — LDAP, legacy .NET
─────────────────────────────────────────────────────────────────────
Sábado → Repaso sin writeups
Domingo sem 3-4 → 🎯 Examen CRTP (24h)
```

---

## 📅 MES 4 — HTB + PG Practice | Hard todo + AD difícil

> CRTP ya completado. Máquinas hard en los tres ejes Domingo GOAD: simulacro DA < 2.5h sin notas

```
61. Lun | HTB        | Poison        | Linux   | Med    — LFI log poison, VNC
62. Mar | PG Practice| Crane         | Linux   | Med    — Web, PrivEsc Linux
63. Mié | HTB        | Jeeves        | Windows | Med    — Jenkins Groovy, token
64. Jue | PG Practice| Press         | Linux   | Med    — FluxCP RCE, PrivEsc
65. Vie | HTB        | Blackfield    | Win/AD  | Hard   — AS-REP, SeBackup
─────────────────────────────────────────────────────────────────────
66. Lun | HTB        | Tartarsauce   | Linux   | Med    — Web upload, NFS
67. Mar | PG Practice| Boolean       | Linux   | Med    — Web app, PrivEsc
68. Mié | HTB        | Querier       | Windows | Hard   — MSSQL hash capture
69. Jue | PG Practice| Nickel        | Windows | Hard   — Multi servicio, PrivEsc
70. Vie | HTB        | Intelligence  | Win/AD  | Med    — PDFs, gMSA, delegation
─────────────────────────────────────────────────────────────────────
71. Lun | HTB        | Irked         | Linux   | Med    — UnrealIRCd, PrivEsc
72. Mar | PG Practice| Pebbles       | Linux   | Med    — Web multi vector
73. Mié | HTB        | Querier       | Windows | Hard   — MSSQL, hash + PrivEsc
74. Jue | PG Practice| MedJed        | Windows | Med    — CMS, PrivEsc Windows
75. Vie | HTB        | Flight        | Win/AD  | Hard   — NTLM coerce, Kerberos
─────────────────────────────────────────────────────────────────────
76. Lun | HTB        | Tabby         | Linux   | Med    — Tomcat, LXD PrivEsc
77. Mar | PG Practice| Hetemit       | Linux   | Med    — Werkzeug RCE
78. Mié | HTB        | Mailing       | Windows | Med    — CVE chain Windows
79. Jue | PG Practice| Slort         | Windows | Med    — Multi etapa, pivoting
80. Vie | HTB        | TheFrizz      | Win/AD  | Hard   — AD harder 2025
─────────────────────────────────────────────────────────────────────
Sábado → Repaso sin writeups
Domingo → GOAD: simulacro DA < 2.5h sin notas
```

---

## 📅 MES 5 — HTB + PG Practice | Todo hard + AD muy difícil

> Foco: máquinas muy duras, AD assumed breach, simulacros parciales Domingo GOAD: DA < 2h

```
81. Lun | HTB        | Usage         | Linux   | Med    — Laravel SQLi, wildcard
82. Mar | PG Practice| Nukem         | Linux   | Hard   — Multi servicio hard
83. Mié | HTB        | Sunday        | Linux   | Med    — Finger, sudo wget
84. Jue | PG Practice| Billyboss     | Windows | Hard   — SeImpersonate
85. Vie | HTB        | Fluffy        | Win/AD  | Med    — AD nuevo 2025
─────────────────────────────────────────────────────────────────────
86. Lun | HTB        | Nineveh       | Linux   | Med    — LFI, stego, cron
87. Mar | PG Practice| Walla         | Linux   | Hard   — Web multi, WAF
88. Mié | HTB        | Keeper        | Linux   | Med    — KeePass dump
89. Jue | PG Practice| AuthBy        | Windows | Hard   — FTP creds, PrivEsc
90. Vie | HTB        | TombWatcher   | Win/AD  | Med    — AD reciente oct 2025
─────────────────────────────────────────────────────────────────────
91. Lun | HTB        | Popcorn       | Linux   | Med    — Web upload, dirty
92. Mar | PG Practice| Wombo         | Linux   | Med    — Redis RCE, PrivEsc
93. Mié | HTB        | Mentor        | Linux   | Med    — SNMP, Docker socket
94. Jue | PG Practice| DVR4          | Windows | Hard   — Argus DVR RCE
95. Vie | HTB        | EscapeTwo     | Win/AD  | Hard   — Assumed breach harder
─────────────────────────────────────────────────────────────────────
96. Lun | HTB        | Jarvis        | Linux   | Med    — SQLi, systemctl sudo
97. Mar | PG Practice| Extplorer     | Linux   | Med    — File manager RCE
98. Mié | HTB        | Pandora       | Linux   | Med    — SNMP UDP, PandoraFMS
99. Jue | PG Practice| Shenzi        | Win/AD  | Hard   — SMB creds, AD PrivEsc
100.Vie | HTB        | Signed        | Win/AD  | Med    — Assumed breach reciente
─────────────────────────────────────────────────────────────────────
Sábado → Repaso sin writeups
Domingo → GOAD: simulacro DA < 2h
```

---

## 📅 MES 6 — PG Practice hard + últimas HTB + Challenge Labs

> Todo hard. Sin hints. Timer por máquina. Domingo GOAD: DA < 1.5h

```
101.Lun | HTB        | Magic         | Linux   | Med    — Upload bypass, SUID
102.Mar | PG Practice| Exfiltrated   | Linux   | Med    — WordPress, sudo
103.Mié | HTB        | Builder       | Linux   | Med    — Jenkins CVE
104.Jue | PG Practice| Hutch         | Win/AD  | Hard   — LDAP anon, AD PrivEsc
105.Vie | HTB        | Monitored     | Linux   | Med    — Nagios, PrivEsc
─────────────────────────────────────────────────────────────────────
106.Lun | PG Practice| Postfish      | Linux   | Hard   — Phishing IMAP
107.Mar | HTB        | Cctv          | Linux   | Med    — Reciente 2025
108.Mié | PG Practice| Vault         | Win/AD  | Hard   — AD cadena completa
109.Jue | HTB        | Dog           | Linux   | Med    — Web CMS reciente
110.Vie | PG Practice| Resourced     | Win/AD  | Hard   — Lateral movement hard
─────────────────────────────────────────────────────────────────────
111.Lun | HTB        | UpDown        | Linux   | Hard   — PHP filters, proc_open
112.Mar | PG Practice| Hawat         | Linux   | Hard   — SQLi blind, hard
113.Mié | HTB        | Help          | Linux   | Med    — HelpDeskZ, PrivEsc
114.Jue | PG Practice| Nagoya        | Win/AD  | Hard   — AD cadena muy hard
115.Vie | HTB        | BoardLight    | Linux   | Med    — Dolibarr CVE, SUID
─────────────────────────────────────────────────────────────────────
116.Lun | PG Practice| Hunit         | Linux   | Hard   — Multi vuln hard
117.Mar | HTB        | UnderPass     | Linux   | Med    — SNMP, RADIUS
118.Mié | PG Practice| Hokkaido      | Win/AD  | Hard   — AD muy hard
119.Jue | HTB        | Fanatastic    | Linux   | Hard   — Web, hard PrivEsc
120.Vie | PG Practice| Access        | Win/AD  | Hard   — AD PG hard
─────────────────────────────────────────────────────────────────────
Sábado sem 1 → 🎯 Challenge Lab OSCP-A (timer 24h real)
Sábado sem 2 → Revisión gaps OSCP-A
Sábado sem 3 → 🎯 Challenge Lab OSCP-B (timer 24h real)
Sábado sem 4 → Si +70pts limpio → AGENDAR EXAMEN
```

---

## 📅 MES 7 — PG Practice resto | Todo hard sin hints

> Simulacro puro. Cada máquina con timer máx 3h.

```
121.Lun | PG Practice| Clue          | Linux   | V.Hard — Multi vector
122.Mar | PG Practice| Hepet         | Windows | Hard   — App Windows
123.Mié | PG Practice| Apex          | Linux   | Hard   — Linux cadena
124.Jue | PG Practice| Mice          | Windows | Hard   — App, PrivEsc
125.Vie | PG Practice| Sybaris       | Linux   | Hard   — Pivoting hard
─────────────────────────────────────────────────────────────────────
126.Lun | PG Practice| Peppo         | Linux   | Hard   — PrivEsc chain
127.Mar | PG Practice| Monster       | Windows | Hard   — App Windows
128.Mié | PG Practice| Sorcerer      | Linux   | Hard   — Multi vector
129.Jue | PG Practice| Fish          | Windows | Hard   — App, PrivEsc
130.Vie | PG Practice| Levram        | Linux   | Hard   — Hard PrivEsc
─────────────────────────────────────────────────────────────────────
131.Lun | PG Practice| Roquefort     | Linux   | Hard   — Linux cadena
132.Mar | PG Practice| QuackerJack   | Linux   | Hard   — Multi vector
133.Mié | PG Practice| Readys        | Linux   | Hard   — Redis hard chain
134.Jue | PG Practice| Astronaut     | Linux   | Hard   — Linux hard
135.Vie | PG Practice| Mzeeav        | Linux   | Hard   — Linux PrivEsc
─────────────────────────────────────────────────────────────────────
136.Lun | PG Practice| LaVita        | Linux   | Hard   — Cadena completa
137.Mar | PG Practice| Flu           | Linux   | Hard   — Multi vector
138.Mié | PG Practice| Workaholic    | Linux   | Hard   — Hard Linux
139.Jue | PG Practice| Xposedapi     | Linux   | Hard   — API RCE, PrivEsc
140.Vie | PG Practice| Zab           | Linux   | Hard   — Reciente hard
─────────────────────────────────────────────────────────────────────
141.Lun | PG Practice| Zipper        | Linux   | Hard   — Linux chain
142.Mar | PG Practice| Fired         | Linux   | Hard   — Linux PrivEsc
143.Mié | PG Practice| Scrutiny      | Linux   | Hard   — Hard Linux
144.Jue | PG Practice| Bullybox      | Linux   | Hard   — Hard PrivEsc
145.Vie | PG Practice| Marketing     | Linux   | Hard   — Linux chain
─────────────────────────────────────────────────────────────────────
146.Lun | PG Practice| SPX           | Linux   | Hard   — Hard Linux
147.Mar | PG Practice| Vmdak         | Linux   | Hard   — Hard Linux
148.Mié | PG Practice| WallpaperHub  | Linux   | Hard   — Hard reciente
149.Jue | PG Practice| BitForge      | Linux   | Hard   — Hard chain
150.Vie | PG Practice| PC            | Linux   | Hard   — gRPC, SQLite
─────────────────────────────────────────────────────────────────────
Sábado → Challenge Lab OSCP-C si disponible
Domingo → GOAD simulacro final → DA < 1.5h
→ ✅ AGENDAR EXAMEN OSCP+
```

---

## 📊 Resumen por mes

```
Mes 1 → HTB solo         20 máq | Linux Easy + Win Easy + AD Easy
Mes 2 → HTB + PG         20 máq | Linux Med  + Win Med  + AD Med
Mes 3 → HTB + PG         20 máq | Linux Hard + Win Med  + AD Med/Hard
Mes 4 → HTB + PG         20 máq | Linux Hard + Win Hard + AD Hard
Mes 5 → HTB + PG         20 máq | Todo Hard  + AD muy difícil
Mes 6 → HTB + PG + Labs  20 máq | Todo Hard  + Challenge Labs
Mes 7 → PG Practice      30 máq | Simulacro puro
──────────────────────────────────────────────────────
TOTAL: 150 máquinas — 100% lista LainKusanagi HTB + PG Practice
```