---
title: Aan de slag met SQL-query's in Azure Cosmos DB
description: Meer informatie over het gebruik van SQL-query's voor het opvragen van gegevens uit Azure Cosmos DB. U kunt voorbeeld gegevens uploaden naar een container in Azure Cosmos DB en er query's op uitvoeren.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: tisande
ms.openlocfilehash: c78e15a0656abd510bbc241d39ffd026afddfd96
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353660"
---
# <a name="getting-started-with-sql-queries"></a>Aan de slag met SQL-query's
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB SQL-API-accounts zijn er twee manieren om gegevens te lezen:

**Punt Lees bewerkingen** : u kunt een sleutel/waarde zoeken op één *item-id* en partitie sleutel. De combi natie van *item-id* en partitie sleutel is de sleutel en het item zelf is de waarde. Voor een 1 KB-document wordt bij het lezen van punten doorgaans de [aanvraag eenheid](request-units.md) kosten 1 berekend met een latentie van 10 MS. Punt lezen retourneert één item.

Hier volgen enkele voor beelden van hoe u met elke SDK **punt Lees bewerkingen** kunt uitvoeren:

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?preserve-view=true&view=azure-dotnet)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem?preserve-view=true&view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item?preserve-view=true&view=azure-node-latest#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy?preserve-view=true&view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**SQL-query's** : u kunt gegevens opvragen door query's te schrijven met behulp van de STRUCTURED query language (SQL) als een JSON-query taal. Query's kosten altijd ten minste 2,3 aanvraag eenheden en, in het algemeen, heeft een hogere en meer variabele latentie dan lees punten. Query's kunnen veel items retour neren.

De meeste Lees-en zware werk belastingen op Azure Cosmos DB gebruiken een combi natie van zowel lees-als SQL-query's. Als u slechts één item hoeft te lezen, zijn punt lezen goed koper en sneller dan query's. Punt Lees bewerkingen hoeven de query-engine niet te gebruiken voor toegang tot gegevens en kunnen de gegevens rechtstreeks lezen. Het is natuurlijk niet mogelijk om alle werk belastingen exclusief gegevens te lezen met behulp van punt Lees bewerkingen, zodat SQL als query taal en [indexering](index-overview.md) van het neutraal een flexibele manier is om toegang te krijgen tot uw gegevens.

Hier volgen enkele voor beelden van hoe u **SQL-query's** kunt uitvoeren voor elke SDK:

- [.NET SDK](./sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Java SDK](./sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](./sql-api-nodejs-samples.md#item-examples)
- [Python SDK](./sql-api-python-samples.md#item-examples)

In de rest van dit document ziet u hoe u SQL-query's in Azure Cosmos DB kunt schrijven. SQL-query's kunnen worden uitgevoerd via de SDK of de Azure Portal.

## <a name="upload-sample-data"></a>Voorbeeld gegevens uploaden

Open in uw SQL API-account Cosmos DB het [Data Explorer](./data-explorer.md) om een container te maken `Families` . Nadat de sjabloon is gemaakt, gebruikt u de gegevens structuren browser om deze te zoeken en te openen. In uw `Families` container ziet u de `Items` optie rechts onder de naam van de container. Als u deze optie opent, ziet u een knop in de menu balk in het midden van het scherm om een nieuw item te maken. U gebruikt deze functie om de volgende JSON-items te maken.

### <a name="create-json-items"></a>JSON-items maken

De volgende twee JSON-items zijn documenten over de Wakefield-families. Ze omvatten ouders, kinderen en hun huis dieren, adres en registratie gegevens. 

Het eerste item heeft teken reeksen, getallen, Booleaanse waarden, matrices en geneste eigenschappen:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Het tweede item maakt `givenName` Gebruik `familyName` van en in plaats van `firstName` en `lastName` :

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>De JSON-items opvragen

Probeer enkele query's uit op de JSON-gegevens om inzicht te krijgen in de belangrijkste aspecten van de SQL-query taal van Azure Cosmos DB.

De volgende query retourneert de items waarbij het `id` veld overeenkomt `AndersenFamily` . Omdat het een `SELECT *` query is, is de uitvoer van de query het volledige JSON-item. Zie [instructie SELECT](sql-query-select.md)voor meer informatie over de syntaxis SELECT.

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

De query resultaten zijn:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Met de volgende query wordt de JSON-uitvoer in een andere vorm opgemaakt. De query projecteert een nieuw JSON `Family` -object met twee geselecteerde velden `Name` en `City` , wanneer de adres plaats hetzelfde is als de status. "NY, NY" komt overeen met dit geval.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

De query resultaten zijn:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Met de volgende query worden alle opgegeven namen van onderliggende items geretourneerd in de familie waarvan de `id` treffers `WakefieldFamily` zijn geordend op basis van de plaats.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

U ziet deze uitvoer:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Opmerkingen

In de voor gaande voor beelden worden verschillende aspecten van de Cosmos DB query taal weer gegeven:  

* Aangezien de SQL-API werkt op JSON-waarden, worden er in plaats van rijen en kolommen getreede entiteiten behandeld. U kunt naar de structuur knooppunten op elke wille keurige diepte verwijzen, net als `Node1.Node2.Node3…..Nodem` bij de tweedelige verwijzing van `<table>.<column>` in ANSI SQL.

* Omdat de query taal werkt met schemaloze gegevens, moet het type systeem dynamisch worden gebonden. Een expressie kan verschillende typen voor verschillende elementen opleveren. Het resultaat van een query is een geldige JSON-waarde, maar is niet gegarandeerd een vast schema.  

* Azure Cosmos DB biedt alleen ondersteuning voor JSON-items. Dit betekent dat het typesysteem en expressies alleen geschikt zijn voor JSON-typen. Zie voor meer informatie de [JSON-specificatie](https://www.json.org/).  

* Een Cosmos-container is een schema-gratis verzameling van JSON-items. De relaties binnen en in container items worden impliciet vastgelegd door containment, niet op basis van primaire sleutel en refererende-sleutel relaties. Deze functie is belang rijk voor de objecten die verderop in dit artikel worden besproken.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-component](sql-query-select.md)