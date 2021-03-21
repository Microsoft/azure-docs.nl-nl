---
title: MongoDB-extensie opdrachten voor het beheren van gegevens in de API van Azure Cosmos DB voor MongoDB
description: In dit artikel wordt beschreven hoe u MongoDB-extensie opdrachten gebruikt voor het beheren van gegevens die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: deba6696eb71287902fa3970ed2d83d0b09ac08d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658483"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Gebruik MongoDB-extensie opdrachten voor het beheren van gegevens die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Het volgende document bevat de aangepaste actie opdrachten die specifiek zijn voor de API van Azure Cosmos DB voor MongoDB. Deze opdrachten kunnen worden gebruikt om database resources te maken en te verkrijgen die specifiek zijn voor het [Azure Cosmos DB capaciteits model](account-databases-containers-items.md).

Met de API van de Azure Cosmos DB voor MongoDB kunt u profiteren van de voor delen Cosmos DB zoals wereld wijde distributie, automatische sharding, hoge Beschik baarheid, latentie garanties, Automatische versleuteling op rest, back-ups en nog veel meer, terwijl uw investeringen in uw MongoDB-app behouden blijven. U kunt communiceren met de API van de Azure Cosmos DB voor MongoDB door gebruik te maken van de open-source [MongoDb-client Stuur Programma's](https://docs.mongodb.org/ecosystem/drivers). De API van Azure Cosmos DB voor MongoDB maakt het gebruik van bestaande client Stuur Programma's mogelijk door te voldoen aan het [MongoDb wire-protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Ondersteuning voor MongoDB-Protocol

De API van Azure Cosmos DB voor MongoDB is compatibel met MongoDB-Server versie 4,0, 3,6 en 3,2. Zie Ondersteunde functies en syntaxis in [4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)en [3,2](mongodb-feature-support.md) artikelen voor meer informatie. 

De volgende extensie opdrachten bieden de mogelijkheid om Azure Cosmos DB-specifieke resources te maken en te wijzigen via database aanvragen:

* [Data base maken](#create-database)
* [Data base bijwerken](#update-database)
* [Data base ophalen](#get-database)
* [Verzameling maken](#create-collection)
* [Verzameling bijwerken](#update-collection)
* [Verzameling ophalen](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Data base maken

Met de opdracht Create Data Base extension maakt u een nieuwe MongoDB-data base. De database naam kan worden gebruikt vanuit de database context die met de `use database` opdracht is ingesteld. In de volgende tabel worden de para meters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| `customAction`   |  `string`  |   De naam van de aangepaste opdracht moet ' CreateDatabase ' zijn.      |
| `offerThroughput` | `int`  | Ingerichte door Voer die u hebt ingesteld voor de data base. Deze parameter is optioneel. |
| `autoScaleSettings` | `Object` | Vereist voor de [modus voor automatisch schalen](provision-throughput-autoscale.md). Dit object bevat de instellingen die zijn gekoppeld aan de modus capaciteit automatisch schalen. U kunt de waarde instellen `maxThroughput` , die de hoogste hoeveelheid aanvraag eenheden beschrijft waarmee de verzameling dynamisch wordt verhoogd. |

### <a name="output"></a>Uitvoer

Als de opdracht is geslaagd, wordt het volgende antwoord geretourneerd:

```javascript
{ "ok" : 1 }
```

Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="create-a-database"></a>Een database maken

Gebruik de volgende opdracht om een Data Base te maken `"test"` met de naam die gebruikmaakt van alle standaard waarden:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Met deze opdracht wordt een Data Base gemaakt zonder door Voer op database niveau. Dit betekent dat de verzamelingen binnen deze data base de hoeveelheid door voer moeten opgeven die u moet gebruiken.

#### <a name="create-a-database-with-throughput"></a>Een Data Base maken met door Voer

Gebruik de volgende opdracht als u een Data Base met de naam wilt maken `"test"` en een ingerichte door Voer van 1000 RUs op [database niveau](set-throughput.md#set-throughput-on-a-database) wilt opgeven:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Hiermee maakt u een Data Base en stelt u een door Voer in. Alle verzamelingen in deze data base delen de ingestelde door Voer, tenzij de verzamelingen worden gemaakt met [een specifiek doorvoer niveau](set-throughput.md#set-throughput-on-a-database-and-a-container).

#### <a name="create-a-database-with-autoscale-throughput"></a>Een Data Base maken met de door Voer van automatisch schalen

Gebruik de volgende opdracht om een Data Base te maken met `"test"` de naam en om een maximale door Voer van 20.000 ru/s op [database niveau](set-throughput.md#set-throughput-on-a-database)in te stellen:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Data base bijwerken

Met de opdracht data base-extensie bijwerken worden de eigenschappen bijgewerkt die zijn gekoppeld aan de opgegeven Data Base. Het wijzigen van de data base van de ingerichte door voer naar automatisch schalen en vice versa wordt alleen ondersteund in azure Portal. In de volgende tabel worden de para meters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| `customAction`    |    `string`     |   De naam van de aangepaste opdracht. Moet ' UpdateDatabase ' zijn.      |
|  `offerThroughput`   |  `int`       |     Nieuwe ingerichte door Voer die u wilt instellen voor de Data Base als de Data Base [door Voer op database niveau](set-throughput.md#set-throughput-on-a-database) gebruikt  |
| `autoScaleSettings` | `Object` | Vereist voor de [modus voor automatisch schalen](provision-throughput-autoscale.md). Dit object bevat de instellingen die zijn gekoppeld aan de modus capaciteit automatisch schalen. U kunt de waarde instellen `maxThroughput` , waarmee de hoogste hoeveelheid aanvraag eenheden wordt beschreven waarmee de data base dynamisch wordt verhoogd. |

Met deze opdracht wordt de data base gebruikt die is opgegeven in de context van de sessie. Dit is de data base die u in de opdracht hebt gebruikt `use <database>` . Op het moment kan de naam van de data base niet worden gewijzigd met deze opdracht.

### <a name="output"></a>Uitvoer

Als de opdracht is geslaagd, wordt het volgende antwoord geretourneerd:

```javascript
{ "ok" : 1 }
```

Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>De ingerichte door Voer bijwerken die is gekoppeld aan een Data Base

Als u de ingerichte door Voer van een Data Base met `"test"` de naam naar 1200 RUs wilt bijwerken, gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>De door Voer voor automatisch schalen bijwerken die is gekoppeld aan een Data Base

Als u de ingerichte door Voer van een Data Base met `"test"` de naam naar 20.000 RUs wilt bijwerken of als u deze wilt transformeren naar een [doorvoer niveau automatisch schalen](provision-throughput-autoscale.md), gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Data base ophalen

De opdracht data base-extensie ophalen retourneert het database object. De database naam wordt gebruikt vanuit de database context waartegen de opdracht wordt uitgevoerd.

```javascript
{
  customAction: "GetDatabase"
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   De naam van de aangepaste opdracht. Moet ' GetDatabase ' zijn|
        
### <a name="output"></a>Uitvoer

Als de opdracht is geslaagd, bevat het antwoord een document met de volgende velden:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |   `int`     |   De status van het antwoord. 1 = = geslaagd. 0 = = mislukt.      |
| `database`    |    `string`        |   De naam van de data base.      |
|   `provisionedThroughput`  |    `int`      |    Ingerichte door Voer die is ingesteld op de Data Base als de Data Base [hand matige door Voer op database niveau](set-throughput.md#set-throughput-on-a-database) gebruikt     |
| `autoScaleSettings` | `Object` | Dit object bevat de capaciteits parameters die aan de Data Base zijn gekoppeld als de [modus voor automatisch schalen](provision-throughput-autoscale.md)wordt gebruikt. De `maxThroughput` waarde beschrijft de hoogste hoeveelheid aanvraag eenheden waarmee de data base dynamisch wordt verhoogd. |

Als de opdracht mislukt, wordt een standaard antwoord voor een aangepaste opdracht geretourneerd. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="get-the-database"></a>De data base ophalen

Als u het database object voor een Data Base `"test"` met de naam wilt ophalen, gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Als de data base geen gekoppelde door Voer heeft, is de uitvoer:

```javascript
{ "database" : "test", "ok" : 1 }
```

Als er een [hand matige door Voer](set-throughput.md#set-throughput-on-a-database) voor de data base is gekoppeld, worden de waarden in de uitvoer weer gegeven `provisionedThroughput` :

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Als voor de Data Base een [automatisch schalen door Voer op database niveau](provision-throughput-autoscale.md) is gekoppeld, wordt in de uitvoer de weer gegeven `provisionedThroughput` , waarin de minimale ru/s voor de Data Base worden beschreven en het `autoScaleSettings` object, inclusief de `maxThroughput` , waarmee de maximale ru/s voor de data base wordt beschreven.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Verzameling maken

Met de opdracht verzamelings extensie maken maakt u een nieuwe MongoDB-verzameling. De database naam wordt gebruikt in de context van de data bases die is ingesteld met de `use database` opdracht. De indeling van de CreateCollection opdracht is als volgt:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" to use Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:

| **Veld** | **Type** | **Vereist** | **Beschrijving** |
|---------|---------|---------|---------|
| `customAction` | `string` | Vereist | De naam van de aangepaste opdracht. Moet ' CreateCollection ' zijn.|
| `collection` | `string` | Vereist | De naam van de verzameling. Er zijn geen speciale tekens of spaties toegestaan.|
| `offerThroughput` | `int` | Optioneel | Ingerichte door Voer om in te stellen op de data base. Als deze para meter niet wordt gegeven, wordt standaard de minimum versie 400 RU/s. * Als u de door voer wilt opgeven na meer dan 10.000 RU/s, `shardKey` is de para meter vereist.|
| `shardKey` | `string` | Vereist voor verzamelingen met een grote door Voer | Het pad naar de Shard-sleutel voor de Shard-verzameling. Deze para meter is vereist als u meer dan 10.000 RU/s in hebt ingesteld `offerThroughput` .  Als deze is opgegeven, moeten alle documenten die zijn ingevoegd, deze sleutel en waarde hebben. |
| `autoScaleSettings` | `Object` | Vereist voor [modus voor automatisch schalen](provision-throughput-autoscale.md) | Dit object bevat de instellingen die zijn gekoppeld aan de modus capaciteit automatisch schalen. U kunt de waarde instellen `maxThroughput` , die de hoogste hoeveelheid aanvraag eenheden beschrijft waarmee de verzameling dynamisch wordt verhoogd. |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdracht antwoord. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Een verzameling maken met de minimale configuratie

Als u een nieuwe verzameling met de naam `"testCollection"` en de standaard waarden wilt maken, gebruikt u de volgende opdracht: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Dit resulteert in een nieuwe vaste, unsharded-verzameling met 400RU/s en een index op het `_id` veld die automatisch wordt gemaakt. Dit type configuratie is ook van toepassing wanneer u nieuwe verzamelingen maakt met behulp van de `insert()` functie. Bijvoorbeeld: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Een unsharded-verzameling maken

Als u een unsharded-verzameling met `"testCollection"` de naam en de ingerichte door Voer van 1000 RUs wilt maken, gebruikt u de volgende opdracht: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

U kunt een verzameling maken met Maxi maal 10.000 RU/s als de, `offerThroughput` zonder dat u een Shard-sleutel hoeft op te geven. Bekijk de volgende sectie voor verzamelingen met een grotere door voer.

#### <a name="create-a-sharded-collection"></a>Een Shard-verzameling maken

Als u een Shard-verzameling wilt maken met de naam `"testCollection"` en de ingerichte door Voer van 11.000 RUs en een `shardkey` eigenschap "a. b", gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Voor deze opdracht is nu de `shardKey` para meter vereist, omdat er meer dan 10.000 ru/s zijn opgegeven in de `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Een unsharded-verzameling voor automatisch schalen maken

Als u een unsharded-verzameling met `'testCollection'` de naam die gebruikmaakt van de [capaciteit voor automatisch schalen](provision-throughput-autoscale.md) , ingesteld op 4.000 ru/s, gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Voor de `autoScaleSettings.maxThroughput` waarde kunt u een bereik van 4.000 ru/s tot 10.000 ru/s zonder een Shard-sleutel opgeven. Voor een hogere door Voer voor automatisch schalen moet u de `shardKey` para meter opgeven.

#### <a name="create-a-sharded-autoscale-collection"></a>Een Shard-verzameling voor automatisch schalen maken

Als u een Shard-verzameling wilt maken `'testCollection'` met de naam met een Shard-sleutel met `'a.b'` de naam en die gebruikmaakt van de [capaciteit voor automatisch schalen](provision-throughput-autoscale.md) , ingesteld op 20.000 ru/s, gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Verzameling bijwerken

Met de opdracht verzamelings extensie bijwerken worden de eigenschappen bijgewerkt die zijn gekoppeld aan de opgegeven verzameling. Het wijzigen van uw verzameling van ingerichte door voer naar automatisch schalen en vice versa wordt alleen ondersteund in azure Portal.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" if using Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting. Changing between Autoscale and Provisioned throughput is only supported in the Azure Portal.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   De naam van de aangepaste opdracht. Moet ' UpdateCollection ' zijn.      |
|  `collection`   |   `string`      |   De naam van de verzameling.       |
| `offerThroughput` | `int` |   Ingerichte door Voer om in te stellen voor de verzameling.|
| `autoScaleSettings` | `Object` | Vereist voor de [modus voor automatisch schalen](provision-throughput-autoscale.md). Dit object bevat de instellingen die zijn gekoppeld aan de modus capaciteit automatisch schalen. De `maxThroughput` waarde beschrijft de hoogste hoeveelheid aanvraag eenheden die de verzameling dynamisch wordt verhoogd. |

## <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdracht antwoord. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>De ingerichte door Voer bijwerken die is gekoppeld aan een verzameling

Als u de ingerichte door Voer van een verzameling met `"testCollection"` de naam naar 1200 RUs wilt bijwerken, gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Verzameling ophalen

De opdracht verzameling aangepaste ophalen retourneert het verzamelings object.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| `customAction`    |   `string`      |   De naam van de aangepaste opdracht. Moet ' GetCollection ' zijn.      |
| `collection`    |    `string`     |    De naam van de verzameling.     |

### <a name="output"></a>Uitvoer

Als de opdracht is geslaagd, bevat het antwoord een document met de volgende velden:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |    `int`     |   De status van het antwoord. 1 = = geslaagd. 0 = = mislukt.      |
| `database`    |    `string`     |   De naam van de data base.      |
| `collection`    |    `string`     |    De naam van de verzameling.     |
|  `shardKeyDefinition`   |   `document`      |  Index specificatie document dat wordt gebruikt als een Shard-sleutel. Dit is een optionele antwoord parameter.       |
|  `provisionedThroughput`   |   `int`      |    Ingerichte door Voer om in te stellen voor de verzameling. Dit is een optionele antwoord parameter.     |
| `autoScaleSettings` | `Object` | Dit object bevat de capaciteits parameters die aan de Data Base zijn gekoppeld als de [modus voor automatisch schalen](provision-throughput-autoscale.md)wordt gebruikt. De `maxThroughput` waarde beschrijft de hoogste hoeveelheid aanvraag eenheden die de verzameling dynamisch wordt verhoogd. |

Als de opdracht mislukt, wordt een standaard antwoord voor een aangepaste opdracht geretourneerd. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

#### <a name="get-the-collection"></a>De verzameling ophalen

Als u het verzamelings object voor een verzameling `"testCollection"` met de naam wilt ophalen, gebruikt u de volgende opdracht:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Als aan de verzameling een bijbehorende doorvoer capaciteit is gekoppeld, wordt de waarde daarin vermeld `provisionedThroughput` en wordt de uitvoer:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Als de verzameling een bijbehorende automatisch schalen heeft, wordt het `autoScaleSettings` object met de `maxThroughput` para meter inbegrepen, waarmee de maximale door Voer wordt gedefinieerd. de verzameling neemt dynamisch toe. Daarnaast bevat het ook de `provisionedThroughput` waarde, waarmee de minimale door Voer wordt gedefinieerd die deze verzameling vermindert als er geen aanvragen in de verzameling zijn: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Als de verzameling een [door Voer op database niveau](set-throughput.md#set-throughput-on-a-database)deelt, wordt de uitvoer op de modus voor automatisch schalen of hand matig uitgevoerd:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Standaard uitvoer van een aangepaste opdracht

Als deze niet is opgegeven, bevat een aangepast antwoord een document met de volgende velden:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |    `int`     |   De status van het antwoord. 1 = = geslaagd. 0 = = mislukt.      |
| `code`    |   `int`      |   Wordt alleen geretourneerd als de opdracht is mislukt (bijvoorbeeld OK = = 0). Bevat de fout code MongoDB. Dit is een optionele antwoord parameter.      |
|  `errMsg`   |  `string`      |    Wordt alleen geretourneerd als de opdracht is mislukt (bijvoorbeeld OK = = 0). Bevat een gebruikers vriendelijk fout bericht. Dit is een optionele antwoord parameter.      |

Bijvoorbeeld:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met de volgende Azure Cosmos DB concepten: 

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
