---
title: Werken met datums in Azure Cosmos DB
description: Meer informatie over het opslaan, indexeren en doorzoeken van datum/tijd-objecten in Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4d4e63bdd1e795bc1c599d0eae8a595aa0d643
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359520"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Werken met datums in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB biedt flexibiliteit en uitgebreide indexeringen voor het schema via een systeem eigen [JSON](https://www.json.org) -gegevens model. Alle Azure Cosmos DB resources, inclusief data bases, containers, documenten en opgeslagen procedures, worden gemodelleerd en opgeslagen als JSON-documenten. Als vereiste voor een draag bare, JSON-(en Azure Cosmos DB) ondersteunt slechts een kleine set basis typen: teken reeks, getal, Booleaanse waarde, matrix, object en null. JSON is echter flexibel en stelt ontwikkel aars en frameworks in staat om complexere typen te maken met behulp van deze primitieven en ze als objecten of matrices samen te stellen.

Naast de basis typen hebben veel toepassingen het type datum/tijd nodig om datums en tijds tempels weer te geven. In dit artikel wordt beschreven hoe ontwikkel aars datums kunnen opslaan, ophalen en opvragen in Azure Cosmos DB met behulp van de .NET SDK.

## <a name="storing-datetimes"></a>Datum-en tijd opslag

Azure Cosmos DB ondersteunt JSON-typen zoals-String, Number, Boolean, null, array en object. Het biedt geen rechtstreekse ondersteuning voor het type datum/tijd. Op dit moment biedt Azure Cosmos DB geen ondersteuning voor lokalisatie van datums. U moet dus DateTimes opslaan als teken reeksen. De aanbevolen notatie voor datum-en tijd teken reeksen in Azure Cosmos DB is `yyyy-MM-ddTHH:mm:ss.fffffffZ` die de ISO 8601 UTC-standaard volgt. Het is raadzaam om alle datums in Azure Cosmos DB op te slaan als UTC. Als de datum teken reeksen naar deze indeling worden geconverteerd, kunnen de sorteer datums lexicographically worden gewijzigd. Als er niet-UTC-datums worden opgeslagen, moet de logica aan de client zijde worden afgehandeld. Voor het converteren van een lokale datum-/tijdwaarde naar UTC moet de offset bekend/opgeslagen zijn als een eigenschap in de JSON en de client kan de offset gebruiken om de waarde van de UTC-datum/tijd te berekenen.

Bereik query's met DateTime-teken reeksen als filters worden alleen ondersteund als de DateTime-teken reeksen allemaal in UTC en dezelfde lengte zijn. In Azure Cosmos DB retourneert de [GetCurrentDateTime](sql-query-getcurrentdatetime.md) -systeem functie de huidige UTC-datum en-8601 tijd in de notatie: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

De meeste toepassingen kunnen de standaard teken reeks representatie voor DateTime gebruiken om de volgende redenen:

* Teken reeksen kunnen worden vergeleken en de relatieve volg orde van de datum/tijd-waarden blijft behouden wanneer ze worden omgezet naar teken reeksen.
* Voor deze benadering zijn geen aangepaste code of kenmerken voor JSON-conversie vereist.
* De datums zoals opgeslagen in JSON zijn leesbaar voor iedereen.
* Deze aanpak kan profiteren van de index van Azure Cosmos DB voor snelle query prestaties.

Het volgende code fragment bevat bijvoorbeeld een `Order` object met twee datetime-eigenschappen, `ShipDate` en `OrderDate` als een document met behulp van de .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Dit document wordt als volgt opgeslagen in Azure Cosmos DB:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

U kunt ook DateTimes opslaan als UNIX-tijds tempels, dat wil zeggen, als een getal dat het aantal verstreken seconden vertegenwoordigt sinds 1 januari 1970. De eigenschap van de interne Time Stamp () van Azure Cosmos DB `_ts` volgt deze methode. U kunt de [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) -klasse gebruiken om datetimes als getallen te serialiseren.

## <a name="querying-datetimes-in-linq"></a>Query's uitvoeren op DateTimes in LINQ

De SQL .NET SDK ondersteunt automatisch het opvragen van gegevens die zijn opgeslagen in Azure Cosmos DB via LINQ. Het volgende code fragment toont bijvoorbeeld een LINQ-query waarmee orders worden gefilterd die in de afgelopen drie dagen zijn verzonden:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Vertaald naar de volgende SQL-instructie en uitgevoerd op Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Meer informatie over de SQL-query taal van Azure Cosmos DB en de LINQ-provider vindt u bij het [opvragen van Cosmos db in LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Datum/tijd indexeren voor bereik query's

Query's zijn gebruikelijk met datum/tijd-waarden. Als u deze query's efficiënt wilt uitvoeren, moet u een index hebben gedefinieerd voor alle eigenschappen in het filter van de query.

Meer informatie over het configureren van het indexerings beleid in [Azure Cosmos DB indexerings beleid](index-policy.md). 

## <a name="next-steps"></a>Volgende stappen

* De code voorbeelden downloaden en uitvoeren [op github](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples)
* Meer informatie over [SQL-query's](sql-query-getting-started.md)
* Meer informatie over [Azure Cosmos DB indexerings beleid](index-policy.md)