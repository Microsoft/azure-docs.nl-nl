---
title: Tips voor betere prestaties voor Azure Cosmos DB async Java SDK v2
description: Meer informatie over client configuratie opties voor het verbeteren van de prestaties van Azure Cosmos-Data Base voor asynchrone Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: bd009ae4909c8cb016a31323294df3a359eb7c51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033660"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Tips voor betere prestaties voor Azure Cosmos DB async Java SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](performance-tips-async-java.md)
> * [Sync Java-SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> Dit is *niet* de meest recente Java-SDK voor Azure Cosmos db. U moet uw project upgraden naar [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) en vervolgens de Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md)lezen. Volg de instructies in de hand leiding [migratie naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) en [reactor versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) om een upgrade uit te voeren. 
> 
> De tips voor betere prestaties in dit artikel zijn alleen voor de Azure Cosmos DB asynchrone Java SDK v2. Zie de Azure Cosmos DB async Java SDK v2 [release opmerkingen](sql-api-sdk-async-java.md), de [maven-opslag plaats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)en Azure Cosmos DB async Java SDK v2 [Troubleshooting Guide (Engelstalig](troubleshoot-java-async-sdk.md) ) voor meer informatie.
>

Azure Cosmos DB is een snelle en flexibele gedistribueerde data base die naadloos kan worden geschaald met gegarandeerde latentie en door voer. U hoeft geen grote architectuur wijzigingen aan te brengen of complexe code te schrijven om uw data base te schalen met Azure Cosmos DB. Omhoog en omlaag schalen is net zo eenvoudig als het maken van één API-aanroep of SDK-methode aanroep. Omdat Azure Cosmos DB echter via netwerk aanroepen wordt benaderd, zijn er optimalisaties aan de client zijde die u kunt uitvoeren om de prestaties van de [Azure Cosmos DB async-SDK v2](sql-api-sdk-async-java.md)te bereiken.

Als u daarom vraagt hoe u de prestaties van mijn Data Base kunt verbeteren? Houd rekening met de volgende opties:

## <a name="networking"></a>Netwerken

