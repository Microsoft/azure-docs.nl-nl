---
title: Migreren van Couch Base naar Azure Cosmos DB SQL-API
description: Stapsgewijze richt lijnen voor het migreren van Couch Base naar Azure Cosmos DB SQL-API
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: a15c6b5919f428b28daab86fea9c3b6473d19162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606195"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migreren van Couch Base naar Azure Cosmos DB SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB is een schaal bare, wereld wijd gedistribueerde, volledig beheerde data base. Het biedt gegarandeerde lage latentie toegang tot uw gegevens. Zie het artikel [overzicht](introduction.md) voor meer informatie over Azure Cosmos db. Dit artikel bevat instructies voor het migreren van Java-toepassingen die zijn verbonden met Couch Base naar een SQL-API-account in Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Verschillen in de nomenclatuur

Hieronder ziet u de belangrijkste functies die in Azure Cosmos DB anders werken in vergelijking tot Couch Base:

| Couchbase | Azure Cosmos DB |
|--|--|
| Couch Base-Server | Account |
| Verzamelingen | Database |
| Verzamelingen | Container/verzameling |
| JSON-document | Item/document |

## <a name="key-differences"></a>Belangrijke verschillen

* Azure Cosmos DB heeft een ID-veld in het document, terwijl Couch base de ID als onderdeel van Bucket heeft. Het veld ID is uniek voor de partitie.

* Azure Cosmos DB wordt geschaald met behulp van de methode partitioneren of sharding. Dit betekent dat de gegevens worden gesplitst in meerdere Shards/partities. Deze partities/Shards worden gemaakt op basis van de partitie sleutel eigenschap die u opgeeft. U kunt de partitie sleutel selecteren om Lees-en schrijf bewerkingen te optimaliseren of te lezen/schrijven. Zie het artikel [partitioning](./partitioning-overview.md) voor meer informatie.

* In Azure Cosmos DB is het niet vereist voor de hiërarchie op het hoogste niveau om de verzameling aan te duiden, omdat de naam van de verzameling al bestaat. Deze functie maakt de JSON-structuur veel eenvoudiger. Hier volgt een voor beeld waarin de verschillen in het gegevens model tussen Couch base en Azure Cosmos DB worden weer gegeven:

   **Couch base**: document-id = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: verwijst naar id in het document, zoals hieronder wordt weer gegeven

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Ondersteuning voor Java SDK

Azure Cosmos DB heeft Sdk's voor het ondersteunen van verschillende Java-frameworks:

* Async-SDK
* Spring boot SDK

In de volgende secties wordt beschreven wanneer u elk van deze Sdk's gebruikt. Bekijk een voor beeld waarin we drie soorten workloads hebben:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couch Base als document opslagplaats & lente op gegevens gebaseerde aangepaste query's

Als de werk belasting die u migreert, is gebaseerd op de lente-boot gebaseerde SDK, kunt u de volgende stappen uitvoeren:

1. Voeg Parent toe aan het POM.xml-bestand:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Eigenschappen toevoegen aan het POM.xml-bestand:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Afhankelijkheden toevoegen aan het POM.xml-bestand:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Voeg toepassings eigenschappen onder resources toe en geef het volgende op. Zorg ervoor dat u de para meters URL, sleutel en database naam vervangt:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definieer de naam van de verzameling in het model. U kunt ook aanvullende aantekeningen opgeven. Bijvoorbeeld ID, partitie sleutel om ze expliciet aan te duiden:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Hier volgen de code fragmenten voor ruwe bewerkingen:

### <a name="insert-and-update-operations"></a>INSERT-en update-bewerkingen

Waarbij *_repo* het object van de opslag plaats en *doc* het object van de klasse POJO is. U kunt gebruiken `.save` om in te voegen of te upsert (als het document met de opgegeven id is gevonden). Het volgende code fragment laat zien hoe u een doc-object invoegt of bijwerkt:

```_repo.save(doc);```

### <a name="delete-operation"></a>Verwijder bewerking

Bekijk het volgende code fragment, waarbij document object ID en partitie sleutel verplicht zijn om het object te zoeken en te verwijderen:

```_repo.delete(doc);```

