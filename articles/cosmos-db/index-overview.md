---
title: Indexeren in Azure Cosmos DB
description: Begrijpen hoe indexering werkt in Azure Cosmos DB, verschillende typen indexen, zoals bereik, ruimtelijke, samengestelde indexen worden ondersteund.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: b7349a08b93810dcc3befd6058302d6c4573ab8d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019207"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexeren in Azure Cosmos DB - overzicht
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB is een schema-neutraal-data base waarmee u de toepassing kunt herhalen zonder schema-of index beheer. Standaard indexeert Azure Cosmos DB automatisch elke eigenschap voor alle items in uw [container](account-databases-containers-items.md#azure-cosmos-containers) zonder schema te hoeven definiëren of secundaire indexen te configureren.

Het doel van dit artikel is om uit te leggen hoe gegevens worden geïndexeerd met Azure Cosmos DB en hoe indexen worden gebruikt om de queryprestaties te verbeteren. U wordt aangeraden deze sectie door te lopen voordat u het [indexerings beleid](index-policy.md)aanpast.

## <a name="from-items-to-trees"></a>Van items naar bomen

Telkens wanneer een item wordt opgeslagen in een container, wordt de inhoud ervan geprojecteerd als een JSON-document en vervolgens omgezet in een structuur weergave. Dat betekent dat elke eigenschap van dat item wordt weer gegeven als een knoop punt in een boom structuur. Er wordt een pseudo-hoofd knooppunt gemaakt als bovenliggend knoop punt voor alle eigenschappen van het eerste niveau van het item. De blad knooppunten bevatten de werkelijke scalaire waarden die door een item worden uitgevoerd.

Bekijk bijvoorbeeld dit item:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Deze wordt weer gegeven in de volgende structuur:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="Het vorige item wordt weer gegeven als een boom structuur" border="false":::

U ziet hoe matrices worden gecodeerd in de boom structuur: elke vermelding in een matrix haalt een tussenliggend knoop punt op dat is gelabeld met de index van die vermelding in de matrix (0, 1 enz.).

## <a name="from-trees-to-property-paths"></a>Van structuren naar eigenschaps paden

De reden waarom Azure Cosmos DB het transformeren van items naar bomen, is omdat hiermee kan worden verwezen naar de paden binnen deze trees. Om het pad voor een eigenschap op te halen, kunnen we de structuur van het hoofd knooppunt naar die eigenschap door lopen en de labels van elk gepasseerd knoop punt samen voegen.

Dit zijn de paden voor elke eigenschap van het hierboven beschreven voor beeld-item:

- /locations/0/Country: "Duitsland"
- /locations/0/City: "Berlijn"
- /locations/1/Country: "Frank rijk"
- /locations/1/City: "Parijs"
- /Headquarters/Country: "België"
- /Headquarters/Employees: 250
- /exports/0/City: "Moskou"
- /exports/1/City: "Athene"

Wanneer een item wordt geschreven, indexeert Azure Cosmos DB in feite elke eigenschap van het pad en de bijbehorende waarde.

## <a name="types-of-indexes"></a><a id="index-types"></a>Typen indexen

Azure Cosmos DB ondersteunt momenteel drie typen indexen. U kunt deze index typen configureren wanneer u het indexerings beleid definieert.

### <a name="range-index"></a>Bereik index

De **bereik** index is gebaseerd op een geordende boom structuur. Het index type van het bereik wordt gebruikt voor:

- Gelijkheids query's:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Gelijkheids overeenkomst voor een matrix element
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Bereik query's:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (werkt voor `>` , `<` ,,, `>=` `<=` , `!=` )

- Controleren op de aanwezigheid van een eigenschap:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Teken reeks systeem functies:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` aanvragen

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` aanvragen

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Bereik indexen kunnen worden gebruikt voor scalaire waarden (teken reeks of getal). Het standaardindexeringsbeleid voor nieuw gemaakte containers dwingt bereikindexen af voor een willekeurige tekenreeks of een willekeurig getal. Zie [voor beelden van bereik indexerings beleid](how-to-manage-indexing-policy.md#range-index) voor meer informatie over het configureren van bereik indexen

### <a name="spatial-index"></a>Ruimtelijke index

Met **ruimtelijke** indexen kunnen efficiënte query's worden uitgevoerd op georuimtelijke objecten zoals-Points, lijnen, veelhoeken en multiveelhoeken. Deze query's gebruiken ST_DISTANCE, ST_WITHIN ST_INTERSECTS tref woorden. Hieronder ziet u enkele voor beelden van ruimtelijke-index type:

- Georuimtelijke afstand query's:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Georuimtelijk binnen query's:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- Georuimtelijke Intersect-query's:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Ruimtelijke indexen kunnen worden gebruikt op correct opgemaakte [GEOjson](./sql-query-geospatial-intro.md) -objecten. Punten, line strings toe, veelhoeken en multiveelhoeken worden momenteel ondersteund. Als u dit index type wilt gebruiken, stelt u deze in met behulp van de `"kind": "Range"` eigenschap bij het configureren van het indexerings beleid. Zie [voor beelden van ruimtelijke indexerings beleid](how-to-manage-indexing-policy.md#spatial-index) voor meer informatie over het configureren van ruimtelijke indexen

### <a name="composite-indexes"></a>Samengestelde indexen

**Samengestelde** indexen verhogen de efficiëntie wanneer u bewerkingen uitvoert op meerdere velden. Het samengestelde-index type wordt gebruikt voor:

- `ORDER BY` query's op meerdere eigenschappen:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Query's met een filter en `ORDER BY` . Deze query's kunnen gebruikmaken van een samengestelde index als de eigenschap filter wordt toegevoegd aan de `ORDER BY` component.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Query's met een filter voor twee of meer eigenschappen waarbij ten minste één eigenschap een gelijkheids filter is

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Zolang één filter predicaat een van het index type gebruikt, evalueert de query-engine dat eerst voordat de rest wordt gescand. Als u bijvoorbeeld een SQL-query hebt zoals `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Met de bovenstaande query wordt eerst gefilterd op vermeldingen waarbij voor naam "Andrew" met behulp van de index. Vervolgens worden alle voor naam = "Andrew" vermeldingen door gegeven aan een volgende pijp lijn om het CONTAINs-filter predicaat te evalueren.

* U kunt query's versnellen en volledige container scans voor komen met behulp van functies die de index niet gebruiken (bijvoorbeeld CONTAINs) door extra filter predikaten toe te voegen die de index gebruiken. De volg orde van de filter componenten is niet belang rijk. De query-engine geeft aan welke predikaten meer selectief zijn en voeren de query dienovereenkomstig uit.

Zie [samengesteld indexerings beleid voor beelden](how-to-manage-indexing-policy.md#composite-index) van meer informatie over het configureren van samengestelde indexen

## <a name="querying-with-indexes"></a>Query's uitvoeren met indexen

De paden die worden geëxtraheerd bij het indexeren van gegevens, maken het gemakkelijker om de index bij het verwerken van een query te zoeken. Door de `WHERE` component van een query te vergelijken met de lijst met geïndexeerde paden, is het mogelijk om de items te identificeren die overeenkomen met het query predicaat zeer snel.

Bekijk bijvoorbeeld de volgende query: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . Het query predicaat (filteren op items, waarbij een wille keurige locatie ' Frank rijk ' heeft als land/regio) zou overeenkomen met het pad dat in rood is gemarkeerd:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="Een specifiek pad binnen een structuur zoeken" border="false":::

> [!NOTE]
> Een `ORDER BY` component waarbij orders met één eigenschap *altijd* een bereik index nodig heeft en mislukt als het pad waarnaar wordt verwezen, niet is opgenomen. Op dezelfde manier `ORDER BY` heeft een query die door meerdere eigenschappen orders *altijd* een samengestelde index nodig.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over indexeren vindt u in de volgende artikelen:

- [Indexeringsbeleid](index-policy.md)
- [Indexerings beleid beheren](how-to-manage-indexing-policy.md)