---
title: Tips voor betere prestaties voor Azure Cosmos DB Java-SDK v2 synchroniseren
description: Meer informatie over client configuratie opties voor het verbeteren van de prestaties van de Azure Cosmos-Data Base voor Sync Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 3eced4e727954906d8ff67e7afe2279100f8f1cc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349058"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Tips voor betere prestaties voor Azure Cosmos DB Java-SDK v2 synchroniseren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](performance-tips-async-java.md)
> * [Sync Java-SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Dit is *niet* de meest recente Java-SDK voor Azure Cosmos db. U moet uw project upgraden naar [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) en vervolgens de Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md)lezen. Volg de instructies in de hand leiding [migratie naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) en [reactor versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) om een upgrade uit te voeren. 
> 
> Deze tips voor prestaties zijn alleen voor Azure Cosmos DB Java SDK v2 alleen te synchroniseren. Bekijk de [opmerkingen](sql-api-sdk-java.md) bij de Azure Cosmos DB Sync Java SDK v2 en de [maven-opslag plaats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) voor meer informatie.
>

Azure Cosmos DB is een snelle en flexibele gedistribueerde data base die naadloos kan worden geschaald met gegarandeerde latentie en door voer. U hoeft geen grote architectuur wijzigingen aan te brengen of complexe code te schrijven om uw data base te schalen met Azure Cosmos DB. Omhoog en omlaag schalen is net zo eenvoudig als het maken van één API-aanroep. Zie voor meer informatie [over het inrichten van container doorvoer](how-to-provision-container-throughput.md) of [het inrichten van de doorvoer capaciteit van de data base](how-to-provision-database-throughput.md). Omdat Azure Cosmos DB echter via netwerk aanroepen wordt benaderd, zijn er optimalisaties aan de client zijde die u kunt uitvoeren om de prestaties te verbeteren wanneer u [Azure Cosmos DB Java SDK v2 synchroniseert](./sql-api-sdk-java.md).

Als u daarom vraagt hoe u de prestaties van mijn Data Base kunt verbeteren? Houd rekening met de volgende opties:

## <a name="networking"></a>Networking
<a id="direct-connection"></a>

