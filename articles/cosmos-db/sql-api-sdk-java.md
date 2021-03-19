---
title: 'Azure Cosmos DB: SQL Java API, SDK & resources'
description: Meer informatie over de SQL Java API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 12/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f18458d0c2ce857ba71463a14844414e631a73ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595693"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK voor SQL API: release opmerkingen en bronnen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET wijzigingenfeed-SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resource provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

Dit is de oorspronkelijke Azure Cosmos DB Java SDK v2 voor de SQL-API die synchrone bewerkingen ondersteunt.

> [!IMPORTANT]  
> Dit is *niet* de meest recente Java-SDK voor Azure Cosmos db. Overweeg [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) te gebruiken voor uw project. Als u een upgrade wilt uitvoeren, volgt u de instructies in de hand leiding [Migrate to Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) and the [reactor VS RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) . 
>

| | Koppelingen |
|---|---|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-documentatie**|[Naslag documentatie voor Java API](/java/api/com.microsoft.azure.documentdb)|
|**Bijdragen aan SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Aan de slag**|[Aan de slag met de Java SDK](./create-sql-api-java.md)|
|**Zelf studie voor web-apps**|[Ontwikkeling van webtoepassingen met Azure Cosmos DB](sql-api-java-application.md)|
|**Minimale ondersteunde runtime**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Er is een fout opgelost bij het verwerken van een query via service Interop.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* Er is ondersteuning toegevoegd voor het uitvoeren van query's voor wijzigings invoer vanuit een bepaald tijdstip.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Hiermee wordt de primaire partitie cache probleem opgelost in de documentCollection-query.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Ondersteuning toegevoegd voor 449 aangepaste configuratie opnieuw proberen.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Oplossen van problemen met time-out van verbindings groep.
* Hiermee wordt het vernieuwen van het auth-token bij interne nieuwe pogingen opgelost.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* De juiste replica beleids label aan client zijde is bijgewerkt op databaseAccount en er zijn databaseAccount configuratie Lees bewerkingen van de cache gemaakt.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Nieuwe poging op ongeldige partitie sleutel bereik fout voor komen als de gebruiker pkRangeId levert.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Versleutelde cache sleutels voor het bereik van geoptimaliseerde partities.
* Hiermee wordt het scenario opgelost waarbij de SDK geen problemen met de splitsing van de partitie van de server oplevert en resulteert in onjuiste routerings caches voor client zijde vernieuwen.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Geoptimaliseerde verzamelings cache vernieuwt.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Er is ondersteuning toegevoegd om een uitzonderings bericht op te halen uit de teken reeks voor aanvraag diagnose.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Versie-API is geïntroduceerd op PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Er is afzonderlijke time-outondersteuning toegevoegd voor de directe modus.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Er wordt een null-fout bericht verbruikt van de service en de document-client uitzondering wordt geproduceerd.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Verbetering van socket verbinding, toevoegen SoKeepAlive standaard ingesteld op True.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Ondersteuning voor diagnostische teken reeksen voor aanvragen is toegevoegd.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Opgeloste fout in PartitionKey voor hash v2.

