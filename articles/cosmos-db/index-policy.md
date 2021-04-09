---
title: Azure Cosmos DB indexerings beleid
description: Meer informatie over het configureren en wijzigen van het standaard indexerings beleid voor automatische indexering en betere prestaties in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: tisande
ms.openlocfilehash: 26465eb9826c60daad7b44e1c2fe6ae3c19b1ed0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378805"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexeringsbeleid in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB heeft elke container een indexeringsbeleid dat bepaalt hoe de items van de container moeten worden geïndexeerd. Het standaard indexeringsbeleid voor nieuw gemaakte containers indexeert elke eigenschap van elk item en dwingt bereikindexen af voor elke tekenreeks of elk getal. Op die manier kunt u goede query prestaties krijgen zonder dat u op de hoogte hoeft te zijn van indexering en index beheer vooraf.

In sommige gevallen wilt u mogelijk dit automatische gedrag overschrijven, zodat het beter aansluit bij uw vereisten. U kunt het indexerings beleid van een container aanpassen door de *indexerings modus* in te stellen en *eigenschaps paden* op te nemen of uit te sluiten.

> [!NOTE]
> De methode voor het bijwerken van het indexerings beleid dat in dit artikel wordt beschreven, is alleen van toepassing op de SQL-API (core) van Azure Cosmos DB. Meer informatie over indexering in [de API van Azure Cosmos DB voor MongoDb](mongodb-indexing.md)

## <a name="indexing-mode"></a>Indexerings modus

Azure Cosmos DB ondersteunt twee indexerings modi:

- **Consistent**: de index wordt synchroon bijgewerkt wanneer u items maakt, bijwerkt of verwijdert. Dit betekent dat de consistentie van uw Lees query's de [consistentie is die voor het account is geconfigureerd](consistency-levels.md).
- **Geen**: indexeren is uitgeschakeld op de container. Dit wordt meestal gebruikt wanneer een container wordt gebruikt als een pure sleutel waarde Store zonder dat hiervoor secundaire indexen nodig zijn. Het kan ook worden gebruikt om de prestaties van bulk bewerkingen te verbeteren. Nadat de bulk bewerkingen zijn voltooid, kan de index modus worden ingesteld op consistent en vervolgens worden bewaakt met behulp van de [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) totdat de bewerking is voltooid.

> [!NOTE]
> Azure Cosmos DB ondersteunt ook een vertraagde indexerings modus. Luie indexering voert updates op de index met een veel lager prioriteitsniveau uit als de engine geen andere taken uitvoert. Dit kan leiden tot **inconsistente of onvolledige** queryresultaten. Als u van plan bent om een query uit te voeren op een Cosmos-container, moet u geen luie indexering selecteren. Nieuwe containers kunnen geen luie indexering selecteren. U kunt een uitzonde ring aanvragen door contact op te nemen met de [ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) (behalve als u een Azure Cosmos-account gebruikt in de [serverloze](serverless.md) modus die geen ondersteuning biedt voor Lazy indexering).

Indexerings beleid is standaard ingesteld op `automatic` . Het wordt bereikt door de `automatic` eigenschap in het indexerings beleid in te stellen op `true` . Als u deze eigenschap instelt op, `true` kan Azure CosmosDB documenten automatisch indexeren wanneer ze zijn geschreven.

## <a name="index-size"></a><a id="index-size"></a>Index grootte

In Azure Cosmos DB is de totale hoeveelheid verbruikte opslag de combi natie van de gegevens grootte en de index grootte. Hier volgen enkele kenmerken van index grootte:

* De index grootte is afhankelijk van het indexerings beleid. Als alle eigenschappen zijn geïndexeerd, kan de index grootte groter zijn dan de grootte van de gegevens.
* Wanneer gegevens worden verwijderd, worden de indexen bijna voortdurend gecomprimeerd. Voor kleine gegevens verwijderingen is het echter mogelijk dat u niet onmiddellijk een afname van de index grootte ziet.
* De index grootte kan tijdelijk toenemen wanneer fysieke partities worden gesplitst. De index ruimte wordt vrijgegeven nadat het splitsen van de partitie is voltooid.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Eigenschaps paden opnemen en uitsluiten

