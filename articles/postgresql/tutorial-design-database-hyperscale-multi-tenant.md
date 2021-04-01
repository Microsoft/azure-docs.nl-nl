---
title: 'Zelfstudie: Een database met meerdere tenants ontwerpen - Hyperscale (Citus) - Azure Database for PostgreSQL'
description: Deze zelfstudie laat zien hoe u een toepassing met meerdere tenants kunt baseren op Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: d8dee086f55c5596af50245b5271d98536eb7e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026196"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Zelfstudie: een database met meerdere tenants ontwerpen met behulp van Azure Database for PostgreSQL – Hyperscale (Citus)

In deze zelfstudie gebruikt u Azure Database for PostgreSQL - Hyperscale (Citus) om te leren hoe u de volgende bewerkingen uitvoert:

> [!div class="checklist"]
> * Een Hyperscale (Citus)-servergroep maken
> * Het hulpprogramma psql gebruiken om een schema te maken
> * Sharding toepassen op tabellen tussen knooppunten
> * Voorbeeldgegevens opnemen
> * Query's uitvoeren op tenantgegevens
> * Gegevens tussen tenants delen
> * Het schema per tenant aanpassen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Het hulpprogramma psql gebruiken om een schema te maken

Zodra u verbinding hebt gemaakt met de Azure Database for PostgreSQL - Hyperscale (Citus) met behulp van psql, kunt u een aantal basistaken voltooien. In deze zelfstudie krijgt u instructies voor het maken van een web-app waarmee adverteerders hun campagnes kunnen volgen.

Meerdere bedrijven kunnen gebruik maken van de app, dus we maken een tabel waarin we bedrijven opslaan en een tweede tabel voor hun campagnes. Voer op de psql-console deze opdrachten uit:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Per campagne wordt betaald voor het uitvoeren van advertenties. Voeg ook een tabel voor advertenties toe, door in psql de volgende code na de bovenstaande code uit te voeren:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Als laatste houden we de statistieken bij over klikbewegingen en de indruk van elke advertentie:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

U kunt de zojuist gemaakte tabellen nu in de lijst met tabellen in psql zien door het volgende uit te voeren:

```postgres
\dt
```

Voor toepassingen met meerdere tenants kan uitsluitend per tenant uniekheid worden afgedwongen, daarom bevatten alle primaire en refererende sleutels de bedrijfs-id.

## <a name="shard-tables-across-nodes"></a>Sharding toepassen op tabellen tussen knooppunten

In een hyperscale-implementatie worden tabelrijen op verschillende knooppunten opgeslagen op basis van de waarde van een door de gebruiker opgegeven kolom. Deze 'distributiekolom' geeft aan welke tenant de eigenaar is van welke rijen.

Stel de distributiekolom in op de bedrijfs\_-id, de tenant-id. Voer in psql deze functies uit:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Voorbeeldgegevens opnemen

Download nu buiten psql, in de normale opdrachtregel, voorbeeldgegevenssets:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Ga terug naar psql en laad de gegevens bulksgewijs. Vergeet niet dat u psql moet uitvoeren in dezelfde map als waarin u de gegevensbestanden hebt gedownload.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Deze gegevens worden nu over werkknooppunten verspreid.

## <a name="query-tenant-data"></a>Query's uitvoeren op tenantgegevens

Wanneer de toepassing om gegevens voor één tenant vraagt, kunt u via de database de query op één werkknooppunt uitvoeren. Query's voor één tenant worden op één tenant-id gefilterd. Met de volgende query wordt bijvoorbeeld `company_id = 5` gefilterd voor advertenties en indrukken. Probeer de query in psql uit te voeren om de resultaten te zien.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Gegevens tussen tenants delen

Tot nu toe zijn alle tabellen gedistribueerd door `company_id`, maar een aantal gegevens 'hoort' niet specifiek van nature bij elke tenant, deze kunnen worden gedeeld. Alle bedrijven op het voorbeeldadvertentieplatform zoeken bijvoorbeeld geografische informatie voor hun doelgroep op basis van IP-adressen.

Maak een tabel om daarin gedeelde geografische informatie op te slaan. Voer de volgende opdrachten uit in psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Maak nu `geo_ips` een 'referentietabel' om een kopie van de tabel op te slaan op elk werkknooppunt.

```sql
SELECT create_reference_table('geo_ips');
```

Laad hierin voorbeeldgegevens. Vergeet niet deze opdracht in psql uit te voeren vanuit de map waarin u de gegevensset hebt gedownload.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Het samenvoegen van de tabel met het aantal klikbewegingen met geo\_ips is efficiënt op alle knooppunten.
Hier ziet u een join om de locatie te zoeken van iedereen die op een advertentie heeft geklikt
290. Probeer de query uit te voeren in psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Het schema per tenant aanpassen

In elke tenant wordt mogelijk speciale informatie opgeslagen niet anderen niet nodig hebben. Alle tenants delen echter een algemene infrastructuur met een identiek databaseschema. Waar kunt u de extra gegevens opslaan?

U kunt bijvoorbeeld een kolomtype met een open einde gebruiken, zoals JSONB van PostgreSQL.  Ons schema bevat een JSONB-veld in `clicks` met de naam `user_data`.
Een bedrijf (laten we het bedrijf Vijf noemen), kan de kolom gebruiken om bij te houden of de gebruiker een mobiel apparaat gebruikt.

Hier ziet u een query om te ontdekken wie er vaker klikt: mobiele of traditionele bezoekers.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

We kunnen deze query voor één bedrijf optimaliseren door het maken van een [gedeeltelijke index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Algemener gezegd: we kunnen [GIN-indices](https://www.postgresql.org/docs/current/static/gin-intro.html) maken op elke sleutel en waarde binnen de kolom.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources in de toekomst niet nodig denkt te hebben, kunt u de servergroep verwijderen. Druk op de knop *Verwijderen* op de pagina *Overzicht* voor uw servergroep. Wanneer u daarom op een pop-uppagina wordt gevraagd, bevestigt u de naam van de servergroep en klikt u op de laatste knop *Verwijderen*.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Hyperscale (Citus)-servergroep inricht. U hebt psql gebruikt om hiermee verbinding te maken, u hebt een schema gemaakt en u hebt gegevens gedistribueerd. U hebt geleerd om een query uit te voeren op gegevens zowel in als tussen tenants en om het schema per tenant aan te passen.

- Meer informatie over [knooppunttypen](./concepts-hyperscale-nodes.md) voor servergroepen
- Bepaal de beste [begingrootte](howto-hyperscale-scale-initial.md) voor uw servergroep
