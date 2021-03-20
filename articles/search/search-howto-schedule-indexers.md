---
title: Uitvoering van de Indexeer functie plannen
titleSuffix: Azure Cognitive Search
description: Plan Azure Cognitive Search Indexeer functies om inhoud periodiek of op een bepaald tijdstip te indexeren.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097973"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Indexeer functies plannen in azure Cognitive Search

Een Indexeer functie wordt normaal gesp roken eenmaal uitgevoerd, direct nadat deze is gemaakt. Daarna kunt u de app op aanvraag opnieuw uitvoeren met behulp van Azure Portal, [Indexeer functie (rest)](/rest/api/searchservice/run-indexer)of een Azure-SDK uitvoeren. U kunt ook een Indexeer functie configureren zodat deze volgens een schema kan worden uitgevoerd. Hieronder vindt u een aantal situaties waarin de planning van de Indexeer functie nuttig is:

* De bron gegevens worden na verloop van tijd gewijzigd en u wilt dat de zoek index automatisch het verschil verwerkt.

* De bron gegevens zijn erg groot en u wilt de verwerking van de Indexeer functie gedurende een periode spreiden. Voor indexerings taken geldt een maximale uitvoerings tijd van 24 uur voor reguliere gegevens bronnen en 2 uur voor Indexeer functies met vaardig heden. Als het indexeren niet binnen het maximum interval kan worden voltooid, kunt u een schema configureren dat elke 2 uur wordt uitgevoerd. Indexeer functies kunnen automatisch ophalen waar ze zich bevinden, zoals aangegeven door een interne bovengrens voor het indexeren van de laatste beëindiging. Als u een Indexeer functie uitvoert op een terugkerende periode van 2 uur, kan deze een zeer grote gegevensset (veel miljoenen documenten) verwerken die groter is dan het interval dat is toegestaan voor één taak. Zie voor meer informatie over het indexeren van grote gegevens volumes [grote gegevens sets indexeren in Azure Cognitive Search](search-howto-large-index.md).

* Een zoek index wordt gevuld met meerdere gegevens bronnen en u wilt dat de Indexeer functies op verschillende tijdstippen worden uitgevoerd om conflicten te verminderen.

Een planning kan er als volgt uitzien: vanaf 1 januari en wordt elke 50 minuten uitgevoerd.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> De scheduler is een ingebouwde functie van Azure Cognitive Search. Er is geen ondersteuning voor externe planners.

## <a name="schedule-property"></a>Eigenschap schema

Een planning maakt deel uit van de definitie van de Indexeer functie. Als de eigenschap **Schedule** wordt wegge laten, wordt de Indexeer functie slechts eenmaal uitgevoerd nadat deze is gemaakt. Als u een **schema** -eigenschap toevoegt, geeft u twee delen op.

| Eigenschap | Beschrijving |
|----------|-------------|
|**Interval** | lang De hoeveelheid tijd tussen het begin van twee opeenvolgende indexerings uitvoeringen. Het kleinste toegestane interval is 5 minuten en de langste is 1440 minuten (24 uur). Deze moet worden ingedeeld als een XSD ' dayTimeDuration-waarde (een beperkte subset van een [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) -waarde). Het patroon hiervoor is: `P(nD)(T(nH)(nM))` . <br/><br/>Voor beelden: elke `PT15M` 2 uur voor elke 15 minuten `PT2H` .|
| **Begin tijd (UTC)** | Beschrijving Hiermee wordt aangegeven wanneer geplande uitvoeringen moeten beginnen. Als u dit weglaat, wordt de huidige UTC-tijd gebruikt. Deze tijd kan in het verleden liggen. in dat geval wordt de eerste uitvoering gepland alsof de Indexeer functie continu actief is sinds de oorspronkelijke **StartTime**.<br/><br/>Voor beelden: `2021-01-01T00:00:00Z` vanaf 1 januari tot en met middernacht, `2021-01-05T22:28:00Z` vanaf 10:28 uur op 5 januari.|

## <a name="scheduling-behavior"></a>Plannings gedrag

Er kan slechts één uitvoering van een Indexeer functie tegelijk worden uitgevoerd. Als er al een Indexeer functie wordt uitgevoerd wanneer de volgende uitvoering wordt gepland, wordt die uitvoering uitgesteld tot het volgende geplande tijdstip.

Laten we eens kijken naar een voor beeld om dit meer concreet te maken. Stel dat we een indexer schema configureren met een **interval** van elk uur en een **begin tijd** van 1 juni 2019 om 8:00:00 uur UTC. Dit kan gebeuren wanneer het uitvoeren van een Indexeer functie langer dan een uur duurt:

* De eerste uitvoering van de Indexeer functie begint op of rond 1 juni 2019 om 8:00 uur UTC. Stel dat deze uitvoering 20 minuten duurt (of een periode van minder dan 1 uur).
* De tweede uitvoering begint op of rond 1 juni 2019 9:00 uur UTC. Stel dat deze uitvoering 70 minuten-meer dan een uur duurt en niet wordt voltooid tot 10:10 uur UTC.
* De derde uitvoering is gepland om te beginnen om 10:00 uur UTC, maar op dat moment wordt de vorige uitvoering nog steeds uitgevoerd. Deze geplande uitvoering wordt vervolgens overgeslagen. De volgende uitvoering van de Indexeer functie wordt pas gestart als 11:00 uur UTC.

> [!NOTE]
> Als een Indexeer functie is ingesteld op een bepaald schema maar herhaaldelijk op hetzelfde document mislukt, wordt de Indexeer functie gestart op een minder frequent interval (Maxi maal het maximum interval van minstens één keer per 24 uur) totdat de voortgang weer wordt uitgevoerd. Als u denkt dat u het onderliggende probleem hebt opgelost, kunt u de Indexeer functie hand matig uitvoeren. als de indexering slaagt, keert de Indexeer functie terug naar de normale planning.

## <a name="schedule-using-rest"></a>Plannen met REST

Geef de **schema** -eigenschap op bij het maken of bijwerken van de Indexeer functie.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Plannen met .NET

In het volgende C#-voor beeld wordt een Azure SQL database Indexeer functie gemaakt met behulp van een vooraf gedefinieerde gegevens bron en-index, en wordt het schema ingesteld om elke dag één keer te worden uitgevoerd, vanaf nu:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Het schema wordt gedefinieerd met behulp van de [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) -klasse, wanneer u een Indexeer functie maakt of bijwerkt met behulp van de [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). De **IndexingSchedule** -constructor vereist een **interval** parameter die is opgegeven met behulp van een **time span** -object. Zoals eerder is vermeld, is de kleinste toegestane waarde voor het interval 5 minuten en de grootste 24 uur. De tweede para meter **StartTime** , opgegeven als **Date Time offset** -object, is optioneel.

## <a name="next-steps"></a>Volgende stappen

Voor Indexeer functies die volgens een planning worden uitgevoerd, kunt u bewerkingen bewaken door de status van de zoek service op te halen of gedetailleerde informatie te verkrijgen door diagnostische logboek registratie in te scha kelen.

* [Status van de zoek Indexeer functie bewaken](search-howto-monitor-indexers.md)
* [Logboek gegevens verzamelen en analyseren](search-monitor-logs.md)