---
title: 'Zelfstudie: Een realtime database ontwerpen - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: Deze zelfstudie laat zien hoe u realtime dashboardquery's kunt parallelliseren met Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026264"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Zelfstudie: Zelfstudie: een database met realtime analyses ontwerpen met behulp van Azure Database for PostgreSQL – Hyperscale (Citus)

In deze zelfstudie gebruikt u Azure Database for PostgreSQL - Hyperscale (Citus) om te leren hoe u de volgende bewerkingen uitvoert:

> [!div class="checklist"]
> * Een Hyperscale (Citus)-servergroep maken
> * Het hulpprogramma psql gebruiken om een schema te maken
> * Sharding toepassen op tabellen tussen knooppunten
> * Voorbeeldgegevens genereren
> * Rollups uitvoeren
> * Query's uitvoeren op onbewerkte en geaggregeerde gegevens
> * Gegevens laten verlopen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Het hulpprogramma psql gebruiken om een schema te maken

Zodra u verbinding hebt gemaakt met de Azure Database for PostgreSQL - Hyperscale (Citus) met behulp van psql, kunt u een aantal basistaken voltooien. In deze zelfstudie wordt uitgelegd hoe u verkeersgegevens opneemt vanuit Web Analytics en vervolgens de gegevens samenvoegt om dashboards in realtime te bieden op basis van die gegevens.

We gaan een tabel maken waarin al onze onbewerkte webverkeersgegevens worden gebruikt. Voer de volgende opdrachten uit in de psql-terminal:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

U gaat ook een tabel maken waarin onze aggregaties per minuut worden bewaard, en een tabel die de positie van onze laatste rollup behoudt. Voer de volgende opdrachten ook in psql uit:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

U kunt de zojuist gemaakte tabellen nu in de lijst met tabellen in psql zien met deze psql-opdracht:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Sharding toepassen op tabellen tussen knooppunten

In een hyperscale-implementatie worden tabelrijen op verschillende knooppunten opgeslagen op basis van de waarde van een door de gebruiker opgegeven kolom. Deze 'distributiekolom' markeert hoe sharding op deze gegevens worden toegepast tussen knooppunten.

We gaan de distributiekolom instellen als site\_id, de shardsleutel. Voer in psql deze functies uit:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu moet onze servergroep gereed zijn om gegevens op te nemen. We kunnen het volgende lokaal uitvoeren vanaf onze verbinding `psql` om continu gegevens in te voegen.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

De query voegt elke seconde ongeveer acht rijen in. De rijen worden op verschillende werkknooppunten opgeslagen, aangestuurd door het distributiekolom, `site_id`.

   > [!NOTE]
   > Laat de query voor het genereren van gegevens lopen en open een tweede psql-verbinding voor de resterende opdrachten in deze zelfstudie.
   >

## <a name="query"></a>Query’s uitvoeren

Met de optie voor hyperscale hosten kunnen meerdere knooppunten gelijktijdig query's verwerken om het proces sneller te laten verlopen. De database berekent bijvoorbeeld aggregaties zoals SUM en COUNT op werkknooppunten en combineert de resultaten in een eindantwoord.

Hier volgt een query voor het tellen van webaanvragen per minuut, samen met een aantal statistieken.
Probeer de query in psql uit te voeren en bekijk de resultaten.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Rollup van gegevens

De vorige query werkt in de vroege stadia prima, maar de prestaties verslechteren naarmate uw gegevens worden geschaald. Zelfs bij gedistribueerde verwerking is het sneller om de gegevens vooraf te berekenen dan om deze herhaaldelijk opnieuw te berekenen.

We kunnen zorgen het dashboard snel blijft werken door de onbewerkte gegevens regelmatig in een aggregatietabel te verdelen. U kunt experimenteren met de aggregatieduur. We hebben een aggregatietabel per minuut gebruikt, maar u kunt gegevens in plaats daarvan in vijf, 15 of 60 minuten indelen.

Als u deze rollup eenvoudiger wilt uitvoeren, gaan we deze in een plpgsql-functie plaatsen. Voer deze opdrachten uit in psql om de functie `rollup_http_request` te maken.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Als onze functie is geïmplementeerd, voert u deze uit om de gegevens samen te tellen:

```sql
SELECT rollup_http_request();
```

En met onze gegevens in een vooraf geaggregeerd formulier kunnen we een query uitvoeren op het rolluptabel om hetzelfde rapport te verkrijgen als eerder. Voer de volgende query uit:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Oude gegevens laten verlopen

De rollups maken query's sneller, maar we moeten nog steeds oude gegevens laten verlopen om overmatige opslagkosten te voorkomen. Bepaal hoelang u gegevens voor elke granulariteit wilt behouden en gebruik standaardquery's om verlopen gegevens te verwijderen. In het volgende voorbeeld hebben we besloten om onbewerkte gegevens voor één dag en aggregaties per minuut gedurende één maand te houden:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Tijdens de productie kunt u deze query's in een functie laten teruglopen en deze elke minuut aanroepen in een cron-taak.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources in de toekomst niet nodig denkt te hebben, kunt u de servergroep verwijderen. Druk op de knop *Verwijderen* op de pagina *Overzicht* voor uw servergroep. Wanneer u daarom op een pop-uppagina wordt gevraagd, bevestigt u de naam van de servergroep en klikt u op de laatste knop *Verwijderen*.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Hyperscale (Citus)-servergroep inricht. U hebt psql gebruikt om hiermee verbinding te maken, u hebt een schema gemaakt en u hebt gegevens gedistribueerd. U hebt geleerd hoe u gegevens in onbewerkte vorm opvraagt, regelmatig de gegevens aggregeert, query's uitvoert op de geaggregeerde tabellen en oude gegevens laat verlopen.

- Meer informatie over [knooppunttypen](./concepts-hyperscale-nodes.md) voor servergroepen
- Bepaal de beste [begingrootte](howto-hyperscale-scale-initial.md) voor uw servergroep
