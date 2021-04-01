---
title: Table colocation-grootschalige (Citus)-Azure Database for PostgreSQL
description: Verwante informatie samen opslaan voor snellere query's
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90884430"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Tabel co-locatie in Azure Database for PostgreSQL – grootschalige (Citus)

Co-locatie betekent dat verwante informatie samen op dezelfde knoop punten wordt opgeslagen. Query's kunnen snel worden uitgevoerd wanneer alle benodigde gegevens beschikbaar zijn zonder netwerk verkeer. Als u gerelateerde gegevens op verschillende knoop punten wilt verplaatsen, kunt u query's efficiënt uitvoeren op elk knoop punt.

## <a name="data-colocation-for-hash-distributed-tables"></a>Gegevens locatie voor op hash-gedistribueerde tabellen

In Azure Database for PostgreSQL – grootschalige (Citus), een rij wordt opgeslagen in een Shard als de hash van de waarde in de kolom distributie binnen het hash-bereik van de Shard valt. Shards met hetzelfde hash-bereik worden altijd op hetzelfde knoop punt geplaatst. Rijen met gelijke distributie kolom waarden bevinden zich altijd op hetzelfde knoop punt in tabellen.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="Shards":::

## <a name="a-practical-example-of-colocation"></a>Een praktisch voor beeld van een co-locatie

Bekijk de volgende tabellen die deel kunnen uitmaken van een multi tenant Web Analytics SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Nu willen we query's beantwoorden die kunnen worden uitgegeven door een klant gericht dash board. Een voorbeeld query is ' het aantal bezoeken in de afgelopen week retour neren voor alle pagina's die beginnen met '/blog ' in de Tenant zes. '

Als de gegevens in de Single-Server-implementatie optie waren, kunnen we onze query eenvoudig met behulp van de uitgebreide set relationele bewerkingen die worden aangeboden door SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Zolang de [werkset](https://en.wikipedia.org/wiki/Working_set) voor deze query in het geheugen past, is een tabel met één server een geschikte oplossing. Laten we eens kijken naar de mogelijkheden voor het schalen van het gegevens model met de implementatie optie grootschalige (Citus).

### <a name="distribute-tables-by-id"></a>Tabellen verdelen op basis van ID

Query's met één server worden langzamer gestart naarmate het aantal tenants en de gegevens die voor elke Tenant zijn opgeslagen toenemen. De werkset stopt met het aanpassen van het geheugen en de CPU wordt een knel punt.

In dit geval kunnen we de gegevens op verschillende knoop punten Shard met behulp van grootschalige (Citus). De eerste en belangrijkste keuze die we moeten maken wanneer we bepalen dat Shard de distributie kolom is. Laten we beginnen met een Naïve-keuze `event_id` voor de gebeurtenis tabel en `page_id` voor de `page` tabel:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Wanneer gegevens worden verspreid over verschillende werk rollen, kunnen we geen samen voegen uitvoeren zoals we op één PostgreSQL-knoop punt zouden doen. In plaats daarvan moeten er twee query's worden uitgevoerd:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Daarna moeten de resultaten van de twee stappen worden gecombineerd door de toepassing.

Het uitvoeren van de query's moet gegevens raadplegen in Shards verspreid over verschillende knoop punten.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="Inefficiënte query's":::

In dit geval maakt de gegevens distributie aanzienlijke nadelen:

-   Overhead van het uitvoeren van query's op elke Shard en het uitvoeren van meerdere query's.
-   Overhead van Q1 waarbij veel rijen worden geretourneerd naar de client.
-   Q2 wordt groot.
-   De nood zaak om query's te schrijven in meerdere stappen vereist wijzigingen in de toepassing.

De gegevens worden verspreid, zodat de query's kunnen worden geparallelleerd. Het is alleen nuttig als de hoeveelheid werk die de query uitvoert, aanzienlijk groter is dan de overhead van het uitvoeren van query's op veel Shards.

### <a name="distribute-tables-by-tenant"></a>Tabellen distribueren op Tenant

In grootschalige (Citus) zijn rijen met dezelfde waarde voor de distributie kolom gegarandeerd dat deze zich op hetzelfde knoop punt bevinden. Als u begint met het maken van de tabel, kunnen we de tabellen met `tenant_id` de kolom distributie aanmaken.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nu grootschalige (Citus) kan de oorspronkelijke query met één server zonder aanpassing beantwoorden (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Vanwege het filter en de samen voeging van tenant_id, weet grootschalige (Citus) dat de volledige query kan worden beantwoord met behulp van de set met Shards die de gegevens voor die specifieke Tenant bevatten. Een enkel PostgreSQL-knoop punt kan de query in één stap beantwoorden.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="Betere query":::

In sommige gevallen moeten query's en tabel schema's worden gewijzigd om de Tenant-ID op te nemen in unieke beperkingen en voor waarden voor samen voegen. Deze wijziging is doorgaans eenvoudig.

## <a name="next-steps"></a>Volgende stappen

- Bekijk hoe Tenant gegevens in de [multi-tenant zelf studie](tutorial-design-database-hyperscale-multi-tenant.md)worden geplaatst.
