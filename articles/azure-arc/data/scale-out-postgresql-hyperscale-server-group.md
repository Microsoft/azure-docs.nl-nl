---
title: Uitschalen Azure Database for PostgreSQL grootschalige-Server groep
description: Uitschalen Azure Database for PostgreSQL grootschalige-Server groep
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779971"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Uw Azure-PostgreSQL grootschalige-Server groep uitschalen door meer worker-knoop punten toe te voegen
In dit document wordt uitgelegd hoe u een PostgreSQL grootschalige-Server groep voor Azure-Arc kunt schalen. Dit doet u door een scenario te maken. **Als u geen gebruik wilt maken van het scenario en alleen meer wilt weten over hoe u kunt uitschalen, gaat u naar de alinea [uitschalen](#scale-out)**.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Aan de slag
Als u al bekend bent met het schaal model van Azure Arc enabled PostgreSQL grootschalige of Azure Database for PostgreSQL grootschalige (Citus), kunt u deze alinea overs Laan. Als dat niet het geval is, kunt u het beste eerst lezen over dit schaal model op de pagina documentatie van Azure Database for PostgreSQL grootschalige (Citus). Azure Database for PostgreSQL grootschalige (Citus) is dezelfde technologie die wordt gehost als een service in azure (platform as A service, ook wel bekend als PAAS) in plaats van te worden aangeboden als onderdeel van Azure Arc enabled Data Services:
- [Knooppunten en tabellen](../../postgresql/concepts-hyperscale-nodes.md)
- [Toepassingstype bepalen](../../postgresql/concepts-hyperscale-app-type.md)
- [Een distributiekolom kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Tabelcolocatie](../../postgresql/concepts-hyperscale-colocation.md)
- [Tabellen distribueren en bewerken](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Een multi tenant-data base ontwerpen](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Een real-time analyse dashboard ontwerpen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* In de bovenstaande documenten slaat u de secties **voor het aanmelden bij de Azure Portal**, & **een Azure database for PostgreSQL-grootschalige maken (Citus)**. Implementeer de resterende stappen in de implementatie van Azure Arc. Deze secties zijn specifiek voor de Azure Database for PostgreSQL grootschalige (Citus) die worden aangeboden als een PaaS-service in de Azure-Cloud, maar de andere onderdelen van de documenten zijn rechtstreeks van toepassing op uw PostgreSQL grootschalige van Azure Arc.

## <a name="scenario"></a>Scenario
Dit scenario verwijst naar de PostgreSQL grootschalige-Server groep die is gemaakt als voor beeld in de documentatie [een Azure-postgresql grootschalige-Server groep maken](create-postgresql-hyperscale-server-group.md) .

### <a name="load-test-data"></a>Testgegevens laden
In het scenario wordt gebruikgemaakt van een voor beeld van openbaar beschik bare GitHub-gegevens die beschikbaar zijn via de [Citus-gegevens website](https://www.citusdata.com/) (Citus-gegevens maakt deel uit van micro soft).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Verbinding maken met de PostgreSQL grootschalige-Server groep van Azure Arc ingeschakeld

##### <a name="list-the-connection-information"></a>De verbindings gegevens weer geven
Maak verbinding met de PostgreSQL grootschalige-Server groep van Azure Arc door eerst de verbindings gegevens op te halen: de algemene indeling van deze opdracht is
```console
azdata arc postgres endpoint list -n <server name>
```
Bijvoorbeeld:
```console
azdata arc postgres endpoint list -n postgres01
```

Voorbeelduitvoer:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Maak verbinding met het client hulpprogramma van uw keuze.

Voer de volgende query uit om te controleren of u momenteel twee (of meer grootschalige worker-knoop punten) hebt die overeenkomen met een Kubernetes-Pod:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Een voorbeeld schema maken
Voer de volgende query uit om twee tabellen te maken:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

JSONB is het JSON-gegevens type in binaire vorm in PostgreSQL. Er wordt een flexibel schema opgeslagen in één kolom en met PostgreSQL. Het schema bevat een EGINNEN-index waarmee elke sleutel en waarde erin kan worden geïndexeerd. Met een EGINNEN-index wordt het snel en eenvoudig om op basis van de verschillende voor waarden rechtstreeks op die Payload te zoeken. Daarom gaan we een aantal indexen maken voordat we onze gegevens laden:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Voor het Shard van standaard tabellen voert u een query uit voor elke tabel. Geef de tabel op die u wilt Shard en de sleutel waarop u deze wilt Shard. We Shard de tabel Evenementen en gebruikers op user_id:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Voorbeeldgegevens laden
De gegevens laden met behulp van kopiëren... VANUIT PROGRAMMA:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Query’s uitvoeren voor de gegevens
En meet nu hoe lang een eenvoudige query met twee knoop punten in beslag neemt:

```sql
SELECT COUNT(*) FROM github_events;
```
Noteer de uitvoerings tijd van de query.


## <a name="scale-out"></a>Uitschalen
De algemene indeling van de scale-out-opdracht is:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> De preview-versie biedt geen ondersteuning voor weer omlaag schalen. Het is bijvoorbeeld niet mogelijk om het aantal werkknooppunten te verminderen. Als u dit wilt doen, moet u de gegevens extraheren/er een back-up van maken, de servergroep verwijderen, een nieuwe servergroep maken met minder werkknooppunten en de gegevens vervolgens importeren.

In dit voor beeld verhogen we het aantal worker-knoop punten van 2 tot 4 door de volgende opdracht uit te voeren:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Wanneer u knoop punten toevoegt, ziet u de status in behandeling voor de Server groep. Bijvoorbeeld:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Zodra de knoop punten beschikbaar zijn, wordt de grootschalige Shard automatisch uitgevoerd en worden de gegevens opnieuw gedistribueerd naar de nieuwe knoop punten. De uitschaal bewerking is een online bewerking. Wanneer de knoop punten worden toegevoegd en de gegevens over de knoop punten opnieuw worden gedistribueerd, blijven de gegevens beschikbaar voor query's.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>De nieuwe vorm van de Server groep verifiëren (optioneel)
Gebruik een van de onderstaande methoden om te controleren of de Server groep nu gebruikmaakt van de extra worker-knoop punten die u hebt toegevoegd.

#### <a name="with-azdata"></a>Met azdata:
Voer de opdracht uit:
```console
azdata arc postgres server list
```

Er wordt een lijst geretourneerd met Server groepen die in uw naam ruimte zijn gemaakt en geeft het aantal worker-knoop punten aan. Bijvoorbeeld:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>Met kubectl:
Voer de opdracht uit:
```console
kubectl get postgresql-12
```

Er wordt een lijst geretourneerd met Server groepen die in uw naam ruimte zijn gemaakt en geeft het aantal worker-knoop punten aan. Bijvoorbeeld:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Als u een server groep hebt gemaakt van versie 11 PostgreSQL in plaats van 12, voert u de volgende opdracht uit: _kubectl Get postgresql-11_

#### <a name="with-a-sql-query"></a>Met een SQL-query:
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de volgende query uit:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Terug naar het scenario

Als u de uitvoerings tijd van de Select count-query wilt vergelijken met de voor beeld-gegevensset, gebruikt u dezelfde Count-query. Het kan worden gebruikt in de vier werk knooppunten, zonder dat er wijzigingen in de SQL-instructie worden aangebracht.

```sql
SELECT COUNT(*) FROM github_events;
```
Noteer de uitvoerings tijd.


> [!NOTE]
> Afhankelijk van uw omgeving: als u bijvoorbeeld uw test server groep hebt geïmplementeerd `kubeadm` op een VM met één knoop punt, ziet u mogelijk een bescheiden verbetering in de uitvoerings tijd. Bekijk de volgende korte Video's om een beter beeld te krijgen van het type prestatie verbetering dat u kunt bereiken met Azure Arc enabled PostgreSQL grootschalige:
>* [High Performance HTAP met Azure PostgreSQL grootschalige (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [HTAP-toepassingen bouwen met python & Azure PostgreSQL grootschalige (Citus)](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [omhoog en omlaag schaalt (geheugen, vCores) uw Azure-postgresql grootschalige-Server groep](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Meer informatie over het instellen van server parameters in uw PostgreSQL grootschalige-Server groep voor Azure-Arc
- Lees de concepten en instructies van Azure Database for PostgreSQL grootschalige om uw gegevens te verdelen over meerdere PostgreSQL grootschalige-knoop punten en om te profiteren van alle kracht van Azure data base for post gres grootschalige. :
    * [Knooppunten en tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Toepassingstype bepalen](../../postgresql/concepts-hyperscale-app-type.md)
    * [Een distributiekolom kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabelcolocatie](../../postgresql/concepts-hyperscale-colocation.md)
    * [Tabellen distribueren en bewerken](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Een multi tenant-data base ontwerpen](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Een real-time analyse dashboard ontwerpen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* In de bovenstaande documenten slaat u de secties **voor het aanmelden bij de Azure Portal**, & **een Azure database for PostgreSQL-grootschalige maken (Citus)**. Implementeer de resterende stappen in de implementatie van Azure Arc. Deze secties zijn specifiek voor de Azure Database for PostgreSQL grootschalige (Citus) die worden aangeboden als een PaaS-service in de Azure-Cloud, maar de andere onderdelen van de documenten zijn rechtstreeks van toepassing op uw PostgreSQL grootschalige van Azure Arc.

- [Opslag configuraties en Kubernetes opslag concepten](storage-configuration.md)
- [Kubernetes-resource model](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
