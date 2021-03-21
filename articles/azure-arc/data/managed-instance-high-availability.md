---
title: Hoge Beschik baarheid door Azure Arc ingeschakelde beheerde instantie
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Meer informatie over het implementeren van een beheerd exemplaar waarvoor Azure Arc is ingeschakeld met hoge Beschik baarheid.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032191"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Hoge Beschik baarheid door Azure Arc ingeschakelde beheerde instantie

Een beheerd exemplaar dat door Azure Arc is ingeschakeld, wordt geïmplementeerd op Kubernetes als een container toepassing en maakt gebruik van Kubernetes-constructs, zoals stateful sets en permanente opslag, om een ingebouwde status controle, fout detectie en failover-mechanismen te bieden voor het onderhouden van de service status. Voor een betere betrouw baarheid kunt u ook het beheerde exemplaar van Azure Arc ingeschakeld configureren voor implementatie met extra replica's in een configuratie met hoge Beschik baarheid. Bewaking, fout detectie en automatische failover worden beheerd door de gegevens controller Arc Data Services. Deze service wordt zonder tussen komst van de gebruiker verschaft, alle van de instellingen van de beschikbaarheids groep, het spie gelen van data bases, om data bases toe te voegen aan de beschikbaarheids groep of failover-en upgrade coördinatie. In dit document worden beide typen hoge Beschik baarheid verkend.

## <a name="built-in-high-availability"></a>Ingebouwde hoge beschikbaarheid 

De ingebouwde hoge Beschik baarheid wordt verzorgd door Kubernetes wanneer externe permanente opslag is geconfigureerd en wordt gedeeld met knoop punten die worden gebruikt door de Arc data service-implementatie. In deze configuratie speelt Kubernetes de rol van de cluster-orchestrator. Wanneer het beheerde exemplaar in een container of het onderliggende knoop punt mislukt, Boots trapt de Orchestrator een andere instantie van de container en koppelt deze aan dezelfde permanente opslag. Dit type is standaard ingeschakeld wanneer u een beheerd exemplaar waarvoor Azure Arc is ingeschakeld implementeert.

### <a name="verify-built-in-high-availability"></a>Ingebouwde hoge Beschik baarheid verifiëren

In deze sectie controleert u de ingebouwde hoge Beschik baarheid van Kubernetes. Wanneer u de stappen voor het testen van deze functionaliteit volgt, verwijdert u de pod van een bestaande beheerde instantie en controleert u of deze actie door Kubernetes wordt hersteld. 

### <a name="prerequisites"></a>Vereisten

- Het Kubernetes-cluster moet [gedeelde, externe opslag](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration) hebben 
- Een beheerd exemplaar waarvoor Azure Arc is ingeschakeld, is geïmplementeerd met één replica (standaard)

1. Bekijk het Peul. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Verwijder de pod van het beheerde exemplaar.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Bijvoorbeeld

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Bekijk het Peul om te controleren of het beheerde exemplaar wordt hersteld.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Bijvoorbeeld

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Nadat alle containers in de pod zijn hersteld, kunt u verbinding maken met het beheerde exemplaar.

## <a name="deploy-with-always-on-availability-groups"></a>Implementeren met AlwaysOn-beschikbaarheids groepen

Voor een grotere betrouw baarheid kunt u het beheerde exemplaar van Azure Arc enabled configureren voor implementatie met extra replica's in een configuratie met hoge Beschik baarheid. 

Mogelijkheden die beschikbaar zijn in de beschikbaarheids groepen:

- Bij implementatie met meerdere replica's wordt er één beschikbaarheids groep gemaakt met de naam `containedag` . Standaard `containedag` heeft drie replica's, waaronder primair. Alle ruwe bewerkingen voor de beschikbaarheids groep worden intern beheerd, met inbegrip van het maken van de beschikbaarheids groep of het koppelen van replica's aan de gemaakte beschikbaarheids groep. Er kunnen geen extra beschikbaarheids groepen worden gemaakt in het beheerde exemplaar van Azure Arc enabled.

