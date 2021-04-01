---
title: Door de gebruiker gedefinieerde functies (Udf's) in Azure Cosmos DB
description: Meer informatie over door de gebruiker gedefinieerde functies in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546332"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Door de gebruiker gedefinieerde functies (Udf's) in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De SQL-API biedt ondersteuning voor door de gebruiker gedefinieerde functies (Udf's). Met scalaire Udf's kunt u nul of veel argumenten door geven en een enkel argument resultaat retour neren. Met de API wordt elk argument gecontroleerd op geldige JSON-waarden.  

## <a name="udf-use-cases"></a>UDF-use cases

De API breidt de SQL-syntaxis uit ter ondersteuning van aangepaste toepassings logica met behulp van Udf's. U kunt Udf's met de SQL-API registreren en ernaar verwijzen in SQL-query's. In tegens telling tot opgeslagen procedures en triggers zijn Udf's alleen-lezen.

Met behulp van Udf's kunt u de query taal van Azure Cosmos DB uitbreiden. Udf's zijn een uitstekende manier om complexe bedrijfs logica in de projectie van een query uit te drukken.

We raden u echter aan om Udf's te vermijden wanneer:

- Er bestaat al een gelijkwaardige [systeem functie](sql-query-system-functions.md) in azure Cosmos db. Systeem functies gebruiken altijd minder RU dan de equivalente UDF.
- De UDF is het enige filter in de `WHERE` component van uw query. UDF maakt geen gebruik van de index, dus voor het evalueren van de UDF moeten documenten worden geladen. Het combi neren van extra filter predikaten die gebruikmaken van de index, in combi natie met een UDF in de `WHERE` component, vermindert het aantal documenten dat door de UDF wordt verwerkt.

Als u dezelfde UDF meerdere keren in een query moet gebruiken, moet u verwijzen naar de UDF in een [subquery](sql-query-subquery.md#evaluate-once-and-reference-many-times), zodat u een joinexpressie kunt gebruiken om de UDF één keer te evalueren, maar ernaar te verwijzen.

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld wordt een UDF onder een item container in de Cosmos-data base geregistreerd. In het voor beeld wordt een UDF gemaakt waarvan de naam is `REGEX_MATCH` . Er worden twee JSON-teken reeks waarden geaccepteerd `input` en `pattern` gecontroleerd of de eerste overeenkomt met het patroon dat is opgegeven in de tweede met behulp van Java script- `string.match()` functie.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Gebruik deze UDF nu in een query-projectie. U moet Udf's kwalificeren met het hoofdletter gevoelige voor voegsel `udf.` Wanneer u ze aanroept vanuit query's.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

U ziet deze uitvoer:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

U kunt de UDF gekwalificeerd met het `udf.` voor voegsel binnen een filter gebruiken, zoals in het volgende voor beeld:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

U ziet deze uitvoer:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

In essentie zijn Udf's geldige scalaire expressies die u in zowel projecties als filters kunt gebruiken.

Als u de kracht van Udf's wilt uitbreiden, bekijkt u een ander voor beeld met voorwaardelijke logica:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

In het volgende voor beeld wordt de UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

U ziet deze uitvoer:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Als de eigenschappen waarnaar wordt verwezen door de UDF-para meters niet beschikbaar zijn in de JSON-waarde, wordt de para meter beschouwd als niet-gedefinieerd en wordt de UDF-aanroep overgeslagen. Als het resultaat van de UDF niet is gedefinieerd, wordt het niet opgenomen in het resultaat.

Zoals in de voor gaande voor beelden wordt weer gegeven, integreert UDFs de kracht van Java script-taal met de SQL-API. Udf's bieden een uitgebreide programmeer bare interface voor het uitvoeren van complexe procedures, voorwaardelijke logica, met behulp van ingebouwde Java Script runtime-functies. De SQL-API biedt de argumenten voor de Udf's voor elk bron item bij de huidige WHERE-of SELECT-component fase van de verwerking. Het resultaat is naadloos opgenomen in de algehele uitvoerings pijplijn. In samen vatting zijn Udf's fantastische hulp middelen voor het uitvoeren van complexe bedrijfs logica als onderdeel van query's.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Systeemfuncties](sql-query-system-functions.md)
- [Aggregaties](sql-query-aggregate-functions.md)