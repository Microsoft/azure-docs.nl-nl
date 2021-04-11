---
title: De kosten voor aanvraag eenheden voor de Azure Cosmos DB-API voor MongoDB-bewerkingen zoeken
description: Meer informatie over het vinden van de koppelings kosten van de aanvraag eenheid voor MongoDB query's die worden uitgevoerd op een Azure Cosmos-container. U kunt de Azure Portal, MongoDB .NET, Java Node.js-Stuur Programma's gebruiken.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b2944c1d29849ea44b5afd878d5b0e030358cc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801822"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>De kosten voor de aanvraag eenheid zoeken voor bewerkingen die zijn uitgevoerd in Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking.

De kosten van alle databasebewerkingen worden genormaliseerd door Azure Cosmos DB en uitgedrukt in aanvraageenheden (kortweg RU's). Kosten voor aanvragen zijn de aanvraag eenheden die worden verbruikt door alle database bewerkingen. U kunt RUs beschouwen als een prestatie valuta die de systeem bronnen aanabstractt, zoals CPU, IOPS en geheugen die nodig zijn om de database bewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de database bewerking een schrijven, een lees punt of een query is, worden de kosten altijd gemeten in RUs. Zie voor meer informatie het artikel [aanvraag eenheden en overwegingen](request-units.md) .

In dit artikel worden de verschillende manieren beschreven waarop u het gebruik van de [aanvraag eenheid](request-units.md) (ru) kunt vinden voor elke bewerking die wordt uitgevoerd op basis van een container in azure Cosmos DB-API voor MongoDb. Als u een andere API gebruikt, raadpleegt u [SQL API](find-request-unit-charge.md), [CASSANDRA-API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)en [Table-API](find-request-unit-charge-table.md) artikelen om de kosten voor de ru/s te vinden.

De RU-kosten worden weer gegeven met een aangepaste [database opdracht](https://docs.mongodb.com/manual/reference/command/) met de naam `getLastRequestStatistics` . Met de opdracht wordt een document geretourneerd dat de naam bevat van de laatste bewerking die is uitgevoerd, de aanvraag kosten en de duur. Als u de Azure Cosmos DB-API voor MongoDB gebruikt, hebt u meerdere opties om de RU-kosten op te halen.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account) en voer het in met gegevens of selecteer een bestaand account dat al gegevens bevat.

1. Ga naar het deel venster **Data Explorer** en selecteer vervolgens de container waaraan u wilt werken.

1. Selecteer de **..** . naast de naam van de container en selecteer **nieuwe query**.

1. Voer een geldige query in en selecteer **query uitvoeren**.

1. Selecteer **query statistieken** om de werkelijke aanvraag kosten weer te geven voor de aanvraag die u hebt uitgevoerd. Met deze query-editor kunt u kosten voor aanvraag eenheden voor alleen query predikaten uitvoeren en weer geven. U kunt deze editor niet gebruiken voor het bewerken van gegevens, zoals INSERT-instructies.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Scherm opname van een aanvraag voor een MongoDB-query in de Azure Portal":::

1. Als u de aanvraag kosten voor het uitvoeren van gegevens wilt ophalen, voert u de `getLastRequestStatistics` opdracht uit vanaf een shell-gebaseerde gebruikers interface, zoals Mongo shell, [Robo 3T gebruiken](mongodb-robomongo.md), [MONGODB kompas](mongodb-compass.md)of een VS code-extensie met shell scripting.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Het MongoDB .NET-stuur programma gebruiken

Wanneer u het [officiële MongoDb .net-stuur programma](https://docs.mongodb.com/ecosystem/drivers/csharp/)gebruikt, kunt u opdrachten uitvoeren door de `RunCommand` methode aan te roepen voor een `IMongoDatabase` object. Voor deze methode is een implementatie van de `Command<>` abstracte klasse vereist:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Zie [Quick Start: een .net-Web-app bouwen met een Azure Cosmos DB-API voor MongoDb](create-mongodb-dotnet.md)voor meer informatie.

## <a name="use-the-mongodb-java-driver"></a>Het MongoDB Java-stuur programma gebruiken


Wanneer u het [officiële MongoDb Java-stuur programma](https://mongodb.github.io/mongo-java-driver/)gebruikt, kunt u opdrachten uitvoeren door de `runCommand` methode aan te roepen voor een `MongoDatabase` object:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Zie [Quick Start: een web-app bouwen met behulp van de Azure Cosmos DB-API voor MongoDb en de Java-SDK](create-mongodb-java.md)voor meer informatie.

## <a name="use-the-mongodb-nodejs-driver"></a>Het MongoDB-stuur programma voor Node.js gebruiken

Wanneer u het [officiële MongoDB Node.js-stuur programma](https://mongodb.github.io/node-mongodb-native/)gebruikt, kunt u opdrachten uitvoeren door de `command` methode aan te roepen voor een `db` object:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Zie [Quick Start: een bestaande MongoDB Node.js web-app migreren naar Azure Cosmos DB](create-mongodb-nodejs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](./optimize-cost-reads-writes.md)