### <a name="read-operation"></a>Lees bewerking

U kunt het document lezen met of zonder de partitie sleutel op te geven. Als u de partitie sleutel niet opgeeft, wordt deze behandeld als een kruis partitie query. Houd rekening met de volgende code voorbeelden, waarbij eerst de bewerking wordt uitgevoerd met de ID en het partitie sleutel veld. In het tweede voor beeld wordt een standaard veld & gebruikt zonder het partitie sleutel veld op te geven.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Dat is alles. u kunt uw toepassing nu gebruiken met Azure Cosmos DB. Het volledige code voorbeeld voor het voor beeld dat in dit document wordt beschreven, is beschikbaar in de [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) github opslag plaats.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couch Base als document opslagplaats & het gebruik van N1QL-query's

N1QL query's zijn de manier om query's te definiëren in de Couch base.

|N1QL-query | Azure CosmosDB-query|
|-------------------|-------------------|
|Selecteer META ( `TravelDocument` ). id als id, `TravelDocument` . * from `TravelDocument` WHERE `_type` = "com. xx. xx. xx. xxx. xxx. xxxx" en Country = "India" en alle m in visa voldoen aan m. type = = ' multi-entry ' en m. land in [' India ', Bhutan '] sorteren op ` Validity` DESC limiet 25 offset 0 | Selecteer c. id, c van c lid m in c. Country = ' India ' waarbij c._type = ' com. xx. xx. xx. xxx. xxx. xxxx ' en c. Country = ' India ' en m. type = ' multi-entry ' en m. country IN (' India ', ' Bhutan ') ORDER BY c. geldigheid DESC OFFSET 0 limiet 25 |

U kunt de volgende wijzigingen in uw N1QL-query's waarnemen:

* U hoeft het sleutel woord META niet te gebruiken of naar het document van het eerste niveau te verwijzen. In plaats daarvan kunt u een eigen verwijzing naar de container maken. In dit voor beeld hebben we het gezien als ' c ' (dit kan alles zijn). Deze verwijzing wordt gebruikt als een voor voegsel voor alle velden van het eerste niveau. FR-voor beeld, c.id, c. land enz.

* U kunt in plaats van ' ANY ' nu een koppeling uitvoeren op een subdocument en ernaar verwijzen met een toegewezen alias zoals ' m '. Zodra u een alias voor een subdocument hebt gemaakt, moet u alias gebruiken. Bijvoorbeeld m. land.

* De volg orde van de verschuiving wijkt af van Azure Cosmos DB query. u moet eerst een OFFSET opgeven. U wordt aangeraden geen lente data SDK te gebruiken als u Maxi maal aangepaste, gedefinieerde query's gebruikt, omdat deze onnodig overhead aan de client zijde kan hebben terwijl de query wordt door gegeven aan Azure Cosmos DB. We hebben in plaats daarvan een directe async Java-SDK, die in dit geval veel efficiënt kan worden gebruikt.

### <a name="read-operation"></a>Lees bewerking

Gebruik de asynchrone Java-SDK met de volgende stappen:

1. Configureer de volgende afhankelijkheden op het POM.xml-bestand:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Maak een verbindings object voor Azure Cosmos DB met behulp van de- `ConnectionBuilder` methode, zoals wordt weer gegeven in het volgende voor beeld. Zorg ervoor dat u deze declaratie in de bonen plaatst, zodat de volgende code slechts één keer wordt uitgevoerd:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Als u de query wilt uitvoeren, moet u het volgende code fragment uitvoeren:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Nu kunt u met de bovenstaande methode meerdere query's door geven en zonder enige moeite worden uitgevoerd. Als u de vereiste hebt om één grote query uit te voeren, die kan worden opgesplitst in meerdere query's, kunt u het volgende code fragment proberen in plaats van het vorige:

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

Met de vorige code kunt u query's parallel uitvoeren en de gedistribueerde uitvoeringen verhogen om te optimaliseren. Verder kunt u de bewerkingen INSERT en update als volgt uitvoeren:

### <a name="insert-operation"></a>Invoeg bewerking

Voer de volgende code uit om het document in te voegen:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Abonneer u vervolgens op mono als:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Bewerking Upsert

Voor de bewerking Upsert moet u het document opgeven dat moet worden bijgewerkt. Als u het volledige document wilt ophalen, kunt u het fragment dat wordt vermeld onder koptekst lezen gebruiken en vervolgens de vereiste velden wijzigen. Het volgende code fragment upsert het document:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Meld u vervolgens aan bij mono. Raadpleeg het fragment voor een mono-abonnement in de INSERT-bewerking.

### <a name="delete-operation"></a>Verwijder bewerking

In het volgende fragment wordt de bewerking verwijderen uitgevoerd:

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Vervolgens abonneert u zich op mono, raadpleegt u het fragment met een mono-abonnement in een invoeg bewerking. Het volledige code voorbeeld is beschikbaar in de [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) github opslag plaats.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couch Base als sleutel/waarde-paar

Dit is een eenvoudig type werk belasting waarin u zoek acties kunt uitvoeren in plaats van query's. Gebruik de volgende stappen voor sleutel/waarde-paren:

1. Overweeg '/ID ' als primaire sleutel. Dit zorgt ervoor dat u de opzoek bewerking rechtstreeks kunt uitvoeren in de specifieke partitie. Maak een verzameling en geef '/ID ' op als partitie sleutel.

1. Schakel het indexeren volledig uit. Omdat u opzoek bewerkingen wilt uitvoeren, is er geen punt om de overhead van indexeren te belasten. Als u indexeren wilt uitschakelen, meldt u zich aan bij Azure Portal, ga naar Azure Cosmos DB account. Open de **Data Explorer**, selecteer uw **Data Base** en de **container**. Open het tabblad **schaal & instellingen** en selecteer het  **indexerings beleid**. Momenteel ziet het indexerings beleid er als volgt uit:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Vervang het bovenstaande indexerings beleid door het volgende beleid:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Gebruik het volgende code fragment om het verbindings object te maken. Verbindings object (om in te plaatsen @Bean of het statisch te maken):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

U kunt de ruwe bewerkingen nu als volgt uitvoeren:

### <a name="read-operation"></a>Lees bewerking

Als u het item wilt lezen, gebruikt u het volgende code fragment:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Invoeg bewerking

U kunt de volgende code uitvoeren om een item in te voegen:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Abonneer u vervolgens op mono als:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Bewerking Upsert

Als u de waarde van een item wilt bijwerken, raadpleegt u het volgende code fragment:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Vervolgens abonneert u zich op mono, raadpleegt u het fragment met een mono-abonnement in een invoeg bewerking.

### <a name="delete-operation"></a>Verwijder bewerking

Gebruik het volgende code fragment om de Verwijder bewerking uit te voeren:

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Vervolgens abonneert u zich op mono, raadpleegt u het fragment met een mono-abonnement in een invoeg bewerking. Het volledige code voorbeeld is beschikbaar in de [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) github opslag plaats.

## <a name="data-migration"></a>Gegevensmigratie

Er zijn twee manieren om gegevens te migreren.

* **Azure Data Factory gebruiken:** Dit is de aanbevolen methode voor het migreren van de gegevens. Zie het artikel over Azure [Cosmos DB Data Factory connector](../data-factory/connector-azure-cosmos-db.md) voor gedetailleerde stappen voor het configureren van de bron als Couch base en sink als Azure Cosmos DB SQL-API.

* **Gebruik het Azure Cosmos DB hulp programma voor het importeren van gegevens:** Deze optie wordt aanbevolen om te migreren met behulp van Vm's met minder gegevens. Zie het artikel over het [importeren van gegevens](./import-data.md) uit voor gedetailleerde stappen.

## <a name="next-steps"></a>Volgende stappen

* Zie [prestaties en schalen testen met Azure Cosmos DB](./performance-testing.md) artikel om prestatie tests uit te voeren.
* Zie [Tips voor prestaties voor Azure Cosmos DB](./performance-tips-async-java.md) artikel voor meer informatie over het optimaliseren van de code.
* Verken de Java async v3 SDK, [SDK Reference](https://github.com/Azure/azure-cosmosdb-java/tree/v3) github opslag plaats.
