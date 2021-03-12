---
title: 'De API van Azure Cosmos DB voor MongoDB (versie 3.6): ondersteunde functies en syntaxis'
description: 'Meer informatie over de API van Azure Cosmos DB voor MongoDB (versie 3.6): ondersteunde functies en syntaxis.'
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 0b6f85a675dc98928309870ea177629203db39e7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557332"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>De API van Azure Cosmos DB voor MongoDB (versie 3.6): ondersteunde functies en syntaxis
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt met de API van Azure Cosmos DB voor MongoDB communiceren via een van de open-source MongoDB-[clientstuurprogramma's](https://docs.mongodb.org/ecosystem/drivers). De API van Azure Cosmos DB voor MongoDB maakt het gebruik van bestaande clientstuurprogramma's mogelijk doordat de API functioneert conform het MongoDB-[wireprotocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Door gebruik te maken van de API van Azure Cosmos DB voor MongoDB hebt u de beschikking over de voordelen van de vertrouwde MongoDB, met alle zakelijke mogelijkheden die Cosmos DB biedt: [wereldwijde distributie](distribute-data-globally.md), [automatische sharding](partitioning-overview.md), garanties voor beschikbaarheid en latentie, versleuteling van niet-actieve gegevens, het maken van back-ups en nog veel meer.

> [!NOTE]
> Versie 3,6 van de Cosmos DB-API voor MongoDB heeft geen huidige plannen voor end-of-Life (EOL). De minimale kennisgeving voor een toekomstige EOL is drie jaar.

## <a name="protocol-support"></a>Ondersteuning voor protocol

De API van Azure Cosmos DB voor MongoDB is standaard compatibel met MongoDB-serverversie **3.6** voor nieuwe accounts. De ondersteunde operators en eventuele beperkingen of uitzonderingen worden hieronder vermeld. Elk clientstuurprogramma dat deze protocollen kent, kan verbinding maken met de API van Azure Cosmos DB voor MongoDB. Wanneer u de API van Azure Cosmos DB voor MongoDB-accounts gebruikt, heeft de 3.6-versie van het account het eindpunt in de indeling `*.mongo.cosmos.azure.com`, terwijl de 3.2-versie van het account de indeling `*.documents.azure.com` heeft.

## <a name="query-language-support"></a>Ondersteuning voor querytaal

De API van Azure Cosmos DB voor MongoDB biedt uitgebreide ondersteuning voor MongoDB-querytaalconstructs. In de volgende secties ziet u de gedetailleerde lijst met serverbewerkingen, operators, fasen, opdrachten en opties die momenteel door Azure Cosmos DB worden ondersteund.

> [!NOTE]
> Dit artikel bevat alleen de ondersteunde serveropdrachten en sluit wrapper-functies aan de clientzijde uit. Wrapper-functies aan de clientzijde, zoals `deleteMany()` en `updateMany()`, maken intern gebruik van de serveropdrachten `delete()` en `update()`. Functies die gebruikmaken van ondersteunde serveropdrachten zijn compatibel met de API van Azure Cosmos DB voor MongoDB.

## <a name="database-commands"></a>Databaseopdrachten

De API van Azure Cosmos DB voor MongoDB biedt ondersteuning voor de volgende databaseopdrachten:

### <a name="query-and-write-operation-commands"></a>Opdrachten voor query- en schrijfbewerkingen

| Opdracht | Ondersteund |
|---------|---------|
| [stroom wijzigen](mongodb-change-streams.md) | Ja |
| delete | Ja |
| eval | Nee |
| find | Ja |
| findAndModify | Ja |
| getLastError | Ja |
| getMore | Ja |
| getPrevError | Nee |
| insert | Ja |
| parallelCollectionScan | Nee |
| resetError | Nee |
| update | Ja |

### <a name="authentication-commands"></a>Verificatieopdrachten

| Opdracht | Ondersteund |
|---------|---------|
| authenticate | Ja |
| getnonce | Ja |
| logout | Ja |

### <a name="administration-commands"></a>Beheeropdrachten

| Opdracht | Ondersteund |
|---------|---------|
| cloneCollectionAsCapped | Nee |
| collMod | Nee |
| connectionStatus | Nee |
| convertToCapped | Nee |
| copydb | Nee |
| maken | Ja |
| createIndexes | Ja |
| currentOp | Ja |
| drop | Ja |
| dropDatabase | Ja |
| dropIndexes | Ja |
| filemd5 | Ja |
| killCursors | Ja |
| killOp | Nee |
| listCollections | Ja |
| listDatabases | Ja |
| listIndexes | Ja |
| reIndex | Ja |
| renameCollection | Nee |


### <a name="diagnostics-commands"></a>Diagnostics commands

| Opdracht | Ondersteund |
|---------|---------|
| buildInfo | Ja |
| collStats | Ja |
| connPoolStats | Nee |
| connectionStatus | Nee |
| dataSize | Nee |
| dbHash | Nee |
| dbStats | Ja |
| explain | Ja |
| features | Nee |
| hostInfo | Ja |
| listDatabases | Ja |
| listCommands | Nee |
| profiler | Nee |
| serverStatus | Nee |
| top | Nee |
| whatsmyuri | Ja |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Samenvoegingspijplijn</a>

### <a name="aggregation-commands"></a>Samenvoegingsopdrachten

| Opdracht | Ondersteund |
|---------|---------|
| aggregate | Ja |
| count | Ja |
| distinct | Ja |
| mapReduce | Nee |

### <a name="aggregation-stages"></a>Samenvoegingsfasen

| Opdracht | Ondersteund |
|---------|---------|
| $addFields | Ja |
| $bucket | Nee |
| $bucketAuto | Nee |
| $changeStream | Ja |
| $collStats | Nee |
| $count | Ja |
| $currentOp | Nee |
| $facet | Ja |
| $geoNear | Ja |
| $graphLookup | Ja |
| $group | Ja |
| $indexStats | Nee |
| $limit | Ja |
| $listLocalSessions | Nee |
| $listSessions | Nee |
| $lookup | Gedeeltelijk |
| $match | Ja |
| $out | Ja |
| $project | Ja |
| $redact | Ja |
| $replaceRoot | Ja |
| $replaceWith | Nee |
| $sample | Ja |
| $skip | Ja |
| $sort | Ja |
| $sortByCount | Ja |
| $unwind | Ja |

> [!NOTE]
> `$lookup` biedt nog geen ondersteuning voor de functie niet- [gecorreleerde subquerys](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#join-conditions-and-uncorrelated-sub-queries) die zijn geïntroduceerd in server versie 3,6. U ontvangt een fout melding met een bericht dat wordt weer gegeven `let is not supported` Als u probeert de `$lookup` operator met `let` en te gebruiken `pipeline` .

### <a name="boolean-expressions"></a>Booleaanse expressies

| Opdracht | Ondersteund |
|---------|---------|
| $and | Ja |
| $not | Ja |
| $or | Ja |

### <a name="set-expressions"></a>Expressies voor instellen

| Opdracht | Ondersteund |
|---------|---------|
| $setEquals | Ja |
| $setIntersection | Ja |
| $setUnion | Ja |
| $setDifference | Ja |
| $setIsSubset | Ja |
| $anyElementTrue | Ja |
| $allElementsTrue | Ja |

### <a name="comparison-expressions"></a>Expressies voor vergelijken

| Opdracht | Ondersteund |
|---------|---------|
| $cmp | Ja |
| $eq | Ja | 
| $gt | Ja | 
| $gte | Ja | 
| $lt | Ja |
| $lte | Ja | 
| $ne | Ja | 
| $in | Ja | 
| $nin | Ja | 

### <a name="arithmetic-expressions"></a>Rekenkundige expressies

| Opdracht | Ondersteund |
|---------|---------|
| $abs | Ja |
| $add | Ja |
| $ceil | Ja |
| $divide | Ja |
| $exp | Ja |
| $floor | Ja |
| $ln | Ja |
| $log | Ja |
| $log10 | Ja |
| $mod | Ja |
| $multiply | Ja |
| $pow | Ja |
| $sqrt | Ja |
| $subtract | Ja |
| $trunc | Ja |

### <a name="string-expressions"></a>Tekenreeksexpressies

| Opdracht | Ondersteund |
|---------|---------|
| $concat | Ja |
| $indexOfBytes | Ja |
| $indexOfCP | Ja |
| $split | Ja |
| $strLenBytes | Ja |
| $strLenCP | Ja |
| $strcasecmp | Ja |
| $substr | Ja |
| $substrBytes | Ja |
| $substrCP | Ja |
| $toLower | Ja |
| $toUpper | Ja |

### <a name="text-search-operator"></a>Operator voor tekst zoeken

| Opdracht | Ondersteund |
|---------|---------|
| $meta | Nee |

### <a name="array-expressions"></a>Matrixexpressies

| Opdracht | Ondersteund |
|---------|---------|
| $arrayElemAt | Ja |
| $arrayToObject | Ja |
| $concatArrays | Ja |
| $filter | Ja |
| $indexOfArray | Ja |
| $isArray | Ja |
| $objectToArray | Ja |
| $range | Ja |
| $reverseArray | Ja |
| $reduce | Ja |
| $size | Ja |
| $slice | Ja |
| $zip | Ja |
| $in | Ja |

### <a name="variable-operators"></a>Operators voor variabelen

| Opdracht | Ondersteund |
|---------|---------|
| $map | Ja |
| $let | Ja |

### <a name="system-variables"></a>Systeemvariabelen

| Opdracht | Ondersteund |
|---------|---------|
| $$CURRENT | Ja |
| $$DESCEND | Ja |
| $$KEEP | Ja |
| $$PRUNE | Ja |
| $$REMOVE | Ja |
| $$ROOT | Ja |

### <a name="literal-operator"></a>Letterlijke operator

| Opdracht | Ondersteund |
|---------|---------|
| $literal | Ja |

### <a name="date-expressions"></a>Datumexpressies

| Opdracht | Ondersteund |
|---------|---------|
| $dayOfYear | Ja |
| $dayOfMonth | Ja |
| $dayOfWeek | Ja |
| $year | Ja |
| $month | Ja | 
| $week | Ja |
| $hour | Ja |
| $minute | Ja | 
| $second | Ja |
| $millisecond | Ja | 
| $dateToString | Ja |
| $isoDayOfWeek | Ja |
| $isoWeek | Ja |
| $dateFromParts | Nee | 
| $dateToParts | Nee |
| $dateFromString | Nee |
| $isoWeekYear | Ja |

### <a name="conditional-expressions"></a>Voorwaardelijke expressies

| Opdracht | Ondersteund |
|---------|---------|
| $cond | Ja |
| $ifNull | Ja |
| $switch | Ja |

### <a name="data-type-operator"></a>Operator voor gegevenstype

| Opdracht | Ondersteund |
|---------|---------|
| $type | Ja |

### <a name="accumulator-expressions"></a>Accumulatorexpressies

| Opdracht | Ondersteund |
|---------|---------|
| $sum | Ja |
| $avg | Ja |
| $first | Ja |
| $last | Ja |
| $max | Ja |
| $min | Ja |
| $push | Ja |
| $addToSet | Ja |
| $stdDevPop | Ja |
| $stdDevSamp | Ja |

### <a name="merge-operator"></a>Operator voor samenvoegen

| Opdracht | Ondersteund |
|---------|---------|
| $mergeObjects | Ja |

## <a name="data-types"></a>Gegevenstypen

| Opdracht | Ondersteund |
|---------|---------|
| Dubbel | Ja |
| Tekenreeks | Ja |
| Object | Ja |
| Matrix | Ja |
| Binaire gegevens | Ja | 
| ObjectId | Ja |
| Booleaans | Ja |
| Date | Ja |
| Null | Ja |
| 32-bits geheel getal (int) | Ja |
| Tijdstempel | Ja |
| 64-bits geheel getal (lang) | Ja |
| MinKey | Ja |
| MaxKey | Ja |
| Decimal128 | Ja | 
| Reguliere expressie | Ja |
| Javascript | Ja |
| JavaScript (met bereik)| Ja |
| Undefined | Ja |

## <a name="indexes-and-index-properties"></a>Indexen en indexeigenschappen

### <a name="indexes"></a>Indexen

| Opdracht | Ondersteund |
|---------|---------|
| Index met één veld | Ja |
| Samengestelde index | Ja |
| Index met meerdere sleutels | Ja |
| Tekstindex | Nee |
| 2dsphere | Ja |
| 2D-index | Nee |
| Gehashte index | Ja |

### <a name="index-properties"></a>Indexeigenschappen

| Opdracht | Ondersteund |
|---------|---------|
| TTL | Ja |
| Uniek | Ja |
| Gedeeltelijk | Nee |
| Niet-hoofdlettergevoelig | Nee |
| Sparse | Nee |
| Achtergrond | Ja |

## <a name="operators"></a>Operators

### <a name="logical-operators"></a>Logische operators

| Opdracht | Ondersteund |
|---------|---------|
| $or | Ja |
| $and | Ja |
| $not | Ja |
| $nor | Ja | 

### <a name="element-operators"></a>Operators voor elementen

| Opdracht | Ondersteund |
|---------|---------|
| $exists | Ja |
| $type | Ja |

### <a name="evaluation-query-operators"></a>Operators voor evaluatiequery's

| Opdracht | Ondersteund |
|---------|---------|
| $expr | Nee |
| $jsonSchema | Nee |
| $mod | Ja |
| $regex | Ja |
| $text | Nee (niet ondersteund. Gebruik in plaats hiervan $regex.)| 
| $where | Nee | 

In de $regex-query's is zoeken in de index toegestaan op basis van links-verankerde expressies. Als u echter de aanpassingsfuncties i (geen hoofdlettergevoeligheid) en m (meerdere regels) gebruikt, wordt de verzameling gescand in alle expressies.

Wanneer $ of | moet worden opgenomen, kunt u het beste twee (of meer) regex-query’s maken. Bijvoorbeeld, bij de volgende oorspronkelijke query: ```find({x:{$regex: /^abc$/})```, moet dit als volgt worden gewijzigd:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```

Het eerste deel maakt gebruik van de index om alleen te zoeken naar de documenten die beginnen met ^abc en het tweede deel zoekt naar een overeenkomst tussen de exacte vermeldingen. De streepoperator | fungeert als de functie: or. De query ```find({x:{$regex: /^abc |^def/})``` komt overeen met de documenten waarin het veld x waarden heeft die beginnen met abc of def. Als u de index wilt gebruiken, kunt u de query het beste opsplitsen in twee verschillende query’s die zijn verbonden met de operator $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operators voor matrices

| Opdracht | Ondersteund | 
|---------|---------|
| $all | Ja | 
| $elemMatch | Ja | 
| $size | Ja | 

### <a name="comment-operator"></a>Operator voor opmerkingen

| Opdracht | Ondersteund | 
|---------|---------|
| $comment | Ja | 

### <a name="projection-operators"></a>Operators voor projecties

| Opdracht | Ondersteund |
|---------|---------|
| $elemMatch | Ja |
| $meta | Nee |
| $slice | Ja |

### <a name="update-operators"></a>Operators voor updates

#### <a name="field-update-operators"></a>Operators voor veldupdates

| Opdracht | Ondersteund |
|---------|---------|
| $inc | Ja |
| $mul | Ja |
| $rename | Ja |
| $setOnInsert | Ja |
| $set | Ja |
| $unset | Ja |
| $min | Ja |
| $max | Ja |
| $currentDate | Ja |

#### <a name="array-update-operators"></a>Operators voor matrixupdates

| Opdracht | Ondersteund |
|---------|---------|
| $ | Ja |
| $[]| Ja |
| $[<identifier>]| Ja |
| $addToSet | Ja |
| $pop | Ja |
| $pullAll | Ja |
| $pull | Ja |
| $push | Ja |
| $pushAll | Ja |


#### <a name="update-modifiers"></a>Aanpassingsfuncties voor bijwerken

| Opdracht | Ondersteund |
|---------|---------|
| $each | Ja |
| $slice | Ja |
| $sort | Ja |
| $position | Ja |

#### <a name="bitwise-update-operator"></a>Operators voor bitwise-updates

| Opdracht | Ondersteund |
|---------|---------|
| $bit | Ja | 
| $bitsAllSet | Nee |
| $bitsAnySet | Nee |
| $bitsAllClear | Nee |
| $bitsAnyClear | Nee |

### <a name="geospatial-operators"></a>Georuimtelijke operators

Operator | Ondersteund | 
--- | --- |
$geoWithin | Ja |
$geoIntersects | Ja | 
$near | Ja |
$nearSphere | Ja |
$geometry | Ja |
$minDistance | Ja |
$maxDistance | Ja |
$center | No |
$centerSphere | No |
$box | No |
$polygon | No |

## <a name="sort-operations"></a>Bewerkingen sorteren

Tijdens het gebruik van bewerking `findOneAndUpdate`, worden sorteerbewerkingen op één veld ondersteund, maar sorteerbewerkingen op meerdere velden worden niet ondersteund.

## <a name="unique-indexes"></a>Unieke indexen

[Unieke indexen](mongodb-indexing.md#unique-indexes) zorgen ervoor dat een specifiek veld geen dubbele waarden bevat in alle documenten van een verzameling, net zoals dat de uniekheid van de standaard-id-sleutel behouden blijft. U kunt unieke indexen in Cosmos DB maken door de opdracht `createIndex` te gebruiken met de beperkingsparameter `unique`:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Samengestelde indexen

[Samengestelde indexen](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) bieden een manier om een index voor groepen velden te maken voor maximaal 8 velden. Dit soort index verschilt van de systeemeigen samengestelde indexen in MongoDB. In Azure Cosmos DB worden samengestelde indexen gebruikt voor het sorteren van bewerkingen die op meerdere velden worden toegepast. Als u een samengestelde index wilt maken, moet u meer dan één eigenschap als de parameter opgeven:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

Azure Cosmos DB ondersteunt GridFS via elk GridFS-compatibel MongoDB-stuur programma.

## <a name="replication"></a>Replicatie

Cosmos DB biedt ondersteuning voor automatische, systeemeigen replicatie op de laagste lagen. Deze logica is uitgebreid om tevens globale replicatie met een lage latentie te bereiken. Cosmos DB biedt geen ondersteuning voor handmatige replicatieopdrachten.





## <a name="retryable-writes"></a>Herhaal bare schrijf bewerkingen

Azure Cosmos DB biedt nog geen ondersteuning voor herstel bare schrijf bewerkingen. Client Stuur Programma's moeten `retryWrites=false` aan hun Connection String worden toegevoegd.

## <a name="sharding"></a>Sharding

Azure Cosmos DB biedt ondersteuning voor automatische sharding aan serverzijde. Het beheert automatisch Shard maken, plaatsen en balanceren. Azure Cosmos DB biedt geen ondersteuning voor handmatige sharding-opdrachten, wat betekent dat u geen opdrachten hoeft aan te roepen zoals addShard, balancerStart, moveChunk enzovoort. U hoeft alleen de Shard-sleutel op te geven tijdens het maken van de containers of het uitvoeren van query's op de gegevens.

## <a name="sessions"></a>Sessies

Azure Cosmos DB biedt nog geen ondersteuning voor sessieopdrachten aan de serverzijde.

## <a name="time-to-live-ttl"></a>TTL (time-to-live)

Azure Cosmos DB ondersteunt een TTL (time-to-Live) op basis van de tijds tempel van het document. TTL kan worden ingeschakeld voor verzamelingen van de [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gebruikers- en rolbeheer

Azure Cosmos DB biedt nog geen ondersteuning voor gebruikers en rollen. Het ondersteunt echter op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) en alleen-lezen en alleen-lezen wacht woorden of sleutels die kunnen worden verkregen via het deel venster connection string in het [Azure Portal](https://portal.azure.com).

## <a name="write-concern"></a>Schrijfprobleem

Bepaalde toepassingen vertrouwen op een [schrijfprobleem](https://docs.mongodb.com/manual/reference/write-concern/) dat het aantal vereiste reacties tijdens een schrijfbewerking opgeeft. Als gevolg van de manier waarop Azure Cosmos DB replicatie afhandelt, worden alle schrijf bewerkingen standaard automatisch een meerderheid van quorum wanneer ze een sterke consistentie gebruiken. Alle schrijfproblemen die in clientcode zijn opgegeven, worden genegeerd. Zie voor meer informatie [consistentie niveaus gebruiken om het artikel Beschik baarheid en prestaties te maximaliseren](consistency-levels.md) .

## <a name="next-steps"></a>Volgende stappen

- Zie [Mongo-functies van versie 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/) voor meer informatie
- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
