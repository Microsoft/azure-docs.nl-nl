---
title: De AdventureWorks-voorbeeld database importeren naar Azure Arc enabled PostgreSQL grootschalige
description: De AdventureWorks-voorbeeld database herstellen naar Azure Arc enabled PostgreSQL grootschalige
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a9efa17fb782d5a913493907b66973272e4e0356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441785"
---
# <a name="import-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>De AdventureWorks-voorbeeld database importeren naar Azure Arc enabled PostgreSQL grootschalige

[AdventureWorks](/sql/samples/adventureworks-install-configure) is een voorbeeld database met een OLTP-data base die wordt gebruikt in zelf studies en voor beelden. Het wordt door micro soft aangehouden en onderhouden als onderdeel van de SQL Server-voor [beelden github-opslag plaats](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Een open-source project heeft de AdventureWorks-data base geconverteerd om compatibel te zijn met Azure Arc enabled PostgreSQL grootschalige.
- [Oorspronkelijk project](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Volg de stappen in project waarmee de CSV-bestanden vooraf worden geconverteerd om compatibel te zijn met PostgreSQL](https://github.com/NorfolkDataSci/adventure-works-postgres)

In dit document wordt een eenvoudig proces voor het ophalen van de AdventureWorks-voorbeeld database geïmporteerd in uw PostgreSQL grootschalige-Server groep.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Het AdventureWorks-back-upbestand downloaden

Down load het bestand AdventureWorks. SQL in de PostgreSQL grootschalige Server Group container. In dit voor beeld gebruiken we de `kubectl exec` opdracht om op afstand een opdracht uit te voeren in de container postgresql grootschalige Server-groep om het bestand naar de container te downloaden. U kunt dit bestand downloaden vanaf een locatie die toegankelijk is voor `curl` . Gebruik dezelfde methode als u een andere Data Base hebt en een back-up wilt maken van de bestanden die u wilt ophalen in de PostgreSQL grootschalige-Server groep. Wanneer het zich in de PostgreSQL grootschalige-Server groep bevinden, is het eenvoudig om de data base, het schema en de gegevens te maken.

Voer een opdracht als deze uit om de bestanden te downloaden Vervang de waarde van de naam van de Pod en de naam ruimte voordat u deze uitvoert:

> [!NOTE]
>  Uw container moet een Internet verbinding hebben via 443 om het bestand te downloaden van GitHub.

> [!NOTE]
>  Gebruik de pod-naam van het coördinator knooppunt van de post gres grootschalige-Server groep. De naam is <server group name> c-0 (bijvoorbeeld postgres01c-0, waarbij c staat voor het knoop punt coördinator).  Als u niet zeker bent van de pod-naam, voert u de opdracht uit `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-import-the-adventureworks-database"></a>Stap 2: de AdventureWorks-data base importeren

Op dezelfde manier kunt u een kubectl exec-opdracht uitvoeren om het psql CLI-hulp programma te gebruiken dat is opgenomen in de containers voor de PostgreSQL grootschalige-Server groep om de data base te maken en te laden.

Voer een opdracht zoals deze uit om de lege data base te maken, waarbij u eerst de waarde van de pod-naam en de naam van de naam ruimte vervangt voordat u deze uitvoert.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Voer vervolgens een opdracht als deze uit om de data base te importeren en de waarde van de naam van de Pod en de naam ruimte te vervangen voordat u deze uitvoert.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Opmerking: er worden niet zoveel prestatie voordelen weer gegeven van het uitvoeren op Azure Arc enabled PostgreSQL grootschalige tot u uitschaalt en u de gegevens/tabellen Shard/distribueert over de worker-knoop punten van uw PostgreSQL grootschalige-Server groep. Zie [voorgestelde volgende stappen](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Voorgestelde volgende stappen
- Lees de concepten en instructies van Azure Database for PostgreSQL grootschalige om uw gegevens over meerdere grootschalige-knoop punten van PostgreSQL te verdelen en om te profiteren van de kracht van Azure Database for PostgreSQL grootschalige. :
    * [Knooppunten en tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Toepassingstype bepalen](../../postgresql/concepts-hyperscale-app-type.md)
    * [Een distributiekolom kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabelcolocatie](../../postgresql/concepts-hyperscale-colocation.md)
    * [Tabellen distribueren en bewerken](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Een multi tenant-data base ontwerpen](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Een real-time analyse dashboard ontwerpen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* In de bovenstaande documenten slaat u de secties **voor het aanmelden bij de Azure Portal**, & **een Azure database for PostgreSQL-grootschalige maken (Citus)**. Implementeer de resterende stappen in de implementatie van Azure Arc. Deze secties zijn specifiek voor de Azure Database for PostgreSQL grootschalige (Citus) die worden aangeboden als een PaaS-service in de Azure-Cloud, maar de andere onderdelen van de documenten zijn rechtstreeks van toepassing op uw PostgreSQL grootschalige van Azure Arc.

- [Uw Azure Database for PostgreSQL Hyperscale-servergroep uitschalen](scale-out-postgresql-hyperscale-server-group.md)
