---
title: Tips voor betere prestaties van Azure Cosmos DB Java SDK v4
description: Meer informatie over client configuratie opties voor het verbeteren van de prestaties van Azure Cosmos Data Base voor Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: 8aad9df4720c833a74659b5cd36b7f5aafdf9b60
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631836"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Tips voor betere prestaties van Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](performance-tips-async-java.md)
> * [Sync Java-SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> De tips voor prestaties in dit artikel zijn alleen voor Azure Cosmos DB Java SDK v4. Raadpleeg de Azure Cosmos DB Java SDK v4- [release opmerkingen](sql-api-sdk-java-v4.md), de [maven-opslag plaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos)en Azure Cosmos DB Java SDK v4 [Troubleshooting Guide (Engelstalig](troubleshoot-java-sdk-v4-sql.md) ) voor meer informatie. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.

Azure Cosmos DB is een snelle en flexibele gedistribueerde data base die naadloos kan worden geschaald met gegarandeerde latentie en door voer. U hoeft geen grote architectuur wijzigingen aan te brengen of complexe code te schrijven om uw data base te schalen met Azure Cosmos DB. Omhoog en omlaag schalen is net zo eenvoudig als het maken van één API-aanroep of SDK-methode aanroep. Omdat Azure Cosmos DB echter via netwerk aanroepen wordt geopend, zijn er optimalisaties aan de client zijde die u kunt uitvoeren om de prestaties van de piek te bereiken wanneer u Azure Cosmos DB Java SDK v4 gebruikt.

Als u daarom vraagt hoe u de prestaties van mijn Data Base kunt verbeteren? Houd rekening met de volgende opties:

## <a name="networking"></a>Netwerken

* **Verbindings modus: directe modus gebruiken**
<a id="direct-connection"></a>
    
De standaard verbindings modus van Java SDK is direct. U kunt de verbindings modus configureren in de client builder met behulp van de methoden *directMode ()* of *gatewayMode ()* , zoals hieronder wordt weer gegeven. Voor het configureren van een beide modus met standaard instellingen roept u een van beide methoden aan zonder argumenten. Als dat niet het geval is, geeft u een instantie van de klasse configuratie-instellingen als het argument (*DirectConnectionConfig* voor *directMode ()*,  *GatewayConnectionConfig* voor *gatewayMode ()*.). Zie het artikel [connectiviteits modi](sql-sdk-connection-modes.md) voor meer informatie over de verschillende connectiviteits opties.
    
### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

--- 

De methode *directMode ()* heeft een extra onderdrukking om de volgende reden. Besturings vlak bewerkingen zoals data base en container ruw gebruiken *altijd* gateway modus. Wanneer de gebruiker de directe modus voor gegevensvlak bewerkingen heeft geconfigureerd, worden de standaard instellingen voor de gateway modus gebruikt. Dit is geschikt voor de meeste gebruikers. Gebruikers die de directe modus willen gebruiken voor gegevenslaag bewerkingen en tunability van de gateway modus-para meters van het besturings vlak, kunnen echter de volgende *directMode ()* overschrijven:

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

--- 

<a name="collocate-clients"></a>
* **Termijnen-clients in dezelfde Azure-regio voor prestaties**
<a id="same-region"></a>

Als dat mogelijk is, plaatst u toepassingen die Azure Cosmos DB aanroepen, in dezelfde regio als de Azure Cosmos-data base. Voor een benadering van een vergelijking worden de aanroepen naar Azure Cosmos DB binnen dezelfde regio binnen 1-2 MS uitgevoerd, maar de latentie tussen de West-en Oost kust van de Verenigde Staten is >50 MS. Deze latentie kan waarschijnlijk variëren van aanvraag om aan te vragen, afhankelijk van de route die door de aanvraag wordt door gegeven aan de limiet van de client naar de grens van het Azure-Data Center. De laagst mogelijke latentie wordt bereikt door ervoor te zorgen dat de aanroepende toepassing zich in dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eind punt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschik bare regio's.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Afbeelding van het verbindings beleid voor Azure Cosmos DB" border="false":::

Een app die samenwerkt met een multi-regio Azure Cosmos DB-account moet [Voorkeurs locaties](tutorial-global-distribution-sql-api.md#preferred-locations) configureren om ervoor te zorgen dat aanvragen naar een co-regio gaan.

* **Versneld netwerken op uw virtuele Azure-machine inschakelen voor een lagere latentie.**

U kunt het beste de instructies volgen voor het inschakelen van versnelde netwerken in uw [Windows (Klik voor instructies)](../virtual-network/create-vm-accelerated-networking-powershell.md) of [Linux (Klik voor instructies)](../virtual-network/create-vm-accelerated-networking-cli.md) Azure VM om de prestaties te maximaliseren.

Zonder versneld netwerken kan IO dat transities tussen uw virtuele Azure-machines en andere Azure-resources onnodig worden gerouteerd via een host en een virtuele switch tussen de virtuele machine en de bijbehorende netwerk kaart. Als u de host en de virtuele switch inline in de DataPath verhoogt, wordt de latentie en jitter in het communicatie kanaal ook niet alleen van de CPU-cycli van de virtuele machine. Met versneld netwerken worden de VM-interfaces direct met de NIC zonder tussen personen. alle details van het netwerk beleid die door de host en de virtuele switch worden verwerkt, worden nu verwerkt in hardware op de NIC. de host en de virtuele switch worden overgeslagen. Over het algemeen kunt u een lagere latentie en een hogere door Voer verwachten, evenals een *consistente* latentie en een kleiner CPU-gebruik wanneer u versneld netwerken inschakelt.

Beperkingen: versneld netwerken moeten worden ondersteund op het VM-besturings systeem en kunnen alleen worden ingeschakeld wanneer de virtuele machine wordt gestopt en de toewijzing ongedaan wordt gemaakt. De virtuele machine kan niet worden geïmplementeerd met Azure Resource Manager.

Raadpleeg de [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) -en [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) -instructies voor meer informatie.

## <a name="sdk-usage"></a>SDK-gebruik
* **De meest recente SDK installeren**

De Azure Cosmos DB Sdk's worden voortdurend verbeterd om de beste prestaties te leveren. Raadpleeg de [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) -pagina's om de meest recente SDK te bepalen en verbeteringen te bekijken.

* **Een singleton Azure Cosmos DB-client gebruiken voor de levens duur van uw toepassing**

Elk Azure Cosmos DB-client exemplaar is thread-safe en voert efficiënt beheer van de verbinding en de caching van adressen uit. Als u efficiënt verbindings beheer en betere prestaties wilt toestaan door de Azure Cosmos DB-client, wordt u aangeraden één exemplaar van de Azure Cosmos DB client per AppDomain te gebruiken voor de levens duur van de toepassing.

<a id="max-connection"></a>

* **Het laagste consistentie niveau gebruiken dat is vereist voor uw toepassing**

Wanneer u een *CosmosClient* maakt, wordt de standaard consistentie gebruikt als deze niet expliciet is ingesteld op *sessie*. Als *sessie* consistentie niet vereist is door uw toepassings logica, stelt u de *consistentie* in op *uiteindelijk*. Opmerking: het wordt aanbevolen om ten minste *sessie* consistentie te gebruiken in toepassingen die gebruikmaken van de Azure Cosmos DB Change feed-processor.

* **Asynchrone API gebruiken om de ingerichte door voer te maximaliseren**

Azure Cosmos DB Java SDK v4 bundelt twee Api's, synchronisatie en async. De async API implementeert ongeveer de SDK-functionaliteit, terwijl de API sync een thin wrapper is die het blok keren van aanroepen naar de async-API blokkeert. Dit is in tegens telling tot de oudere Azure Cosmos DB async Java SDK v2, die alleen van toepassing is op async, en op de oudere Azure Cosmos DB Java SDK v2, die alleen is gesynchroniseerd en die een volledig gescheiden implementatie heeft. 
    
De keuze van de API wordt bepaald tijdens het initialiseren van de client. een *CosmosAsyncClient* ondersteunt asynchrone API terwijl een *CosmosClient* Sync API ondersteunt. 
    
De async-API implementeert de niet-blokkerende IO en is de optimale keuze als uw doel is de door voer te maximaliseren bij het uitgeven van aanvragen voor Azure Cosmos DB. 
    
Het gebruik van Sync API kan de juiste keuze zijn als u een API wilt gebruiken die op de reactie op elke aanvraag wordt geblokkeerd, of als synchrone bewerking het dominante paradigma is in uw toepassing. U kunt bijvoorbeeld de API Sync wanneer u gegevens persistent wilt maken Azure Cosmos DB in een micro Services-toepassing, omdat de door Voer niet kritiek is.  
    
Houd er rekening mee dat synchronisatie-API-door Voer wordt gedegradeerd met toename van de reactie tijd van de aanvraag, terwijl de async API de mogelijkheden voor volledige band breedte van uw hardware kan verzadigen. 
    
Geografisch plaatsing kan u een grotere en consistente door Voer bieden bij het gebruik van de synchronisatie-API (Zie [termijnen-clients in dezelfde Azure-regio voor prestaties](#collocate-clients)), maar er wordt nog wel verwacht dat de door Voer van de asynchrone API wordt overschreden.

Sommige gebruikers zijn mogelijk ook niet bekend met [project reactor](https://projectreactor.io/), het reactieve streams-Framework dat wordt gebruikt voor het implementeren van Azure Cosmos DB Java SDK v4 async API. Als dit een probleem is, raden we u aan onze [hand leiding](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) voor het inleidende reactor patroon te lezen en vervolgens deze [Inleiding te bekijken om de programmering opnieuw actief](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) te maken. Als u Azure Cosmos DB al hebt gebruikt met een async-interface en de SDK die u hebt gebruikt, is Azure Cosmos DB async Java SDK v2, is het mogelijk dat u bekend bent met [reactivex](http://reactivex.io/) / [RxJava](https://github.com/ReactiveX/RxJava) , maar niet zeker weet wat er in Project reactor is gewijzigd. Bekijk in dat geval onze reactor-en RxJava- [gids](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) om bekendheid te krijgen.

De volgende code fragmenten laten zien hoe u uw Azure Cosmos DB-client voor respectievelijk de asynchrone API-of API-bewerking voor de synchronisatie kunt initialiseren:

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

 --- 

* **Connection Policy afstemmen**

Standaard worden de directe modus Cosmos DB aanvragen via TCP verzonden wanneer u Azure Cosmos DB Java SDK v4 gebruikt. In de interne directe modus wordt een speciale architectuur gebruikt om netwerk bronnen dynamisch te beheren en de beste prestaties te verkrijgen.

In Azure Cosmos DB Java SDK v4 is de directe modus de beste keuze om de database prestaties te verbeteren met de meeste werk belastingen. 

* ***Overzicht van de directe modus** _

:::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Afbeelding van de architectuur van de directe modus" border="false":::

De architectuur aan de client zijde die in directe modus wordt gebruikt, maakt voorspel bare netwerk gebruik en multiplex toegang tot Azure Cosmos DB replica's mogelijk. In het bovenstaande diagram ziet u hoe directe modus client aanvragen naar replica's in de Cosmos DB back-end stuurt. De architectuur van de directe modus wijst Maxi maal 10 _ *kanalen** toe aan de client zijde per database replica. Een kanaal is een TCP-verbinding voorafgegaan door een aanvraag buffer, die 30 aanvragen diep is. De kanalen die deel uitmaken van een replica, worden dynamisch toegewezen naar behoefte aan het **service-eind punt** van de replica. Wanneer de gebruiker een aanvraag uitgeeft in de directe modus, stuurt de **TransportClient** de aanvraag naar het juiste service-eind punt op basis van de partitie sleutel. De **aanvraag wachtrij** buffers aanvragen vóór het service-eind punt.

* ***Configuratie opties voor directe modus** _

Als niet-standaard gedrag van de directe modus gewenst is, maakt u een _DirectConnectionConfig *-exemplaar en past u de eigenschappen aan. vervolgens geeft u het aangepaste eigenschaps exemplaar door met de methode *directMode ()* in de Azure Cosmos DB client builder.

Deze configuratie-instellingen bepalen het gedrag van de onderliggende direct-modus architectuur die hierboven wordt beschreven.

Als eerste stap gebruikt u de volgende aanbevolen configuratie-instellingen hieronder. Deze *DirectConnectionConfig* opties zijn geavanceerde configuratie-instellingen die op onverwachte manieren van invloed kunnen zijn op de prestaties van de SDK. gebruikers wordt aangeraden ze te wijzigen, tenzij ze duidelijk weten wat de voor afwegingen zijn en wat absoluut nood zakelijk is. Neem contact op met het [Azure Cosmos DB team](mailto:CosmosDBPerformanceSupport@service.microsoft.com) als u problemen ondervindt in dit onderwerp.

| Configuratie optie       | Standaard   |
| :------------------:       | :-----:   |
| idleConnectionTimeout      | "PT0"     |
| maxConnectionsPerEndpoint  | "130"     |
| connectTimeout             | "PT5S"    |
| idleEndpointTimeout        | PT1H    |
| maxRequestsPerConnection   | 30      |

* **Parallelle query's voor gepartitioneerde verzamelingen afstemmen**

Azure Cosmos DB Java SDK v4 ondersteunt parallelle query's, waarmee u parallel een gepartitioneerde verzameling kunt uitvoeren. Zie [code voorbeelden](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) met betrekking tot het werken met Azure Cosmos DB Java SDK v4 voor meer informatie. Parallelle query's zijn ontworpen om de latentie en door Voer van query's te verbeteren ten opzichte van hun serieel equivalent.

* ***SetMaxDegreeOfParallelism \: afstemmen** _
    
Parallelle query's werken door meerdere partities parallel te doorzoeken. Gegevens uit een afzonderlijke gepartitioneerde verzameling worden echter serieel opgehaald ten opzichte van de query. Gebruik setMaxDegreeOfParallelism daarom om het aantal partities in te stellen dat de maximale kans heeft om de meest uitvoering van de query te bereiken, mits alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u setMaxDegreeOfParallelism gebruiken om een hoog nummer in te stellen en het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de maximale mate van parallelle uitvoering.

Het is belang rijk te weten dat parallelle query's de beste voor delen opleveren als de gegevens gelijkmatig worden verdeeld over alle partities met betrekking tot de query. Als de gepartitioneerde verzameling zodanig is gepartitioneerd dat alle of een meerderheid van de gegevens die door een query zijn geretourneerd, in een paar partities is geconcentreerd (één partitie in het ergste geval), wordt de prestaties van de query door deze partities beïnvloed.

_ ***Afstemmen \: setMaxBufferedItemCount** _
    
Parallelle query is ontworpen om de resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Het vooraf ophalen helpt bij de algehele latentie verbetering van een query. setMaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Als u setMaxBufferedItemCount instelt op het verwachte aantal geretourneerde resultaten (of een hoger getal), kan de query het maximale voor deel van het vooraf ophalen van het bericht ontvangen.

Het vooraf ophalen van werkt op dezelfde manier, onafhankelijk van de MaxDegreeOfParallelism en er is één buffer voor de gegevens van alle partities.

_ **Uw client opschalen-workload**

Als u test op hoog doorvoer niveau, kan de client toepassing het knel punt worden als gevolg van de machine die de CPU of het netwerk gebruik afgetopteert. Als u dit punt bereikt, kunt u het Azure Cosmos DB-account nog verder pushen door uw client toepassingen op meerdere servers te schalen.

Een goede vuist regel is niet groter dan >50% CPU-gebruik op een bepaalde server om latentie laag te blijven.

<a id="tune-page-size"></a>

* **De pagina grootte voor query's/feeds voor betere prestaties afstemmen**

Bij het uitvoeren van een bulksgewijze Lees bewerking van documenten met behulp van de functie voor lees bewerkingen (bijvoorbeeld *readItems*) of bij het uitgeven van een SQL-query (*queryItems*), worden de resultaten geretourneerd op een gesegmenteerde wijze als de resultatenset te groot is. Standaard worden resultaten geretourneerd in delen van 100 items of 1 MB, waarbij de limiet eerst wordt bereikt.

Stel dat uw toepassing een query uitgeeft aan Azure Cosmos DB en stel dat uw toepassing de volledige set query resultaten vereist om de taak te volt ooien. Om het aantal netwerk round trips te verminderen dat vereist is om alle toepasselijke resultaten op te halen, kunt u de pagina grootte verg Roten door het veld [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) -header aan te passen. 

* Voor query's met één partitie, het aanpassen van de waarde voor het veld [x-MS-maximum-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) in-1 (geen limiet voor de pagina grootte) zorgt voor een maximale latentie door het aantal query-antwoord pagina's te minimaliseren: de volledige resultatenset wordt op één pagina geretourneerd, of als de query langer duurt dan het time-outinterval, wordt de volledige resultatenset geretourneerd op het minimum aantal pagina's dat mogelijk is Hiermee wordt het aantal keer dat de aanvraag wordt gereisd, opgeslagen.
    
* Voor kruis partitie query's, stelt u het veld [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) in op-1 en verwijdert u de pagina grootte beperkende Risico's voor de SDK met onbeheerbare pagina formaten. In de cross-Partition-Case raden we u aan om de maximale pagina grootte te verhogen tot een grote, maar eindige waarde, bijvoorbeeld 1000, om de latentie te verminderen. 
    
In sommige toepassingen hebt u mogelijk niet de volledige set query resultaten nodig. In gevallen waarin u slechts enkele resultaten wilt weer geven, bijvoorbeeld als uw gebruikers interface of toepassings-API slechts 10 resultaten per keer retourneert, kunt u ook de pagina grootte verkleinen tot 10 om de door Voer voor lees bewerkingen en query's te verminderen.

U kunt ook het argument voorkeurs pagina grootte van de methode *byPage* instellen, in plaats van het veld rest header rechtstreeks te wijzigen. Houd er rekening mee dat [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) of het voorkeurs argument pagina grootte van *byPage* alleen een bovengrens voor de pagina grootte instellen, niet een absolute vereiste; Daarom ziet u Azure Cosmos DB retour pagina's die kleiner zijn dan de door u gewenste pagina grootte. 

* **De juiste planner gebruiken (Vermijd het stelen van Event-i/o-Netty-threads)**

De asynchrone functionaliteit van Azure Cosmos DB Java SDK is gebaseerd op [Netty](https://netty.io/) niet-blokkerende io. De SDK gebruikt een vast aantal i/o-Netty voor gebeurtenissen uit de Event-lus (net zoveel CPU-kernen die uw computer heeft) voor het uitvoeren van i/o-bewerkingen. De stroom die door de API wordt geretourneerd, geeft het resultaat van een van de gedeelde i/o-Netty-threads. Het is dus belang rijk om de Netty-threads van de gedeelde IO-gebeurtenis-lus niet te blok keren. Het uitvoeren van CPU-intensieve werk of het blok keren van een bewerking op de Netty thread voor de IO-gebeurtenis kan leiden tot deadlock of de SDK-door Voer aanzienlijk verminderen.

Met de volgende code wordt bijvoorbeeld een CPU-intensieve werk uitgevoerd op de Event-lus IO Netty-thread:
### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

Wanneer het resultaat wordt ontvangen als u CPU-intensief werk wilt uitvoeren, moet u dit voor komen door de Event loop IO Netty-thread. U kunt in plaats daarvan uw eigen planner opgeven om uw eigen thread te bieden voor het uitvoeren van uw werk, zoals hieronder wordt weer gegeven (vereist `import reactor.core.scheduler.Schedulers` ).

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

Op basis van het type van uw werk moet u de juiste bestaande reactor planner gebruiken voor uw werk. Lees hier [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html) .

Raadpleeg de [Cosmos DB Directory van de Azure SDK voor Java monorepo op github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)voor meer informatie over Azure Cosmos DB Java SDK v4.

* **Logboek registratie-instellingen in uw toepassing optimaliseren**

U kunt het beste om een aantal redenen logboek registratie toevoegen aan een thread die hoge door Voer van aanvragen genereert. Als uw doel is de ingerichte door Voer van een container volledig te verzadigen met aanvragen die door deze thread worden gegenereerd, kunnen logboek registratie optimalisaties de prestaties aanzienlijk verbeteren.

* ***Een asynchrone logboek registratie configureren** _

De latentie van een synchrone logboek registratie per definitie in de berekening van de totale latentie van de thread voor het genereren van een aanvraag. Een asynchrone logger, zoals [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) , wordt aangeraden om logboek registratie overhead van uw hoogwaardige toepassings threads te loskoppelen.

_ ***Logboek registratie van Netty uitschakelen** _

Logboek registratie van de Netty-bibliotheek is intensieve en moet worden uitgeschakeld (de configuratie kan niet worden onderdrukt) om extra CPU-kosten te voor komen. Als u zich niet in de foutopsporingsmodus bevindt, schakelt u de logboek registratie van Netty uit. Als u dus log4j gebruikt om de extra CPU-kosten te verwijderen die zijn gemaakt door ``org.apache.log4j.Category.callAppenders()`` van Netty, voegt u de volgende regel toe aan de code basis:

```java
org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
```

 **Bron limiet voor open files van het besturings systeem**
 
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

* **Partitie sleutel in punt schrijf bewerkingen opgeven**

Als u de prestaties van punt schrijf bewerkingen wilt verbeteren, geeft u de item partitie sleutel op in de API-aanroep punt schrijven, zoals hieronder wordt weer gegeven:

# <a name="async"></a>[Async](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

--- 

in plaats van alleen de instantie item op te geven, zoals hieronder wordt weer gegeven:

# <a name="async"></a>[Async](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

--- 

De laatste wordt ondersteund, maar voegt latentie toe aan uw toepassing. de SDK moet het item parseren en de partitie sleutel extra heren.

## <a name="indexing-policy"></a>Indexeringsbeleid
 
* **Niet-gebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

Met het indexerings beleid van Azure Cosmos DB kunt u opgeven welke document paden moeten worden opgenomen of uitgesloten van indexering door gebruik te maken van indexerings paden (setIncludedPaths en setExcludedPaths). Het gebruik van indexerings paden biedt verbeterde schrijf prestaties en een lagere index opslag voor scenario's waarin de query patronen vooraf bekend zijn, aangezien index kosten direct worden gecorreleerd aan het aantal unieke paden dat is geïndexeerd. De volgende code laat bijvoorbeeld zien hoe u volledige secties van de documenten (ook wel een substructuur genoemd) kunt opnemen in en uitsluiten van indexering met behulp van het Joker teken ' * '.

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK v4 (maven com. Azure:: Azure-Cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

Zie [Azure Cosmos DB Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

## <a name="throughput"></a>Doorvoer
<a id="measure-rus"></a>

* **Meten en afstemmen voor lagere aanvraag eenheden/tweede gebruik**

Azure Cosmos DB biedt een uitgebreide set database bewerkingen, waaronder relationele en hiërarchische query's met Udf's, opgeslagen procedures en triggers, die allemaal op de documenten in een database verzameling worden uitgevoerd. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van te denken over en het beheren van hardwarebronnen, kunt u een aanvraag eenheid (RU) beschouwen als een enkele maat eenheid voor de resources die nodig zijn om verschillende database bewerkingen uit te voeren en een toepassings aanvraag te onderhouden.

De door Voer is ingericht op basis van het aantal ingestelde [aanvraag eenheden](request-units.md) voor elke container. Het verbruik van de aanvraag eenheid wordt geëvalueerd als een rente per seconde. Toepassingen die het aantal ingerichte aanvraag eenheden voor hun container overschrijden, zijn beperkt tot het aantal onder het ingerichte niveau voor de container daalt. Als voor uw toepassing een hogere door Voer is vereist, kunt u de door Voer verhogen door extra aanvraag eenheden in te richten.

De complexiteit van een query is van invloed op het aantal aanvraag eenheden dat voor een bewerking wordt verbruikt. Het aantal predikaten, de aard van de predikaten, het aantal Udf's en de grootte van de bron gegevens sets beïnvloeden de kosten van de query bewerkingen.

Als u de overhead van een wille keurige bewerking (maken, bijwerken of verwijderen) wilt meten, inspecteert u de [x-MS-Request-factuurkop](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) tekst om het aantal aanvraag eenheden te meten dat door deze bewerkingen wordt verbruikt. U kunt ook de equivalente eigenschap RequestCharge bekijken in ResourceResponse \<T> of FeedResponse \<T> .

# <a name="async"></a>[Async](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

# <a name="sync"></a>[Synchroniseren](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

--- 

De aanvraag kosten die in deze header worden geretourneerd, zijn een fractie van uw ingerichte door voer. Als u bijvoorbeeld 2000 RU/s hebt ingericht, en als de voor gaande query 1000 1 KB-documenten retourneert, is de kosten van de bewerking 1000. Binnen één seconde voldoet de server slechts twee aanvragen voordat de volgende aanvragen worden beperkt. Zie [aanvraag eenheden](request-units.md) en de [reken eenheid voor aanvragen](https://www.documentdb.com/capacityplanner)voor meer informatie.

<a id="429"></a>
* **Aantal verwerkings frequenties/aanvragen afhandelen te groot**

Wanneer een client de gereserveerde door Voer voor een account probeert te overschrijden, is er geen prestatie vermindering op de server en wordt er geen gebruik van de doorvoer capaciteit meer dan het gereserveerde niveau. De-server preventief de aanvraag met RequestRateTooLarge (HTTP-status code 429) beëindigen en retourneert de header [x-MS-after-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , waarmee de hoeveelheid tijd in milliseconden wordt aangegeven dat de gebruiker moet wachten voordat de aanvraag opnieuw wordt geprobeerd.

```xml
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

De Sdk's ondervangen dit antwoord impliciet, respecteert de door de server opgegeven nieuwe poging na de header en voert de aanvraag opnieuw uit. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

Als u meer dan één client cumulatief op dezelfde manier hebt uitgevoerd, is het standaard aantal nieuwe pogingen dat momenteel is ingesteld op 9 intern door de client mogelijk niet voldoende. in dit geval genereert de client een *CosmosClientException* met de status code 429 naar de toepassing. Het standaard aantal nieuwe pogingen kan worden gewijzigd met behulp van setRetryOptions in het Connection Policy-exemplaar. Standaard wordt de *CosmosClientException* met de status code 429 geretourneerd na een cumulatieve wacht tijd van 30 seconden als de aanvraag boven het aanvraag aantal blijft. Dit gebeurt zelfs wanneer het huidige aantal nieuwe pogingen kleiner is dan het maximum aantal nieuwe pogingen. Dit is de standaard waarde van 9 of een door de gebruiker gedefinieerd getal.

Hoewel het gedrag voor automatische pogingen helpt om de flexibiliteit en bruikbaarheid voor de meeste toepassingen te verbeteren, is het mogelijk om conflicteert bij het uitvoeren van benchmarks voor prestaties, met name bij het meten van latentie. De door de client waargenomen latentie krijgt een waarschuwing als het experiment de server beperking bereikt en zorgt ervoor dat de client-SDK op de achtergrond opnieuw probeert. Om latentie pieken te voor komen tijdens prestatie experimenten, meet u de kosten die worden geretourneerd door elke bewerking en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraag tarief vallen. Zie [aanvraag eenheden](request-units.md)voor meer informatie.

* **Ontwerpen voor kleinere documenten voor een hogere door Voer**

De aanvraag kosten (de verwerkings kosten van aanvragen) van een bepaalde bewerking worden rechtstreeks gecorreleerd aan de grootte van het document. Bewerkingen voor grote documenten kosten meer dan bewerkingen voor kleine documenten. Stel in het ideale geval uw toepassing en werk stromen in om de grootte van uw object te verg Roten ~ 1 KB of een vergelijk bare volg orde of grootte. Voor wacht tijd gevoelige toepassingen moeten grote items worden vermeden: met multi-MB documenten wordt uw toepassing trager.

## <a name="next-steps"></a>Volgende stappen

Zie [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal baarheid en hoge prestaties.