* **Verbindings modus: directe modus gebruiken**
    
  Hoe een client verbinding maakt met Azure Cosmos DB heeft belang rijke gevolgen voor de prestaties, met name op het gebied van latentie aan de client zijde. De *ConnectionMode* is een belang rijke configuratie-instelling die beschikbaar is voor het configureren van de *Connection Policy* van de client. Voor Azure Cosmos DB async Java SDK v2 zijn de twee beschik bare ConnectionModes:  
      
  * [Gateway (standaard)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  De gateway modus wordt op alle SDK-platforms ondersteund en is standaard de geconfigureerde optie. Als uw toepassingen worden uitgevoerd in een bedrijfs netwerk met strikte firewall beperkingen, is de gateway modus de beste keuze, omdat deze gebruikmaakt van de standaard HTTPS-poort en een enkel eind punt.   De verhoudingen van de prestaties zijn echter wel dat de gateway modus een extra netwerk-hop omvat elke keer dat gegevens worden gelezen of geschreven naar Azure Cosmos DB. Als gevolg hiervan biedt de directe modus betere prestaties vanwege minder netwerk-hops.
  
  De *ConnectionMode* wordt geconfigureerd tijdens de constructie van het *DocumentClient* -exemplaar met de para meter *Connection Policy* .

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Plaats clients in dezelfde Azure-regio voor betere prestaties**

  Als dat mogelijk is, plaatst u toepassingen die Azure Cosmos DB aanroepen, in dezelfde regio als de Azure Cosmos-data base. Voor een benadering van een vergelijking worden de aanroepen naar Azure Cosmos DB binnen dezelfde regio binnen 1-2 MS uitgevoerd, maar de latentie tussen de West-en Oost kust van de Verenigde Staten is >50 MS. Deze latentie kan waarschijnlijk variëren van aanvraag om aan te vragen, afhankelijk van de route die door de aanvraag wordt door gegeven aan de limiet van de client naar de grens van het Azure-Data Center. De laagst mogelijke latentie wordt bereikt door ervoor te zorgen dat de aanroepende toepassing zich in dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eind punt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschik bare regio's.

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Afbeelding van het verbindings beleid voor Azure Cosmos DB" border="false":::

## <a name="sdk-usage"></a>SDK-gebruik

* **De meest recente SDK installeren**

  De Azure Cosmos DB Sdk's worden voortdurend verbeterd om de beste prestaties te leveren. Raadpleeg de pagina's met [opmerkingen](sql-api-sdk-async-java.md) bij de Azure Cosmos DB ASYNC Java SDK v2 om de meest recente SDK te bepalen en verbeteringen te bekijken.

* **Een singleton Azure Cosmos DB-client gebruiken voor de levens duur van uw toepassing**

  Elk AsyncDocumentClient-exemplaar is thread-safe en voert efficiënt beheer van de verbinding en het opslaan van adressen. Om het beheer van efficiënte verbindingen en betere prestaties door AsyncDocumentClient mogelijk te maken, is het raadzaam één exemplaar van AsyncDocumentClient per AppDomain te gebruiken voor de levens duur van de toepassing.

* **Connection Policy afstemmen**

  Standaard worden de directe modus Cosmos DB aanvragen via TCP verzonden wanneer u de Azure Cosmos DB async Java SDK v2 gebruikt. Intern gebruikt de SDK een speciale directe modus architectuur om netwerk bronnen dynamisch te beheren en de beste prestaties te verkrijgen.

  In de Azure Cosmos DB async-SDK v2 is de optie directe modus de beste keuze om de database prestaties te verbeteren met de meeste werk belastingen. 

  * ***Overzicht van directe modus***

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Afbeelding van de architectuur van de directe modus" border="false":::
  
  De architectuur aan de client zijde die in directe modus wordt gebruikt, maakt voorspel bare netwerk gebruik en multiplex toegang tot Azure Cosmos DB replica's mogelijk. In het bovenstaande diagram ziet u hoe directe modus client aanvragen naar replica's in de Cosmos DB back-end stuurt. De architectuur van de directe modus wijst Maxi maal 10 **kanalen** aan de client zijde per database replica toe. Een kanaal is een TCP-verbinding voorafgegaan door een aanvraag buffer, die 30 aanvragen diep is. De kanalen die deel uitmaken van een replica, worden dynamisch toegewezen naar behoefte aan het **service-eind punt** van de replica. Wanneer de gebruiker een aanvraag uitgeeft in de directe modus, stuurt de **TransportClient** de aanvraag naar het juiste service-eind punt op basis van de partitie sleutel. De **aanvraag wachtrij** buffers aanvragen vóór het service-eind punt.

  * ***Configuratie opties voor Connection Policy voor directe modus***

    Als eerste stap gebruikt u de volgende aanbevolen configuratie-instellingen hieronder. Neem contact op met het [Azure Cosmos DB team](mailto:CosmosDBPerformanceSupport@service.microsoft.com) als u problemen ondervindt in dit onderwerp.

    Als u Azure Cosmos DB gebruikt als referentie database (dat wil zeggen, de data base wordt gebruikt voor veel lees bewerkingen op punten en weinig schrijf bewerkingen), kan het acceptabel zijn om *idleEndpointTimeout* in te stellen op 0 (dat wil zeggen, geen time-out).


    | Configuratie optie       | Standaard    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT 0,5 S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Programmeer tips voor directe modus***

  Raadpleeg het artikel over het oplossen van [problemen met](troubleshoot-java-async-sdk.md) de Azure Cosmos DB ASYNC Java SDK v2 als basis lijn voor het oplossen van problemen met de SDK.
  
  Enkele belang rijke tips voor het Program meren van de directe modus:
  
  * **Multi-threading in uw toepassing gebruiken voor efficiënte TCP-gegevens overdracht** : nadat u een aanvraag hebt gedaan, moet uw toepassing worden geabonneerd om gegevens te ontvangen in een andere thread. Als u dit niet doet, wordt een onbedoelde ' halve duplex ' bewerking geforceerd en worden de volgende aanvragen geblokkeerd voor het antwoord op de vorige aanvraag.
  
  * **Reken intensief werk belastingen uit te voeren op een speciale thread** : om Vergelijk bare redenen voor de vorige tip, worden bewerkingen, zoals complexe gegevens verwerking, het beste in een afzonderlijke thread geplaatst. Een aanvraag die gegevens ophaalt uit een ander gegevens archief (bijvoorbeeld als de thread gelijktijdig gebruikmaakt van Azure Cosmos DB-en Spark-gegevens opslag), kan de latentie verg root en wordt aanbevolen een extra thread te maken die op een reactie van het andere gegevens archief wacht.
  
    * De onderliggende netwerk-i/o in de Azure Cosmos DB async Java SDK v2 wordt beheerd door Netty. Zie deze [Tips voor het voor komen van coderings patronen die NETTY io-threads blok keren](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Gegevens modellering** : de SLA van Azure Cosmos DB veronderstelt dat de document grootte kleiner is dan 1 KB. Het optimaliseren van uw gegevens model en Program meren om te zorgen voor een kleinere document grootte leidt doorgaans tot minder latentie. Als u opslag ruimte en het ophalen van documenten hebt die groter zijn dan 1 KB, is de aanbevolen benadering voor documenten om te koppelen aan gegevens in Azure Blob Storage.

* **Parallelle query's voor gepartitioneerde verzamelingen afstemmen**

  Azure Cosmos DB asynchrone Java SDK v2 ondersteunt parallelle query's, waarmee u parallel een gepartitioneerde verzameling kunt uitvoeren. Zie [code voorbeelden](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) met betrekking tot het werken met de sdk's voor meer informatie. Parallelle query's zijn ontworpen om de latentie en door Voer van query's te verbeteren ten opzichte van hun serieel equivalent.

  * ***SetMaxDegreeOfParallelism afstemmen\:***
    
    Parallelle query's werken door meerdere partities parallel te doorzoeken. Gegevens uit een afzonderlijke gepartitioneerde verzameling worden echter serieel opgehaald ten opzichte van de query. Gebruik setMaxDegreeOfParallelism daarom om het aantal partities in te stellen dat de maximale kans heeft om de meest uitvoering van de query te bereiken, mits alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u setMaxDegreeOfParallelism gebruiken om een hoog nummer in te stellen en het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de maximale mate van parallelle uitvoering.

    Het is belang rijk te weten dat parallelle query's de beste voor delen opleveren als de gegevens gelijkmatig worden verdeeld over alle partities met betrekking tot de query. Als de gepartitioneerde verzameling zodanig is gepartitioneerd dat alle of een meerderheid van de gegevens die door een query zijn geretourneerd, in een paar partities is geconcentreerd (één partitie in het ergste geval), wordt de prestaties van de query door deze partities beïnvloed.

  * ***SetMaxBufferedItemCount afstemmen\:***
    
    Parallelle query is ontworpen om de resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Het vooraf ophalen helpt bij de algehele latentie verbetering van een query. setMaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Als u setMaxBufferedItemCount instelt op het verwachte aantal geretourneerde resultaten (of een hoger getal), kan de query het maximale voor deel van het vooraf ophalen van het bericht ontvangen.

    Het vooraf ophalen van werkt op dezelfde manier, onafhankelijk van de MaxDegreeOfParallelism en er is één buffer voor de gegevens van alle partities.

* **Uitstel implementeren met getRetryAfterInMilliseconds-intervallen**

  Tijdens prestatie tests moet u de belasting verg Roten tot een klein aantal aanvragen wordt beperkt. Als deze beperking is ingesteld, moet de client toepassing uitstel voor het interval voor nieuwe pogingen van de server. Door de uitstel te respecteren, zorgt u ervoor dat u de minimale hoeveelheid tijd die wacht tussen nieuwe pogingen.

* **Uw client opschalen-workload**

  Als u test met hoge doorvoer niveaus (>50.000 RU/s), kan de client toepassing het knel punt worden als gevolg van het uitvallen van het CPU-of netwerk gebruik. Als u dit punt bereikt, kunt u het Azure Cosmos DB-account nog verder pushen door uw client toepassingen op meerdere servers te schalen.

* **Adres Sering op basis van namen gebruiken**

  Gebruik op naam gebaseerde adres Sering, waarbij koppelingen de indeling hebben `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , in plaats van SelfLinks ( \_ Self), die de indeling hebben `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` om te voor komen dat ResourceIds worden opgehaald van alle resources die worden gebruikt om de koppeling te maken. Als deze resources opnieuw worden gemaakt (mogelijk met dezelfde naam), is het mogelijk dat deze niet meer in de cache worden opgeslagen.

* **De pagina grootte voor query's/feeds voor betere prestaties afstemmen**

  Bij het uitvoeren van een bulksgewijze Lees bewerking van documenten met behulp van de functie voor lees bewerkingen (bijvoorbeeld readDocuments) of bij het uitgeven van een SQL-query, worden de resultaten op een gesegmenteerde manier geretourneerd als de resultatenset te groot is. Standaard worden resultaten geretourneerd in delen van 100 items of 1 MB, waarbij de limiet eerst wordt bereikt.

  Om het aantal netwerk round trips te verminderen dat vereist is om alle toepasselijke resultaten op te halen, kunt u de pagina grootte verg Roten met de aanvraag header [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) tot 1000. In gevallen waarin u slechts enkele resultaten wilt weer geven, bijvoorbeeld als uw gebruikers interface of toepassings-API slechts tien resultaten per keer retourneert, kunt u ook de pagina grootte verkleinen tot 10 om de door Voer voor lees bewerkingen en query's te verminderen.

  U kunt ook de pagina grootte instellen met behulp van de methode setMaxItemCount.

* **De juiste planner gebruiken (Vermijd het stelen van Event-i/o-Netty-threads)**

  De Azure Cosmos DB asynchrone Java SDK v2 maakt gebruik van [Netty](https://netty.io/) voor niet-blokkerende io. De SDK gebruikt een vast aantal i/o-Netty voor gebeurtenissen uit de Event-lus (net zoveel CPU-kernen die uw computer heeft) voor het uitvoeren van i/o-bewerkingen. De waarneem bare geretourneerde API levert het resultaat op een van de Netty-threads van de gedeelde IO-gebeurtenis. Het is dus belang rijk om de Netty-threads van de gedeelde IO-gebeurtenis-lus niet te blok keren. Het uitvoeren van CPU-intensieve werk of het blok keren van een bewerking op de Netty thread voor de IO-gebeurtenis kan leiden tot deadlock of de SDK-door Voer aanzienlijk verminderen.

  Met de volgende code wordt bijvoorbeeld een CPU-intensieve werk uitgevoerd op de Event-lus IO Netty-thread:

  **Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Wanneer het resultaat wordt ontvangen als u CPU-intensief werk wilt uitvoeren, moet u dit voor komen door de Event loop IO Netty-thread. U kunt in plaats daarvan uw eigen planner opgeven om uw eigen thread te bieden voor het uitvoeren van uw werk.

  **Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Op basis van het type van uw werk moet u de juiste bestaande RxJava-planner gebruiken voor uw werk. Lees hier [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) .

  Raadpleeg de [github-pagina](https://github.com/Azure/azure-cosmosdb-java) voor Azure Cosmos DB ASYNC Java SDK v2 voor meer informatie.

* **Logboek registratie van Netty uitschakelen**

    Logboek registratie van de Netty-bibliotheek is intensieve en moet worden uitgeschakeld (de configuratie kan niet worden onderdrukt) om extra CPU-kosten te voor komen. Als u zich niet in de foutopsporingsmodus bevindt, schakelt u de logboek registratie van Netty uit. Als u dus log4j gebruikt om de extra CPU-kosten te verwijderen die zijn gemaakt door ``org.apache.log4j.Category.callAppenders()`` van Netty, voegt u de volgende regel toe aan de code basis:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Bron limiet voor open files van besturings systeem**
 
    Voor sommige Linux-systemen (zoals Red Hat) geldt een bovengrens voor het aantal geopende bestanden en dus het totale aantal verbindingen. Voer de volgende handelingen uit om de huidige limieten te bekijken:

    ```bash
    ulimit -a
    ```

    Het aantal geopende bestanden (geen bestand) moet groot genoeg zijn om voldoende ruimte te hebben voor de geconfigureerde grootte van de verbindings groep en andere geopende bestanden door het besturings systeem. Het kan worden gewijzigd om een grotere grootte van de verbindings groep toe te staan.

    Open het bestand limits. conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Voeg de volgende regels toe/wijzig deze:

    ```
    * - nofile 100000
    ```

## <a name="indexing-policy"></a>Indexeringsbeleid
 
* **Niet-gebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

    Met het indexerings beleid van Azure Cosmos DB kunt u opgeven welke document paden moeten worden opgenomen of uitgesloten van indexering door gebruik te maken van indexerings paden (setIncludedPaths en setExcludedPaths). Het gebruik van indexerings paden biedt verbeterde schrijf prestaties en een lagere index opslag voor scenario's waarin de query patronen vooraf bekend zijn, aangezien index kosten direct worden gecorreleerd aan het aantal unieke paden dat is geïndexeerd. De volgende code laat bijvoorbeeld zien hoe u een volledige sectie van de documenten (ook wel een substructuur genoemd) uitsluit van indexering met behulp van het Joker teken ' * '.

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Zie [Azure Cosmos DB Indexing policies](./index-policy.md)(Engelstalig) voor meer informatie.

## <a name="throughput"></a><a id="measure-rus"></a>Doorvoer

* **Meten en afstemmen voor lagere aanvraag eenheden/tweede gebruik**

    Azure Cosmos DB biedt een uitgebreide set database bewerkingen, waaronder relationele en hiërarchische query's met Udf's, opgeslagen procedures en triggers, die allemaal op de documenten in een database verzameling worden uitgevoerd. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van te denken over en het beheren van hardwarebronnen, kunt u een aanvraag eenheid (RU) beschouwen als een enkele maat eenheid voor de resources die nodig zijn om verschillende database bewerkingen uit te voeren en een toepassings aanvraag te onderhouden.

    De door Voer is ingericht op basis van het aantal ingestelde [aanvraag eenheden](request-units.md) voor elke container. Het verbruik van de aanvraag eenheid wordt geëvalueerd als een rente per seconde. Toepassingen die het aantal ingerichte aanvraag eenheden voor hun container overschrijden, zijn beperkt tot het aantal onder het ingerichte niveau voor de container daalt. Als voor uw toepassing een hogere door Voer is vereist, kunt u de door Voer verhogen door extra aanvraag eenheden in te richten.

    De complexiteit van een query is van invloed op het aantal aanvraag eenheden dat voor een bewerking wordt verbruikt. Het aantal predikaten, de aard van de predikaten, het aantal Udf's en de grootte van de bron gegevens sets beïnvloeden de kosten van de query bewerkingen.

    Als u de overhead van een wille keurige bewerking (maken, bijwerken of verwijderen) wilt meten, inspecteert u de [x-MS-Request-factuurkop](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) tekst om het aantal aanvraag eenheden te meten dat door deze bewerkingen wordt verbruikt. U kunt ook de equivalente eigenschap RequestCharge bekijken in ResourceResponse \<T> of FeedResponse \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Asynchrone Java SDK v2 (maven com. micro soft. Azure:: Azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    De aanvraag kosten die in deze header worden geretourneerd, zijn een fractie van uw ingerichte door voer. Als u bijvoorbeeld 2000 RU/s hebt ingericht, en als de voor gaande query 1000 1 KB-documenten retourneert, is de kosten van de bewerking 1000. Binnen één seconde voldoet de server slechts twee aanvragen voordat de volgende aanvragen worden beperkt. Zie [aanvraag eenheden](request-units.md) en de [reken eenheid voor aanvragen](https://www.documentdb.com/capacityplanner)voor meer informatie.

* **Aantal verwerkings frequenties/aanvragen afhandelen te groot**

    Wanneer een client de gereserveerde door Voer voor een account probeert te overschrijden, is er geen prestatie vermindering op de server en wordt er geen gebruik van de doorvoer capaciteit meer dan het gereserveerde niveau. De-server preventief de aanvraag met RequestRateTooLarge (HTTP-status code 429) beëindigen en retourneert de header [x-MS-after-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , waarmee de hoeveelheid tijd in milliseconden wordt aangegeven dat de gebruiker moet wachten voordat de aanvraag opnieuw wordt geprobeerd.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    De Sdk's ondervangen dit antwoord impliciet, respecteert de door de server opgegeven nieuwe poging na de header en voert de aanvraag opnieuw uit. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

    Als u meer dan één client cumulatief op dezelfde manier hebt uitgevoerd, is het standaard aantal nieuwe pogingen dat momenteel is ingesteld op 9 intern door de client mogelijk niet voldoende. in dit geval genereert de client een DocumentClientException met de status code 429 naar de toepassing. Het standaard aantal nieuwe pogingen kan worden gewijzigd met behulp van setRetryOptions in het Connection Policy-exemplaar. Standaard wordt de DocumentClientException met de status code 429 geretourneerd na een cumulatieve wacht tijd van 30 seconden als de aanvraag boven het aanvraag aantal blijft. Dit gebeurt zelfs wanneer het huidige aantal nieuwe pogingen kleiner is dan het maximum aantal nieuwe pogingen. Dit is de standaard waarde van 9 of een door de gebruiker gedefinieerd getal.

    Hoewel het gedrag voor automatische pogingen helpt om de flexibiliteit en bruikbaarheid voor de meeste toepassingen te verbeteren, is het mogelijk om conflicteert bij het uitvoeren van benchmarks voor prestaties, met name bij het meten van latentie. De door de client waargenomen latentie krijgt een waarschuwing als het experiment de server beperking bereikt en zorgt ervoor dat de client-SDK op de achtergrond opnieuw probeert. Om latentie pieken te voor komen tijdens prestatie experimenten, meet u de kosten die worden geretourneerd door elke bewerking en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraag tarief vallen. Zie [aanvraag eenheden](request-units.md)voor meer informatie.

* **Maak een ontwerp voor kleinere documenten voor een hogere doorvoer**

    De aanvraag kosten (de verwerkings kosten van aanvragen) van een bepaalde bewerking worden rechtstreeks gecorreleerd aan de grootte van het document. Bewerkingen voor grote documenten kosten meer dan bewerkingen voor kleine documenten.

## <a name="next-steps"></a>Volgende stappen

Zie [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal baarheid en hoge prestaties.