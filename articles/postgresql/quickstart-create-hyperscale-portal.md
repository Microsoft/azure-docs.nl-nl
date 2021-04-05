---
title: 'Quickstart: een servergroep maken - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: Quickstart voor het maken en uitvoeren van query's van gedistribueerde tabellen op Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 03a6e927a074067e85f1a3adca38cae386d1af38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026213"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>Quickstart: een Hyperscale (Citus)-servergroep maken in Azure Portal

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze snelstart ontdekt u hoe u een Azure Database for PostgreSQL - Hyperscale (Citus)-servergroep maakt in Azure Portal. U verkent gedistribueerde gegevens: sharding van tabellen over knooppunten, de opname van voorbeeldgegevens en het uitvoeren van query's die op meerdere knooppunten worden uitgevoerd.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Tabellen maken en distribueren

Zodra u met behulp van psql verbinding hebt gemaakt met het hyperscale-coördinatorknooppunt, kunt u een aantal basistaken voltooien.

Op Hyperscale (Citus)-servers zijn drie typen tabellen aanwezig:

- Gedistribueerde tabellen of tabellen waarop sharding is toegepast (uitgespreid voor schaalaanpassing voor prestaties en parallellisering)
- Referentietabellen (meerdere kopieën)
- Lokale tabellen (vaak gebruikt voor interne beheertabellen)

In deze quickstart richten we ons met name op gedistribueerde tabellen en proberen we onze bekendheid hiermee te vergroten.

Het gegevensmodel waarmee we gaan werken is eenvoudig: gebruikers- en gebeurtenisgegevens van GitHub. Gebeurtenissen zijn bijvoorbeeld het maken van een fork, git-opmerkingen met betrekking tot een organisatie, enzovoort.

Zodra u via psql verbinding hebt gemaakt, gaan we onze tabellen maken. Voer in de psql-console het volgende uit:

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

Het veld `payload` van `github_events` heeft een JSONB-gegevenstype. JSONB is het JSON-gegevenstype in binaire vorm in Postgres. Door dit gegevenstype is het eenvoudig om een flexibel schema in één kolom op te slaan.

In Postgres kan een `GIN`-index voor dit type worden gemaakt, waarmee elke sleutel en waarde in dit type worden geïndexeerd. Met een index kunt u snel en gemakkelijk query's uitvoeren op de nettolading met verschillende voorwaarden. Maak nu een aantal indexen voordat we onze gegevens gaan laden. In psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Vervolgens maken we die Postgres-tabellen op het coördinatorknooppunt en geven we aan Hyperscale (Citus) door dat ze via sharding naar de werknemers moet worden gestuurd. Hiervoor voeren we een query uit voor elke tabel en geven we de sleutel op waarmee de sharding moet worden uitgevoerd. In het huidige voorbeeld passen we sharding toe op zowel de gebeurtenissen- als de gebruikerstabel op `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

We kunnen nu gegevens laden. Blijf in psql en gebruik een shell (???) om de bestanden te downloaden:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Hierna laadt u de gegevens uit de bestanden in de gedistribueerde tabellen:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Query's uitvoeren

Nu gaan we over naar het leuke gedeelte: we gaan een aantal query's uitvoeren. We beginnen met een eenvoudige `count (*)` om te zien hoeveel gegevens we hebben geladen:

```sql
SELECT count(*) from github_events;
```

Dat ging goed. We komen later terug bij dat soort aggregaties, maar voor nu kijken we eerst naar een aantal andere query's. In de JSONB `payload`-kolom staan vele gegevens, maar het varieert op basis van het gebeurtenistype. `PushEvent`-gebeurtenissen bevatten een grootte met daarin het aantal unieke doorvoeringen voor de push. Hiermee kunnen we het totale aantal doorvoeringen per uur vinden:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Tot dusver gingen de query's alleen over de github\_-gebeurtenissen, maar we kunnen deze informatie combineren met github\_-gebruikers. Omdat we sharding hebben toegepast op gebruikers en gebeurtenissen met dezelfde id (`user_id`), bevinden de rijen van beide tabellen met overeenkomende gebruikers-id's zich op [dezelfde locatie](concepts-hyperscale-colocation.md), namelijk op dezelfde databaseknooppunten, en kunnen ze eenvoudig worden gekoppeld.

Als we naar `user_id` gaan, kan Hyperscale (Citus) de samenvoegbewerking in shards pushen, zodat ze tegelijkertijd kunnen worden uitgevoerd op werkknooppunten. Laten we bijvoorbeeld de gebruikers zoeken die het grootste aantal opslagplaatsen hebben gemaakt:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources in de toekomst niet nodig denkt te hebben, kunt u de servergroep verwijderen. Druk op de knop **Verwijderen** op de pagina **Overzicht** voor uw servergroep. Wanneer u daarom op een pop-uppagina wordt gevraagd, bevestigt u de naam van de servergroep en klikt u op de laatste knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Hyperscale (Citus)-servergroep inricht. U hebt psql gebruikt om hiermee verbinding te maken, u hebt een schema gemaakt en u hebt gegevens gedistribueerd.

- Volg hierna een [zelfstudie voor het bouwen van schaalbare toepassingen met meerdere tenants](./tutorial-design-database-hyperscale-multi-tenant.md)
- Bepaal de beste [begingrootte](howto-hyperscale-scale-initial.md) voor uw servergroep