Een aangepast indexerings beleid kan eigenschaps paden opgeven die expliciet worden opgenomen of uitgesloten van indexeren. Door het aantal geïndexeerde paden te optimaliseren, kunt u de latentie en de RU-kosten van schrijf bewerkingen aanzienlijk verminderen. Deze paden worden gedefinieerd volgens [de methode die wordt beschreven in de sectie Overzicht indexering](index-overview.md#from-trees-to-property-paths) met de volgende toevoegingen:

- een pad dat leidt naar een scalaire waarde (teken reeks of getal) eindigt op `/?`
- elementen uit een matrix worden samen met de `/[]` notatie (in plaats van `/0` , `/1` enzovoort) beschreven.
- het `/*` Joker teken kan worden gebruikt om alle elementen onder het knoop punt te vergelijken

Hetzelfde voor beeld opnieuw uitvoeren:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- het `headquarters` `employees` pad is `/headquarters/employees/?`

- het `locations` `country` pad is `/locations/[]/country/?`

- het pad naar iets onder `headquarters` is `/headquarters/*`

We kunnen bijvoorbeeld het `/headquarters/employees/?` pad toevoegen. Dit pad zorgt ervoor dat de eigenschap Employees wordt geïndexeerd, maar dat er geen extra geneste JSON binnen deze eigenschap zou worden geïndexeerd.

## <a name="includeexclude-strategy"></a>Strategie voor opnemen/uitsluiten

Elk indexerings beleid moet het basispad bevatten `/*` als een opgenomen of uitgesloten pad.

- Neem het hoofdpad op om selectieve paden uit te sluiten die niet hoeven te worden geïndexeerd. Dit is de aanbevolen benadering, omdat hiermee Azure Cosmos DB proactief een nieuwe eigenschap kan indexeren die aan uw model kan worden toegevoegd.
- Sluit het hoofdpad uit om selectieve paden op te nemen die moeten worden geïndexeerd.

- Voor paden met gewone tekens die bevatten: alfanumerieke tekens en _ (onderstrepings teken), hoeft u de padtekenreeks niet te escapepen rond dubbele aanhalings tekens (bijvoorbeeld '/Path/? '). Voor paden met andere speciale tekens moet u de teken reeks voor het pad Escape rond dubbele aanhalings tekens (bijvoorbeeld "/ \" Path-ABC \" /?"). Als u speciale tekens in uw pad verwacht, kunt u elk pad voor de beveiliging op te zeggen. Het maakt niet uit wat het enige verschil is als u alle paden en alleen de bestanden met speciale tekens weglaat.

- De eigenschap System `_etag` wordt standaard uitgesloten van indexeren, tenzij de ETAG wordt toegevoegd aan het opgenomen pad voor indexering.

- Als de indexerings modus is ingesteld op **consistent**, worden de systeem eigenschappen `id` `_ts` automatisch geïndexeerd.

Bij het opnemen en uitsluiten van paden kunnen de volgende kenmerken optreden:

- `kind` Dit kan ofwel `range` of zijn `hash` . Ondersteuning van hash-indexen is beperkt tot gelijkheids filters. De functionaliteit van bereik index biedt alle functionaliteit van hash-indexen, evenals efficiënte sorteer volgorde, bereik filters, systeem functies. Het gebruik van een bereik index wordt altijd aangeraden.

- `precision` is een getal dat is gedefinieerd op index niveau voor opgenomen paden. Een waarde die de `-1` maximale nauw keurigheid aangeeft. We raden u aan deze waarde altijd in te stellen op `-1` .

- `dataType` Dit kan ofwel `String` of zijn `Number` . Hiermee worden de typen JSON-eigenschappen aangegeven die worden geïndexeerd.

Als deze eigenschap niet is opgegeven, hebben deze eigenschappen de volgende standaard waarden:

| **Eigenschapsnaam**     | **Standaardwaarde** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` en `Number` |

Zie [deze sectie](how-to-manage-indexing-policy.md#indexing-policy-examples) voor voor beelden van indexerings beleid voor het opnemen en uitsluiten van paden.

## <a name="includeexclude-precedence"></a>Voor rang opnemen/uitsluiten

Als uw opgenomen paden en uitgesloten paden een conflict veroorzaken, heeft het precieze pad prioriteit.

Hier volgt een voorbeeld:

**Opgenomen pad**: `/food/ingredients/nutrition/*`

**Uitgesloten pad**: `/food/ingredients/*`

In dit geval heeft het opgenomen pad voor rang op het uitgesloten pad, omdat dit nauw keuriger is. Op basis van deze paden worden alle gegevens in het `food/ingredients` pad, of genest binnen, uitgesloten van de index. De uitzonde ring hiervan zijn gegevens in het opgenomen pad: `/food/ingredients/nutrition/*` , dat wordt geïndexeerd.

Hier volgen enkele regels voor de prioriteit van opgenomen en uitgesloten paden in Azure Cosmos DB:

- Diepere paden zijn nauw keuriger dan smallere paden. bijvoorbeeld: `/a/b/?` is nauw keuriger dan `/a/?` .

- De `/?` is nauw keuriger dan `/*` . Het `/a/?` is bijvoorbeeld nauw keuriger dan `/a/*` dat voor `/a/?` rang heeft.

- Het pad `/*` moet een opgenomen pad of uitgesloten pad zijn.

## <a name="spatial-indexes"></a>Ruimtelijke indexen

Wanneer u een ruimtelijk pad definieert in het indexerings beleid, moet u definiëren welke index ```type``` moet worden toegepast op dat pad. Mogelijke typen voor ruimtelijke indexen zijn onder andere:

* Spreek

* Polygoon

* Multi Polygon

* Lines Tring

Met Azure Cosmos DB worden standaard geen ruimtelijke indexen gemaakt. Als u ruimtelijke SQL-functies wilt gebruiken, moet u een ruimtelijke index maken op basis van de vereiste eigenschappen. Zie [deze sectie](sql-query-geospatial-index.md) voor voor beelden van indexerings beleid voor het toevoegen van ruimtelijke indexen.

## <a name="composite-indexes"></a>Samengestelde indexen

Query's die een- `ORDER BY` component met twee of meer eigenschappen hebben, hebben een samengestelde index nodig. U kunt ook een samengestelde index definiëren om de prestaties van veel gelijkheid en bereik query's te verbeteren. Standaard zijn er geen samengestelde indexen gedefinieerd, dus u moet zo nodig [samengestelde indexen toevoegen](how-to-manage-indexing-policy.md#composite-index) .

In tegens telling tot met opgenomen of uitgesloten paden kunt u geen pad maken met het `/*` Joker teken. Elk samengesteld pad heeft een impliciet `/?` aan het einde van het pad dat u niet hoeft op te geven. Samengestelde paden leiden naar een scalaire waarde. Dit is de enige waarde die wordt opgenomen in de samengestelde index.

Wanneer u een samengestelde index definieert, geeft u het volgende op:

- Twee of meer eigenschaps paden. De volg orde waarin eigenschaps paden worden gedefinieerd.

- De volg orde (oplopend of aflopend).

> [!NOTE]
> Wanneer u een samengestelde index toevoegt, gebruikt de query bestaande bereik indexen totdat de nieuwe samengestelde index toevoeging is voltooid. Daarom is het mogelijk dat u de prestatie verbeteringen niet onmiddellijk kunt waarnemen wanneer u een samengestelde index toevoegt. Het is mogelijk om de voortgang van de index transformatie [te volgen met behulp van een van de sdk's](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Volg orde van query's op meerdere eigenschappen:

De volgende overwegingen worden gebruikt bij het gebruik van samengestelde indexen voor query's met een- `ORDER BY` component met twee of meer eigenschappen:

- Als de samengestelde index paden niet overeenkomen met de volg orde van de eigenschappen in de `ORDER BY` component, kan de samengestelde index de query niet ondersteunen.

- De volg orde van samengestelde index paden (oplopend of aflopend) moet ook overeenkomen met de `order` in de- `ORDER BY` component.

- De samengestelde index ondersteunt ook een `ORDER BY` component met de tegenovergestelde volg orde op alle paden.

Bekijk het volgende voor beeld waarbij een samengestelde index is gedefinieerd voor de eigenschappen naam, leeftijd en _ts:

| **Samengestelde index**     | **Voorbeeld `ORDER BY` query**      | **Ondersteund door samengestelde index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

U moet het indexerings beleid aanpassen zodat u alle benodigde query's kunt uitvoeren `ORDER BY` .

### <a name="queries-with-filters-on-multiple-properties"></a>Query's met filters op meerdere eigenschappen

Als een query filters heeft voor twee of meer eigenschappen, kan het handig zijn om een samengestelde index voor deze eigenschappen te maken.

Bekijk bijvoorbeeld de volgende query met zowel een gelijkheids-als bereik filter:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

Deze query is efficiënter, neemt minder tijd in beslag en verbruikt meer dan één RU, als een samengestelde index op kan worden gebruikt `(name ASC, age ASC)` .

Query's met meerdere bereik filters kunnen ook worden geoptimaliseerd met een samengestelde index. Elke afzonderlijke samengestelde index kan echter slechts één bereik filter optimaliseren. Bereik filters zijn onder andere `>` ,, `<` `<=` , `>=` en `!=` . Het bereik filter moet als laatste worden gedefinieerd in de samengestelde index.

Houd rekening met de volgende query met een gelijkheids filter en twee bereik filters:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

Deze query is efficiënter met een samengestelde index op `(name ASC, age ASC)` en `(name ASC, _ts ASC)` . De query maakt echter geen gebruik van een samengestelde index op `(age ASC, name ASC)` omdat de eigenschappen met gelijkheids filters eerst moeten worden gedefinieerd in de samengestelde index. Er zijn twee afzonderlijke samengestelde indexen vereist in plaats van één samengestelde index, `(name ASC, age ASC, _ts ASC)` omdat elke samengestelde index alleen een filter voor één bereik kan optimaliseren.

De volgende overwegingen worden gebruikt bij het maken van samengestelde indexen voor query's met filters voor meerdere eigenschappen

- Filter expressies kunnen gebruikmaken van meerdere samengestelde indexen.
- De eigenschappen in het filter van de query moeten overeenkomen met die in de samengestelde index. Als een eigenschap zich in de samengestelde index bevindt maar niet is opgenomen in de query als een filter, wordt de samengestelde index niet gebruikt voor de query.
- Als een query extra eigenschappen in het filter heeft die niet in een samengestelde index zijn gedefinieerd, wordt een combi natie van samengestelde en bereik indexen gebruikt om de query te evalueren. Hiervoor is minder RU vereist dan exclusief het gebruik van bereik indexen.
- Als een eigenschap een bereik filter heeft ( `>` ,,, `<` `<=` `>=` of `!=` ), dan moet deze eigenschap als laatste worden gedefinieerd in de samengestelde index. Als een query meer dan één bereik filter heeft, kan het profiteren van meerdere samengestelde indexen.
- Bij het maken van een samengestelde index voor het optimaliseren van query's met meerdere filters, `ORDER` heeft de samengestelde index geen invloed op de resultaten. Deze eigenschap is optioneel.

Bekijk de volgende voor beelden waarin een samengestelde index is gedefinieerd voor de eigenschappen naam, leeftijd en tijds tempel:

| **Samengestelde index**     | **Voorbeeld query**      | **Ondersteund door samengestelde index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>Query's met een filter en sorteren op

Als een query filtert op een of meer eigenschappen en andere eigenschappen in de ORDER BY-component heeft, kan het handig zijn om de eigenschappen in het filter toe te voegen aan de- `ORDER BY` component.

Als u bijvoorbeeld de eigenschappen in het filter aan de- `ORDER BY` component toevoegt, kan de volgende query worden herschreven om gebruik te maken van een samengestelde index:

Query's uitvoeren met de bereik index:

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

Query's uitvoeren met samengestelde index:

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

Dezelfde query optimalisaties kunnen worden gegeneraliseerd voor `ORDER BY` query's met filters, zodat afzonderlijke samengestelde indexen slechts één bereik filter kunnen ondersteunen.

Query's uitvoeren met de bereik index:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

Query's uitvoeren met samengestelde index:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

Daarnaast kunt u samengestelde indexen gebruiken om query's te optimaliseren met systeem functies en volg orde op:

Query's uitvoeren met de bereik index:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

Query's uitvoeren met samengestelde index:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

De volgende overwegingen zijn van toepassing bij het maken van samengestelde indexen voor het optimaliseren van een query met een filter en een `ORDER BY` component:

* Als u geen samengestelde index definieert voor een query met een filter voor één eigenschap en een afzonderlijke `ORDER BY` component met behulp van een andere eigenschap, zal de query toch slagen. De RU-kosten van de query kunnen echter worden verminderd met een samengestelde index, met name als de eigenschap in de `ORDER BY` component een hoge kardinaliteit heeft.
* Als de query filtert op Eigenschappen, moeten deze eerst worden opgenomen in de `ORDER BY` component.
* Als de query filtert op meerdere eigenschappen, moeten de gelijkheids filters de eerste eigenschappen in de `ORDER BY` component zijn.
* Als de query filtert op meerdere eigenschappen, kunt u Maxi maal één bereik filter of systeem functie gebruiken per samengestelde index. De eigenschap die in het bereik filter of de systeem functie wordt gebruikt, moet als laatste worden gedefinieerd in de samengestelde index.
* Alle overwegingen voor het maken van samengestelde indexen voor `ORDER BY` query's met meerdere eigenschappen en query's met filters voor meerdere eigenschappen zijn nog steeds van toepassing.


| **Samengestelde index**                      | **Voorbeeld `ORDER BY` query**                                  | **Ondersteund door samengestelde index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

### <a name="queries-with-a-filter-and-an-aggregate"></a>Query's met een filter en een statistische functie 

Als een query filtert op een of meer eigenschappen en een statistische systeem functie heeft, kan het handig zijn om een samengestelde index te maken voor de eigenschappen in de functie filter en aggregatie systeem. Deze optimalisatie is van toepassing op de [Sum](sql-query-aggregate-sum.md) -en [AVG](sql-query-aggregate-avg.md) -systeem functies.

De volgende overwegingen zijn van toepassing bij het maken van samengestelde indexen voor het optimaliseren van een query met een filter en een statistische systeem functie.

* Samengestelde indexen zijn optioneel bij het uitvoeren van query's met aggregaties. De RU-kosten van de query kunnen echter vaak aanzienlijk worden verkleind met een samengestelde index.
* Als de query filtert op meerdere eigenschappen, moeten de gelijkheids filters de eerste eigenschappen in de samengestelde index zijn.
* U kunt Maxi maal één bereik filter per samengestelde index hebben en dit moet zich bevindt op de eigenschap in de statistische systeem functie.
* De eigenschap in de functie aggregate systeem moet als laatste worden gedefinieerd in de samengestelde index.
* Dit `order` `ASC` `DESC` is niet van belang.

| **Samengestelde index**                      | **Voorbeeld query**                                  | **Ondersteund door samengestelde index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name > "John"``` | `No` |
| ```(name ASC, age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age = 25``` | `Yes` |
| ```(age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age > 25``` | `No` |

## <a name="index-transformationmodifying-the-indexing-policy"></a><index-Transform>het wijzigen van het indexerings beleid

Het indexerings beleid van een container kan op elk gewenst moment worden bijgewerkt [met behulp van de Azure portal of een van de ondersteunde sdk's](how-to-manage-indexing-policy.md). Een update voor het indexerings beleid activeert een trans formatie van de oude index naar de nieuwe, die online en in-place wordt uitgevoerd (zodat er geen extra opslag ruimte wordt verbruikt tijdens de bewerking). Het oude indexerings beleid is efficiënt getransformeerd naar het nieuwe beleid zonder dat dit van invloed is op de beschik baarheid, lees Beschik baarheid of de door Voer die is ingericht op de container. Index transformatie is een asynchrone bewerking en de tijd die nodig is om te volt ooien, is afhankelijk van de ingerichte door Voer, het aantal items en de grootte ervan.

> [!IMPORTANT]
> Index transformatie is een bewerking waarbij [aanvraag eenheden](request-units.md)worden verbruikt. Aanvraag eenheden die worden gebruikt door een index transformatie worden momenteel niet gefactureerd als u [serverloze](serverless.md) containers gebruikt. Deze aanvraag eenheden worden gefactureerd als serverloos algemeen beschikbaar is.

> [!NOTE]
> Het is mogelijk om de voortgang van de index transformatie [te volgen met behulp van een van de sdk's](how-to-manage-indexing-policy.md).

Er zijn geen gevolgen voor het schrijven van de beschik baarheid tijdens index transformaties. De index transformatie gebruikt uw ingerichte RUs, maar met een lagere prioriteit dan uw ruwe bewerkingen of query's.

Er is geen invloed op de Lees Beschik baarheid bij het toevoegen van een nieuwe index. Met query's worden alleen nieuwe indexen gebruikt wanneer de index transformatie is voltooid. Tijdens de index transformatie zal de query-engine bestaande indexen blijven gebruiken. u ziet dan vergelijk bare Lees prestaties tijdens de indexerings transformatie om te zien wat u hebt gezien voordat de index wijziging werd geïnitieerd. Bij het toevoegen van nieuwe indexen is er ook geen risico van onvolledige of inconsistente query resultaten.

Bij het verwijderen van indexen en het direct uitvoeren van query's die filteren op de verwijderde indexen, is er geen garantie voor consistente of volledige query resultaten. Als u meerdere indexen verwijdert en dit doet in één wijziging van het indexerings beleid, biedt de query-engine consistente en volledige resultaten tijdens de index transformatie. Als u indexen verwijdert via meerdere wijzigingen in het indexerings beleid, biedt de query-engine echter geen consistente of volledige resultaten tot alle index transformaties zijn voltooid. De meeste ontwikkel aars kunnen geen indexen verwijderen en vervolgens onmiddellijk proberen om query's uit te voeren die gebruikmaken van deze indexen, zodat deze situatie in de praktijk niet waarschijnlijk is.

> [!NOTE]
> Waar mogelijk moet u altijd proberen meerdere index wijzigingen in één wijziging in een indexerings beleid te groeperen

## <a name="indexing-policies-and-ttl"></a>Indexerings beleid en TTL

Het gebruik van de [functie time-to-Live (TTL)](time-to-live.md) vereist indexering. Dit betekent dat:

- het is niet mogelijk om TTL te activeren voor een container waarop de indexerings modus is ingesteld `none` ,
- het is niet mogelijk om de indexerings modus in te stellen op geen in een container waarin TTL wordt geactiveerd.

Als er geen pad naar een eigenschap moet worden geïndexeerd, maar TTL vereist is, kunt u een indexerings beleid gebruiken waarbij een indexerings modus `consistent` is ingesteld op, geen opgenomen paden en `/*` als alleen uitgesloten pad.

## <a name="next-steps"></a>Volgende stappen

Lees meer over indexeren in de volgende artikelen:

- [Overzicht van indexeren](index-overview.md)
- [Indexerings beleid beheren](how-to-manage-indexing-policy.md)
