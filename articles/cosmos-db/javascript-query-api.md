---
title: Werken met geïntegreerde Java script-query-API in Azure Cosmos DB opgeslagen procedures en triggers
description: In dit artikel worden de concepten geïntroduceerd voor de in Java script geïntegreerde query-API voor het maken van opgeslagen procedures en triggers in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340971"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript-query-API in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Naast het uitgeven van query's met behulp van de SQL-API in Azure Cosmos DB, biedt de [Cosmos DB SDK aan de server zijde](https://github.com/Azure/azure-cosmosdb-js-server/) een Java script-interface voor het uitvoeren van geoptimaliseerde query's in Cosmos DB opgeslagen procedures en triggers. U hoeft niet op de hoogte te zijn van de SQL-taal om deze Java script-interface te gebruiken. Met de Java script-query-API kunt u programmatisch query's maken door predikaten te gebruiken in volg orde van functie aanroepen, met een syntaxis die bekend is met de ECMAScript5's array-ingebouwde en populaire Java script-bibliotheken zoals Lodash. Query's worden door de Java Script-runtime geparseerd en efficiënt uitgevoerd met Azure Cosmos DB indices.

## <a name="supported-javascript-functions"></a>Ondersteunde Java script-functies

| **Functie** | **Beschrijving** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Start een geketende aanroep die moet worden beëindigd met de waarde ().|
|`filter(predicateFunction [, options] [, callback])`|Hiermee wordt de invoer gefilterd met behulp van een predicaat functie die waar/onwaar retourneert om invoer documenten te filteren in/uit te voeren in de resulterende set. Deze functie gedraagt zich op dezelfde manier als een WHERE-component in SQL.|
|`flatten([isShallow] [, options] [, callback])`|Hiermee worden matrices van elk invoer item gecombineerd en afgevlakt tot één matrix. Deze functie gedraagt zich op dezelfde manier als SelectMany in LINQ.|
|`map(transformationFunction [, options] [, callback])`|Past een projectie toe op basis van een transformatie functie waarmee elk invoer item wordt toegewezen aan een Java Script-object of-waarde. Deze functie gedraagt zich op dezelfde manier als een SELECT-component in SQL.|
|`pluck([propertyName] [, options] [, callback])`|Deze functie is een snelkoppeling voor een kaart die de waarde van één eigenschap uit elk invoer item ophaalt.|
|`sortBy([predicate] [, options] [, callback])`|Produceert een nieuwe set documenten door de documenten in de invoer document stroom in oplopende volg orde te sorteren met behulp van het opgegeven predicaat. Deze functie gedraagt zich op dezelfde manier als een ORDER BY-component in SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produceert een nieuwe set documenten door de documenten in de invoer document stroom in aflopende volg orde te sorteren met het opgegeven predicaat. Deze functie gedraagt zich op dezelfde manier als een component ORDER BY x in SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Voert een self-join uit met de binnenste matrix en voegt resultaten van beide zijden toe als Tuples voor de projectie van het resultaat. U kunt bijvoorbeeld een persoons document samen voegen met persoon. huis dieren zouden Tuples van [person, huisdier] opleveren. Dit is vergelijkbaar met SelectMany in .NET LINK.|

Wanneer ze zijn opgenomen in predikaten en/of selector-functies, worden de volgende Java script-constructies automatisch geoptimaliseerd om rechtstreeks op Azure Cosmos DB indices te worden uitgevoerd:

- Eenvoudige Opera tors `=` `+` `-` `*` `/` : `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Letterlijke waarden, inclusief de object letterlijke waarde: {}
- var, retour

De volgende Java script-constructies worden niet geoptimaliseerd voor Azure Cosmos DB indices:

- Controle stroom (bijvoorbeeld als, voor, while)
- Functie aanroepen

Zie de [Cosmos DB server side java script-documentatie](https://github.com/Azure/azure-cosmosdb-js-server/)voor meer informatie.

## <a name="sql-to-javascript-cheat-sheet"></a>Cheat-blad van SQL naar Java script

De volgende tabel bevat verschillende SQL-query's en de bijbehorende java script-query's. Net als bij SQL-query's zijn eigenschappen (bijvoorbeeld item.id) hoofdletter gevoelig.

> [!NOTE]
> `__` (dubbele onderstreping) is een alias naar `getContext().getCollection()` bij gebruik van de JavaScript-query-API.

|**SQL**|**Java script-query-API**|**Beschrijving**|
|---|---|---|
|UITGESCHAKELD<br>VAN documenten| __. map (function (doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;retour document;<br>});|De resultaten zijn in alle documenten (gepagineerd met vervolg token).|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. bericht als msg,<br>&nbsp;&nbsp;&nbsp;docs. acties <br>VAN documenten|__. map (function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;opvragen<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bericht: doc. bericht,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;acties: doc. acties<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projecteert de id, het bericht (met een alias als een bericht) en actie van alle documenten.|
|UITGESCHAKELD<br>VAN documenten<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. id = "X998_Y998"|__. filter (functie (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;retour doc.id = = = "X998_Y998";<br>});|Query's voor documenten met het predicaat: ID = "X998_Y998".|
|UITGESCHAKELD<br>VAN documenten<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Tags, 123)|__. filter (functie (x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;x. Tags retour neren && x. Tags. indexOf (123) >-1;<br>});|Query's voor documenten met de eigenschap Tags en tags zijn een matrix met de waarde 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. bericht als msg<br>VAN documenten<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. id = "X998_Y998"|__. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. filter (functie (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retour doc.id = = = "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. map (functie (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;opvragen<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bericht: document<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>. Value ();|Query's voor documenten met een predikaat, ID = "X998_Y998" en vervolgens projecteert u de id en het bericht (als alias voor msg).|
|WAARDEcode selecteren<br>VAN documenten<br>Voeg tag toe IN docs. Koptags<br>Sorteren op docs._ts|__. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. filter (functie (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retour document. Tags && matrix. isArray (doc. Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. sortBy (functie (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retour doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. Pluck ("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;. afvlakking ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Value ()|Filters voor documenten met een matrix eigenschap, tags en sorteren de resulterende documenten door de _ts tijds tempel systeem eigenschap. vervolgens worden projecten en de labels matrix afgevlakt.|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven en gebruiken van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB:

- [Opgeslagen procedures en triggers schrijven met behulp van Java script-query-API](how-to-write-javascript-query-api.md)
- [Werken met Azure Cosmos DB opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)
- [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies gebruiken in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Naslag informatie voor de API-server zijde Azure Cosmos DB java script](https://azure.github.io/azure-cosmosdb-js-server)
- [Java script-ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