1. **Verbindings modus: DirectHttps gebruiken**

    Hoe een client verbinding maakt met Azure Cosmos DB is belangrijk voor de prestaties, in het bijzonder wat betreft de latentie aan de clientzijde. Er is één sleutel configuratie-instelling beschikbaar voor het configureren van de client- [Connection Policy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) : de [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode).  De twee beschik bare ConnectionModes zijn:

   1. [Gateway (standaard)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      De gateway modus wordt op alle SDK-platforms ondersteund en is de geconfigureerde standaard waarde.  Als uw toepassing wordt uitgevoerd in een bedrijfs netwerk met strikte firewall beperkingen, is de gateway de beste keuze, omdat deze gebruikmaakt van de standaard HTTPS-poort en een enkel eind punt. De verhoudingen van de prestaties zijn echter wel dat de gateway modus een extra netwerk-hop omvat elke keer dat gegevens worden gelezen of geschreven naar Azure Cosmos DB. Als gevolg hiervan biedt de DirectHttps-modus betere prestaties vanwege minder netwerk-hops. 

      Het Azure Cosmos DB Sync Java SDK v2 gebruikt HTTPS als transport protocol. HTTPS maakt gebruik van TLS voor initiële verificatie en het versleutelen van verkeer. Wanneer u de Azure Cosmos DB Sync Java SDK v2 gebruikt, moet alleen HTTPS-poort 443 open zijn. 

      De ConnectionMode wordt geconfigureerd tijdens de constructie van het DocumentClient-exemplaar met de para meter Connection Policy. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Java SDK v2 synchroniseren (maven com. micro soft. Azure:: Azure-documentdb)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Diagram toont het Azure Cosmos D B-verbindings beleid." border="false":::

   <a id="same-region"></a>
2. **Termijnen-clients in dezelfde Azure-regio voor prestaties**

    Als dat mogelijk is, plaatst u toepassingen die Azure Cosmos DB aanroepen, in dezelfde regio als de Azure Cosmos-data base. Voor een benadering van een vergelijking worden de aanroepen naar Azure Cosmos DB binnen dezelfde regio binnen 1-2 MS uitgevoerd, maar de latentie tussen de West-en Oost kust van de Verenigde Staten is >50 MS. Deze latentie kan waarschijnlijk variëren van aanvraag om aan te vragen, afhankelijk van de route die door de aanvraag wordt door gegeven aan de limiet van de client naar de grens van het Azure-Data Center. De laagst mogelijke latentie wordt bereikt door ervoor te zorgen dat de aanroepende toepassing zich in dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eind punt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschik bare regio's.

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="In het diagram worden de aanvragen en antwoorden in twee regio's weer gegeven, waarbij computers verbinding maken met een Cosmos D B-account via mid-tier Services." border="false":::
   
## <a name="sdk-usage"></a>SDK-gebruik
1. **De meest recente SDK installeren**

    De Azure Cosmos DB Sdk's worden voortdurend verbeterd om de beste prestaties te leveren. Raadpleeg de [Azure Cosmos DB SDK](./sql-api-sdk-java.md) -pagina's om de meest recente SDK te bepalen en verbeteringen te bekijken.
2. **Een singleton Azure Cosmos DB-client gebruiken voor de levens duur van uw toepassing**

    Elk [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient) -exemplaar is thread-safe en voert efficiënt verbindings beheer en adres caching uit wanneer deze in de directe modus worden uitgevoerd. Om het beheer van efficiënte verbindingen en betere prestaties door DocumentClient mogelijk te maken, is het raadzaam één exemplaar van DocumentClient per AppDomain te gebruiken voor de levens duur van de toepassing.

   <a id="max-connection"></a>
3. **Verhoog MaxPoolSize per host bij gebruik van de gateway modus**

    Azure Cosmos DB aanvragen worden gedaan via HTTPS/REST wanneer de gateway modus wordt gebruikt, en deze worden onderhevig aan de standaard verbindings limiet per hostnaam of IP-adres. Mogelijk moet u de MaxPoolSize instellen op een hogere waarde (200-1000) zodat de client bibliotheek meerdere gelijktijdige verbindingen met Azure Cosmos DB kan gebruiken. In de Azure Cosmos DB Java SDK v2 te synchroniseren, is de standaard waarde voor [Connection Policy. getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) 100. Gebruik [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) om de waarde te wijzigen.

4. **Parallelle query's voor gepartitioneerde verzamelingen afstemmen**

    Azure Cosmos DB de Java SDK-versie 1.9.0 en hoger te synchroniseren ondersteunen parallelle query's, waarmee u een gepartitioneerde verzameling parallel kunt opvragen. Zie [code voorbeelden](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) met betrekking tot het werken met de sdk's voor meer informatie. Parallelle query's zijn ontworpen om de latentie en door Voer van query's te verbeteren ten opzichte van hun serieel equivalent.

    (a) **_afstemmen \: setMaxDegreeOfParallelism_* _ parallelle query's werken door meerdere partities parallel te doorzoeken. Gegevens uit een afzonderlijke gepartitioneerde verzameling worden echter serieel opgehaald ten opzichte van de query. Gebruik [setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) daarom om het aantal partities in te stellen dat de maximale kans heeft om de meest uitvoering van de query te bereiken, mits alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u setMaxDegreeOfParallelism gebruiken om een hoog nummer in te stellen en het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de maximale mate van parallelle uitvoering. 

    Het is belang rijk te weten dat parallelle query's de beste voor delen opleveren als de gegevens gelijkmatig worden verdeeld over alle partities met betrekking tot de query. Als de gepartitioneerde verzameling zodanig is gepartitioneerd dat alle of een meerderheid van de gegevens die door een query zijn geretourneerd, in een paar partities is geconcentreerd (één partitie in het ergste geval), wordt de prestaties van de query door deze partities beïnvloed.

    (b) de parallelle query _*_tuning setMaxBufferedItemCount \:_*_ is ontworpen om de resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Het vooraf ophalen helpt bij de algehele latentie verbetering van een query. setMaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Door [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) in te stellen op het verwachte aantal geretourneerde resultaten (of een hoger getal), kan de query Maxi maal voor deel ontvangen van vooraf ophalen.

    Het vooraf ophalen van werkt op dezelfde manier, onafhankelijk van de MaxDegreeOfParallelism en er is één buffer voor de gegevens van alle partities.  

5. _ *Implementeer uitstel met getRetryAfterInMilliseconds-intervallen**

    Tijdens prestatie tests moet u de belasting verg Roten tot een klein aantal aanvragen wordt beperkt. Als deze beperking is ingesteld, moet de client toepassing uitstel op een vertraging voor het door de server opgegeven interval voor nieuwe pogingen. Door de uitstel te respecteren, zorgt u ervoor dat u de minimale hoeveelheid tijd die wacht tussen nieuwe pogingen. Ondersteuning voor het beleid voor opnieuw proberen is opgenomen in versie 1.8.0 en hoger van de [Azure Cosmos DB Sync Java SDK](./sql-api-sdk-java.md). Zie [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds)voor meer informatie.

6. **Uw client opschalen-workload**

    Als u test met hoge doorvoer niveaus (>50.000 RU/s), kan de client toepassing het knel punt worden als gevolg van het uitvallen van het CPU-of netwerk gebruik. Als u dit punt bereikt, kunt u het Azure Cosmos DB-account nog verder pushen door uw client toepassingen op meerdere servers te schalen.

7. **Adres Sering op basis van namen gebruiken**

    Gebruik op naam gebaseerde adres Sering, waarbij koppelingen de indeling hebben `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , in plaats van SelfLinks ( \_ Self), die de indeling hebben `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` om te voor komen dat ResourceIds worden opgehaald van alle resources die worden gebruikt om de koppeling te maken. Als deze resources opnieuw worden gemaakt (mogelijk met dezelfde naam), is het mogelijk dat deze niet meer in de cache worden opgeslagen.

   <a id="tune-page-size"></a>
8. **De pagina grootte voor query's/feeds voor betere prestaties afstemmen**

    Bij het uitvoeren van een bulksgewijze Lees bewerking van documenten met behulp van de functie voor lees bewerkingen (bijvoorbeeld [readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) of bij het uitgeven van een SQL-query, worden de resultaten op een gesegmenteerde manier geretourneerd als de resultatenset te groot is. Standaard worden resultaten geretourneerd in delen van 100 items of 1 MB, waarbij de limiet eerst wordt bereikt.

    Om het aantal netwerk round trips te verminderen dat vereist is om alle toepasselijke resultaten op te halen, kunt u de pagina grootte verg Roten met de aanvraag header [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) tot 1000. In gevallen waarin u slechts enkele resultaten wilt weer geven, bijvoorbeeld als uw gebruikers interface of toepassings-API slechts tien resultaten per keer retourneert, kunt u ook de pagina grootte verkleinen tot 10 om de door Voer voor lees bewerkingen en query's te verminderen.

    U kunt ook de pagina grootte instellen met behulp van de [methode setPageSize](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Indexeringsbeleid
 
1. **Niet-gebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

    Met het indexerings beleid van Azure Cosmos DB kunt u opgeven welke document paden moeten worden opgenomen of uitgesloten van indexering door gebruik te maken van indexerings paden ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) en [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). Het gebruik van indexerings paden biedt verbeterde schrijf prestaties en een lagere index opslag voor scenario's waarin de query patronen vooraf bekend zijn, aangezien index kosten direct worden gecorreleerd aan het aantal unieke paden dat is geïndexeerd.  De volgende code laat bijvoorbeeld zien hoe u een volledige sectie (substructuur) van de documenten kunt uitsluiten van indexeren met het Joker teken ' * '.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Java SDK v2 synchroniseren (maven com. micro soft. Azure:: Azure-documentdb)

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

## <a name="throughput"></a>Doorvoer
<a id="measure-rus"></a>

1. **Meten en afstemmen voor lagere aanvraag eenheden/tweede gebruik**

    Azure Cosmos DB biedt een uitgebreide set database bewerkingen, waaronder relationele en hiërarchische query's met Udf's, opgeslagen procedures en triggers, die allemaal op de documenten in een database verzameling worden uitgevoerd. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van te denken over en het beheren van hardwarebronnen, kunt u een aanvraag eenheid (RU) beschouwen als een enkele maat eenheid voor de resources die nodig zijn om verschillende database bewerkingen uit te voeren en een toepassings aanvraag te onderhouden.

    De door Voer is ingericht op basis van het aantal ingestelde [aanvraag eenheden](request-units.md) voor elke container. Het verbruik van de aanvraag eenheid wordt geëvalueerd als een rente per seconde. Toepassingen die het aantal ingerichte aanvraag eenheden voor hun container overschrijden, zijn beperkt tot het aantal onder het ingerichte niveau voor de container daalt. Als voor uw toepassing een hogere door Voer is vereist, kunt u de door Voer verhogen door extra aanvraag eenheden in te richten. 

    De complexiteit van een query is van invloed op het aantal aanvraag eenheden dat voor een bewerking wordt verbruikt. Het aantal predikaten, de aard van de predikaten, het aantal Udf's en de grootte van de bron gegevens sets beïnvloeden de kosten van de query bewerkingen.

    Als u de overhead van een wille keurige bewerking (maken, bijwerken of verwijderen) wilt meten, inspecteert u de [x-MS-Request-factuurkop](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (of de equivalente eigenschap RequestCharge in [ResourceResponse \<T> ](/java/api/com.microsoft.azure.documentdb.resourceresponse) of [FeedResponse \<T> ](/java/api/com.microsoft.azure.documentdb.feedresponse) om het aantal aanvraag eenheden te meten dat door deze bewerkingen wordt verbruikt.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Java SDK v2 synchroniseren (maven com. micro soft. Azure:: Azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    De aanvraag kosten die in deze header worden geretourneerd, zijn een fractie van uw ingerichte door voer. Als u bijvoorbeeld 2000 RU/s hebt ingericht, en als de voor gaande query 1000 1 KB-documenten retourneert, is de kosten van de bewerking 1000. Binnen één seconde voldoet de server slechts twee aanvragen voordat de volgende aanvragen worden beperkt. Zie [aanvraag eenheden](request-units.md) en de [reken eenheid voor aanvragen](https://www.documentdb.com/capacityplanner)voor meer informatie.
   <a id="429"></a>
1. **Aantal verwerkings frequenties/aanvragen afhandelen te groot**

    Wanneer een client de gereserveerde door Voer voor een account probeert te overschrijden, is er geen prestatie vermindering op de server en wordt er geen gebruik van de doorvoer capaciteit meer dan het gereserveerde niveau. De-server preventief de aanvraag met RequestRateTooLarge (HTTP-status code 429) beëindigen en retourneert de header [x-MS-after-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , waarmee de hoeveelheid tijd in milliseconden wordt aangegeven dat de gebruiker moet wachten voordat de aanvraag opnieuw wordt geprobeerd.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    De Sdk's ondervangen dit antwoord impliciet, respecteert de door de server opgegeven nieuwe poging na de header en voert de aanvraag opnieuw uit. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

    Als u meer dan één client cumulatief op dezelfde manier hebt uitgevoerd, is het standaard aantal nieuwe pogingen dat momenteel is ingesteld op 9 intern door de client mogelijk niet voldoende. in dit geval genereert de client een [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) met de status code 429 naar de toepassing. Het standaard aantal nieuwe pogingen kan worden gewijzigd met behulp van [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) in het [Connection Policy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) -exemplaar. Standaard wordt de DocumentClientException met de status code 429 geretourneerd na een cumulatieve wacht tijd van 30 seconden als de aanvraag boven het aanvraag aantal blijft. Dit gebeurt zelfs wanneer het huidige aantal nieuwe pogingen kleiner is dan het maximum aantal nieuwe pogingen. Dit is de standaard waarde van 9 of een door de gebruiker gedefinieerd getal.

    Hoewel het gedrag voor automatische pogingen helpt om de flexibiliteit en bruikbaarheid voor de meeste toepassingen te verbeteren, is het mogelijk om conflicteert bij het uitvoeren van benchmarks voor prestaties, met name bij het meten van latentie.  De door de client waargenomen latentie krijgt een waarschuwing als het experiment de server beperking bereikt en zorgt ervoor dat de client-SDK op de achtergrond opnieuw probeert. Om latentie pieken te voor komen tijdens prestatie experimenten, meet u de kosten die worden geretourneerd door elke bewerking en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraag tarief vallen. Zie [aanvraag eenheden](request-units.md)voor meer informatie.
3. **Ontwerpen voor kleinere documenten voor een hogere door Voer**

    De aanvraag kosten (de verwerkings kosten van aanvragen) van een bepaalde bewerking worden rechtstreeks gecorreleerd aan de grootte van het document. Bewerkingen voor grote documenten kosten meer dan bewerkingen voor kleine documenten.

## <a name="next-steps"></a>Volgende stappen
Zie [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal baarheid en hoge prestaties.