- Alle data bases worden automatisch toegevoegd aan de beschikbaarheids groep, met inbegrip van alle gebruikers-en systeem databases zoals `master` en `msdb` . Deze functie biedt een weer gave van één systeem over de replica's van de beschikbaarheids groep. Let `containedag_master` op zowel `containedag_msdb` als de data bases als u rechtstreeks verbinding maakt met het exemplaar. De `containedag_*` data bases vertegenwoordigen de `master` en `msdb` binnen de beschikbaarheids groep.

- Een extern eind punt wordt automatisch ingericht om verbinding te maken met data bases in de beschikbaarheids groep. Dit eind punt `<managed_instance_name>-svc-external` speelt de rol van de beschikbaarheids groep-listener af.

### <a name="deploy"></a>Implementeren

Als u een beheerd exemplaar wilt implementeren met beschikbaarheids groepen, voert u de volgende opdracht uit.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Status controleren
Zodra het exemplaar is geïmplementeerd, voert u de volgende opdrachten uit om de status van uw exemplaar te controleren:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Voorbeelduitvoer:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Let op het aanvullende nummer van `Replicas` en het `AGstatus` veld dat de status van de beschikbaarheids groep aangeeft. Als alle replica's actief en gesynchroniseerd zijn, is deze waarde `healthy` . 

### <a name="restore-a-database"></a>Een database herstellen 
Er zijn extra stappen vereist voor het herstellen van een data base in een beschikbaarheids groep. De volgende stappen laten zien hoe u een Data Base kunt herstellen naar een beheerd exemplaar en toevoegen aan een beschikbaarheids groep. 

1. Maak het externe eind punt van het primaire exemplaar beschikbaar door een nieuwe Kubernetes-service te maken.

    Bepaal de pod die als host fungeert voor de primaire replica door verbinding te maken met het beheerde exemplaar en uit te voeren:

    ```sql
    SELECT @@SERVERNAME
    ```
    Maak de kubernetes-service voor het primaire exemplaar door de onderstaande opdracht uit te voeren als uw kubernetes-cluster gebruikmaakt van nodePort-Services. Vervang door `podName` de naam van de server die wordt geretourneerd bij de vorige stap, `serviceName` waarbij de voorkeurs naam voor de Kubernetes-service is gemaakt.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Voor een LoadBalancer-service voert u dezelfde opdracht uit, behalve dat het type van de gemaakte service is `LoadBalancer` . Bijvoorbeeld: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Hier volgt een voor beeld van deze opdracht die wordt uitgevoerd op de Azure Kubernetes-service, waarbij de pod die als host fungeert voor de primaire server `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Ontvang het IP-adres van de Kubernetes-service die is gemaakt:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Zet de data base terug naar het eind punt van het primaire exemplaar.

    Voeg het back-upbestand van de data base toe aan de container van het primaire exemplaar.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Voorbeeld

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Herstel het back-upbestand van de data base door de onderstaande opdracht uit te voeren.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Voorbeeld

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Voeg de data base aan de beschikbaarheids groep toe.

    De data base kan alleen worden toegevoegd aan de AG als deze wordt uitgevoerd in de modus voor volledig herstel en er moet een logboek back-up worden gemaakt. Voer de onderstaande TSQL-instructies uit om de herstelde data base toe te voegen aan de beschikbaarheids groep.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    In het volgende voor beeld wordt een Data Base toegevoegd met de naam `WideWorldImporters` die op het exemplaar is hersteld:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Als best practice moet u de Kubernetes-service die hierboven is gemaakt verwijderen door de volgende opdracht uit te voeren:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Beperkingen

Beschik baarheid van Azure Arc Managed instance-beschikbaarheids groepen heeft dezelfde [beperkingen als beschikbaarheids groepen voor Big Data-clusters. Klik hier voor meer informatie.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [functies en mogelijkheden van SQL Managed instance met Azure Arc](managed-instance-features.md)