### <a name="212"></a><a name="2.1.2"></a>omschreven
* Er is ondersteuning toegevoegd voor samengestelde indexen.
* Er is een probleem opgelost in Global Endpoint Manager om het vernieuwen af te dwingen.
* Er is een fout opgelost voor upsert met voor waarden in de directe modus.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Er is een fout opgelost in de cache van de gateway.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Ondersteuning voor het schrijven van meerdere regio's is toegevoegd voor de directe modus.
* Er is ondersteuning toegevoegd voor het verwerken van IOExceptions die worden gegenereerd als ServiceUnavailable-uitzonde ringen, van een proxy.
* Er is een fout opgelost in het beleid voor opnieuw proberen van de eindpunt detectie.
* Er is een fout opgelost om ervoor te zorgen dat er geen null-pointer uitzonderingen worden gegenereerd in BaseDatabaseAccountConfigurationProvider.
* Er is een fout opgelost om ervoor te zorgen dat QueryIterator geen Null-waarden retourneert.
* Een bug opgelost om te garanderen dat grote PartitionKey is toegestaan

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Ondersteuning voor het schrijven van meerdere regio's is toegevoegd voor de gateway modus.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Er is een fout opgelost in de sleutel bereik lezen van de partitie voor een query.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Er is een fout opgelost bij het instellen van de header grootte van het vervolg token in de DirectHttps-modus.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* De ondersteuning voor streaming is toegevoegd.
* Er is ondersteuning toegevoegd voor aangepaste meta gegevens.
* Verbeterde logica voor sessie verwerking.
* Er is een fout opgelost in de cache van het partitie sleutel bereik.
* Er is een NPE-fout opgelost in de directe modus.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Er is ondersteuning toegevoegd voor de unieke index.
* Er is ondersteuning toegevoegd voor het beperken van de voortzettings token grootte in feed-opties.
* Er is een fout opgelost in JSON-serialisatie (tijds tempel).
* Er is een fout opgelost in JSON-serialisatie (enum).
* Afhankelijkheid van com. fasterxml. Jackson. core: Jackson-DataBind is bijgewerkt naar 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Verbeterde groepering van verbindingen voor directe modus.
* Verbeterde prefetch-verbetering voor niet-OrderBy Kruis partitie query's.
* Verbeterde UUID-generatie.
* Verbeterde logica voor consistentie van sessies.
* Er is ondersteuning toegevoegd voor multipolygoon.
* Er is ondersteuning toegevoegd voor de statistieken voor het partitie sleutel bereik voor de verzameling.
* Er is een fout opgelost in ondersteuning voor meerdere regio's.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Verbeterde prestaties van JSON-serialisatie.
* Voor deze SDK-versie is de nieuwste versie van [Azure Cosmos DB emulator](https://aka.ms/cosmosdb-emulator)vereist.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Interne wijzigingen voor micro soft-vrienden bibliotheken.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Er is een probleem opgelost bij het lezen van de sleutel reeksen met één partitie.
* Er is een probleem opgelost in het parseren van ResourceID dat invloed heeft op een Data Base met korte namen.
* Er is een probleem opgelost met de fout code ring van de partitie sleutel.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Kritieke fout oplossingen voor het aanvragen van verwerking tijdens partitie splitsingen.
* Er is een probleem met de sterke en BoundedStaleness consistentie niveaus opgelost.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Er is ondersteuning toegevoegd voor een nieuw consistentie niveau met de naam ConsistentPrefix.
* Er is een fout opgelost bij het lezen van de verzameling in de sessie modus.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Ondersteuning voor gepartitioneerde verzameling met een grootte van 2.500 RU/SEC en schalen in stappen van 100 RU/sec.
* Er is een fout opgelost in de systeem eigen assembly, waardoor er een NullRef-uitzonde ring kan optreden in sommige query's.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Er is een fout opgelost in de configuratie van de query-engine die uitzonde ringen kan veroorzaken voor query's in de modus gateway.
* Er zijn enkele fouten in de sessie container opgelost die ertoe kunnen leiden dat een ' eigenaar-resource niet kan worden gevonden ' voor aanvragen onmiddellijk nadat de verzameling is gemaakt.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Er is ondersteuning toegevoegd voor aggregatie query's (aantal, MIN, MAX, SUM en AVG). Zie [ondersteuning voor aggregatie](sql-query-aggregate-functions.md).
* Ondersteuning toegevoegd voor wijzigings invoer.
* Er is ondersteuning toegevoegd voor verzamelings quotum gegevens via RequestOptions. setPopulateQuotaInfo.
* Er is ondersteuning toegevoegd voor de logboek registratie van opgeslagen-procedure scripts via RequestOptions. setScriptLoggingEnabled.
* Er is een fout opgelost waarbij de query in de DirectHttps-modus niet meer reageert wanneer er vertragings fouten optreden.
* Er is een fout opgelost in de modus voor sessie consistentie.
* Er is een fout opgelost waardoor NullReferenceException in http context kan worden veroorzaakt wanneer het aanvraag aantal hoog is.
* Verbeterde prestaties van de DirectHttps-modus.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Er is ondersteuning voor een eenvoudige client instantie met de API Connection Policy. setProxy () toegevoegd.
* De API DocumentClient. Close () is toegevoegd om het DocumentClient-exemplaar correct af te sluiten.
* Verbeterde query prestaties in de modus voor directe connectiviteit door het query plan van de systeem eigen assembly af te leiden in plaats van de gateway.
* Stel FAIL_ON_UNKNOWN_PROPERTIES = False in zodat gebruikers geen JsonIgnoreProperties in hun POJO hoeven te definiëren.
* Herstructured logboek registratie voor het gebruik van SLF4J.
* Een aantal andere bugs in consistentie lezer opgelost.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Er is een fout opgelost in het verbindings beheer om verbindings lekken in de modus voor directe connectiviteit te voor komen.
* Er is een fout opgelost in de bovenste query waar deze NullReference-uitzonde ring kan genereren.
* Verbeterde prestaties door het aantal netwerk aanroepen voor de interne caches te verminderen.
* Status code, ActivityID en aanvraag-URI in DocumentClientException zijn toegevoegd voor betere probleem oplossing.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Er is een probleem opgelost in het verbindings beheer voor stabiliteit.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Er is ondersteuning toegevoegd voor het consistentie niveau van BoundedStaleness.
* Er is ondersteuning toegevoegd voor directe connectiviteit voor ruwe bewerkingen voor gepartitioneerde verzamelingen.
* Er is een fout opgelost bij het opvragen van een Data Base met SQL.
* Er is een fout opgelost in de sessie cache waarin het sessie token onjuist kan worden ingesteld.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Er is ondersteuning toegevoegd voor parallelle query's op meerdere partities.
* Er is ondersteuning toegevoegd voor TOP/ORDER BY query's voor gepartitioneerde verzamelingen.
* Er is ondersteuning toegevoegd voor sterke consistentie.
* Er is ondersteuning toegevoegd voor aanvragen op basis van een naam wanneer u directe connectiviteit gebruikt.
* Opgelost zodat ActivityId consistent blijft in alle nieuwe aanvragen.
* Er is een fout met betrekking tot de sessie cache opgelost tijdens het opnieuw maken van een verzameling met dezelfde naam.
* De gegevens typen veelhoek en Lines Tring zijn toegevoegd tijdens het opgeven van het indexerings beleid voor verzamelingen voor ruimtelijke query's met geoomheining.
* Opgeloste problemen met java doc voor Java 1,8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Er is een fout opgelost in PartitionKeyDefinitionMap om enkele partitie verzamelingen in de cache op te slaan en geen extra ophaal partitie sleutel aanvragen te maken.
* Er is een fout opgelost die niet opnieuw wordt uitgevoerd wanneer een onjuiste partitie sleutel waarde wordt gegeven.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* De ondersteuning voor meerdere regio's database accounts is toegevoegd.
* Er is ondersteuning toegevoegd voor automatische nieuwe poging op vertraagde aanvragen met opties voor het aanpassen van het maximale aantal nieuwe pogingen en de maximale wacht tijd voor opnieuw proberen.  Zie RetryOptions en Connection Policy. getRetryOptions ().
* Aangepaste partitie code op basis van IPartitionResolver is afgeschaft. Gebruik gepartitioneerde verzamelingen voor hogere opslag en door voer.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* De ondersteuning voor het beleid voor opnieuw proberen is toegevoegd voor frequentie limieten.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Toegevoegde time-to-Live (TTL)-ondersteuning voor documenten.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Geïmplementeerde [gepartitioneerde verzamelingen](partitioning-overview.md) en door de [gebruiker gedefinieerde prestatie niveaus](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Er is een fout opgelost in HashPartitionResolver om hash-waarden in Little-Endian te genereren zodat deze consistent zijn met andere Sdk's.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Voeg voor het toevoegen van & hashes voor het bereik van de sharding-toepassingen op meerdere partities hulp middelen toe.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementeer Upsert. Er zijn nieuwe upsertXXX-methoden toegevoegd ter ondersteuning van de functie Upsert.
* Implementeer route ring op basis van ID'S. Geen wijzigingen in de open bare API, alle wijzigingen intern.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Release is overgeslagen om het versie nummer in overeenstemming te brengen met andere Sdk's

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Ondersteunt georuimtelijke index
* Valideert de eigenschap ID voor alle resources. Id's voor resources mogen niet de tekens?,/, #, bevatten \, of eindigen met een spatie.
* Hiermee wordt de voortgang van de index transformatie van de nieuwe header toegevoegd aan ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementeert v2-indexerings beleid

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Release-en pensioen datums

Microsoft zal ten minste **12 maanden** vóór de buitengebruikstelling van een SDK een melding doen, om de overgang naar een nieuwere/ondersteunde versie te versoepelen. Nieuwe functies en functionaliteiten en optimalisaties worden alleen toegevoegd aan de huidige SDK, omdat het raadzaam is om altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren.

> [!WARNING]
> Na 30 mei 2020 worden er bij Azure Cosmos DB geen problemen meer opgelost, worden nieuwe functies toegevoegd en wordt ondersteuning geboden voor versie 1. x van de Azure Cosmos DB Java SDK voor SQL API. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanaf versie 1.x van de SDK nog steeds behandeld door de Azure Cosmos DB-service.
>
> Na 29 februari 2016 worden er bij Azure Cosmos DB geen problemen meer opgelost, worden nieuwe functies toegevoegd en wordt ondersteuning geboden voor versie 0. x van de Azure Cosmos DB Java SDK voor SQL API. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanaf versie 0. x van de SDK blijven geleverd door de Azure Cosmos DB-service.


| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [2.6.1](#2.6.1) |17 december 2020 |--- |
| [2.6.0](#2.6.0) |16 juli 2020 |--- |
| [2.5.1](#2.5.1) |03 juni 2020 |--- |
| [2.5.0](#2.5.0) |12 mei 2020 |--- |
| [2.4.7](#2.4.7) |20 februari 2020 |--- |
| [2.4.6](#2.4.6) |24 januari 2020 |--- |
| [2.4.5](#2.4.5) |10 november 2019 |--- |
| [2.4.4](#2.4.4) |24 oktober 2019 |--- |
| [2.4.2](#2.4.2) |26 sep, 2019 |--- |
| [2.4.1](#2.4.1) |Jul 18, 2019 |--- |
| [2.4.0](#2.4.0) |Mei 04, 2019 |--- |
| [2.3.0](#2.3.0) |Apr 24, 2019 |--- |
| [2.2.3](#2.2.3) |Apr 16, 2019 |--- |
| [2.2.2](#2.2.2) |Apr 05, 2019 |--- |
| [2.2.0](#2.2.0) |27 mrt, 2019 |--- |
| [2.1.3](#2.1.3) |13 maart 2019 |--- |
| [omschreven](#2.1.2) |9 maart 2019 |--- |
| [2.1.1](#2.1.1) |13 december 2018 |--- |
| [2.1.0](#2.1.0) |20 november 2018 |--- |
| [2.0.0](#2.0.0) |Sept 21, 2018 |--- |
| [1.16.4](#1.16.4) |Sept 10, 2018 |30 mei 2020 |
| [1.16.3](#1.16.3) |Sept 09, 2018 |30 mei 2020 |
| [1.16.2](#1.16.2) |29 juni 2018 |30 mei 2020 |
| [1.16.1](#1.16.1) |16 mei 2018 |30 mei 2020 |
| [1.16.0](#1.16.0) |15 maart 2018 |30 mei 2020 |
| [1.15.0](#1.15.0) |14 november 2017 |30 mei 2020 |
| [1.14.0](#1.14.0) |28 oktober 2017 |30 mei 2020 |
| [1.13.0](#1.13.0) |25 augustus 2017 |30 mei 2020 |
| [1.12.0](#1.12.0) |11 juli 2017 |30 mei 2020 |
| [1.11.0](#1.11.0) |10 mei 2017 |30 mei 2020 |
| [1.10.0](#1.10.0) |11 maart 2017 |30 mei 2020 |
| [1.9.6](#1.9.6) |21 februari 2017 |30 mei 2020 |
| [1.9.5](#1.9.5) |31 januari 2017 |30 mei 2020 |
| [1.9.4](#1.9.4) |24 november 2016 |30 mei 2020 |
| [1.9.3](#1.9.3) |30 oktober 2016 |30 mei 2020 |
| [1.9.2](#1.9.2) |28 oktober 2016 |30 mei 2020 |
| [1.9.1](#1.9.1) |26 oktober 2016 |30 mei 2020 |
| [1.9.0](#1.9.0) |03 oktober 2016 |30 mei 2020 |
| [1.8.1](#1.8.1) |30 juni 2016 |30 mei 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 mei 2020 |
| [1.7.1](#1.7.1) |30 april 2016 |30 mei 2020 |
| [1.7.0](#1.7.0) |27 april 2016 |30 mei 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 mei 2020 |
| [1.5.1](#1.5.1) |31 december 2015 |30 mei 2020 |
| [1.5.0](#1.5.0) |December 04, 2015 |30 mei 2020 |
| [1.4.0](#1.4.0) |05 oktober 2015 |30 mei 2020 |
| [1.3.0](#1.3.0) |05 oktober 2015 |30 mei 2020 |
| [1.2.0](#1.2.0) |05 augustus 2015 |30 mei 2020 |
| [1.1.0](#1.1.0) |9 juli 2015 |30 mei 2020 |
| 1.0.1 |12 mei 2015 |30 mei 2020 |
| [1.0.0](#1.0.0) |7 april 2015 |30 mei 2020 |
| 0.9.5-prelease |9 maart 2015 |29 februari 2016 |
| 0.9.4-prelease |17 februari 2015 |29 februari 2016 |
| 0.9.3-prelease |13 januari 2015 |29 februari 2016 |
| 0.9.2-prelease |19 december 2014 |29 februari 2016 |
| 0.9.1 tot en-prelease |19 december 2014 |29 februari 2016 |
| 0.9.0-prelease |10 december 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
