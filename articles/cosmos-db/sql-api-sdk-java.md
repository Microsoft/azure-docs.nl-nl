---
title: 'Azure Cosmos DB: SQL Java API, SDK & resources'
description: Meer informatie over de SQL Java API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6644f495f28fb76503948c18354a5af0fcf832e5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364750"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK voor SQL API: opmerkingen bij de release en resources
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
> * [Spark 3 OLTP-connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

Dit is de oorspronkelijke Azure Cosmos DB Sync Java SDK v2 voor SQL API die ondersteuning biedt voor synchrone bewerkingen.

> [!IMPORTANT]  
> Dit is *niet de* nieuwste Java SDK voor Azure Cosmos DB. Overweeg het [Azure Cosmos DB Java SDK v4 te gebruiken](sql-api-sdk-java-v4.md) voor uw project. Volg de instructies in de handleiding Migreren naar Azure Cosmos DB [Java SDK v4](migrate-java-v4-sdk.md) en de [handleiding Reactor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) om een upgrade uit te voeren. 
>

| | Koppelingen |
|---|---|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-documentatie**|[Java API-referentiedocumentatie](/java/api/com.microsoft.azure.documentdb)|
|**Bijdragen aan de SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Aan de slag**|[Aan de slag met de Java SDK](./create-sql-api-java.md)|
|**Zelfstudie voor web-apps**|[Webtoepassingsontwikkeling met Azure Cosmos DB](sql-api-java-application.md)|
|**Minimaal ondersteunde runtime**|[Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Opmerkingen bij de release

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Er is een fout opgelost bij het verwerken van een query via service-interop.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* Ondersteuning toegevoegd voor het uitvoeren van query's op de wijzigingsfeed vanaf een bepaald tijdstip.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Probleem met primaire partitiecache opgelost voor documentCollection-query.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Ondersteuning toegevoegd voor aangepaste configuratie van 449-nieuwe poging.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Probleem met time-out van verbindingsgroep opgelost.
* Herstelt het vernieuwen van het auth-token bij interne nieuwe proberen.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* De juiste replicabeleidstag aan de clientzijde is bijgewerkt op databaseAccount en databaseAccountconfiguratie is gelezen uit de cache.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Fout bij ongeldig partitiesleutelbereik voorkomen als de gebruiker pkRangeId levert.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Geoptimaliseerde cachevernieuwingen voor partitiesleutelbereik.
* Herstelt het scenario waarbij de SDK geen partitie-splitshint van de server splitst en resulteert in een onjuiste vernieuwing van routeringscaches aan de clientzijde.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Geoptimaliseerde cachevernieuwingen voor verzamelingen.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Ondersteuning toegevoegd voor het ophalen van het interne uitzonderingsbericht uit de diagnostische tekenreeks van de aanvraag.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Versie-API geïntroduceerd in PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Er is afzonderlijke time-outondersteuning toegevoegd voor de directe modus.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Null-foutbericht van service gebruiken en documentclient-uitzondering produceren.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Verbetering van de socketverbinding, waardoor SoKeepAlive standaard true wordt toegevoegd.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Ondersteuning voor diagnostische tekenreeksen voor aanvragen toegevoegd.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Er is een fout opgelost in PartitionKey voor Hash V2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Ondersteuning toegevoegd voor samengestelde indexen.
* Er is een fout opgelost in Global Endpoint Manager om vernieuwing af te dwingen.
* Er is een fout opgelost voor upserts met voorwaarden vooraf in de directe modus.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Er is een fout in de adrescache van de gateway opgelost.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Schrijfondersteuning voor meerdere regio's toegevoegd voor de directe modus.
* Er is ondersteuning toegevoegd voor het verwerken van IOExceptions die zijn ontstaan als ServiceUnavailable-uitzonderingen, vanaf een proxy.
* Er is een fout opgelost in het beleid voor opnieuw proberen van eindpuntdetectie.
* Er is een fout opgelost om ervoor te zorgen dat er geen null-aanwijzer-uitzonderingen worden geretourneerd in BaseDatabaseAccountConfigurationProvider.
* Er is een fout opgelost om ervoor te zorgen dat QueryIterator geen null-waarden retourneert.
* Er is een fout opgelost om ervoor te zorgen dat grote PartitionKey is toegestaan

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Schrijfondersteuning voor meerdere regio's toegevoegd voor gatewaymodus.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Er is een fout opgelost in leespartitiesleutelbereiken voor een query.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Er is een fout opgelost bij het instellen van de grootte van de vervolg-tokenheader in de DirectHttps-modus.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Ondersteuning voor streaming-fail over toegevoegd.
* Ondersteuning toegevoegd voor aangepaste metagegevens.
* Verbeterde sessieverwerkingslogica.
* Er is een fout opgelost in de cache voor het bereik van partitiesleutels.
* Er is een NPE-fout opgelost in de directe modus.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Ondersteuning toegevoegd voor Unique Index.
* Ondersteuning toegevoegd voor het beperken van de grootte van het vervolg-token in feedopties.
* Er is een fout in Json Serialization (tijdstempel) opgelost.
* Er is een fout in Json Serialization (enum) opgelost.
* Afhankelijkheid van com.fasterxml.core.core:hole-databind is bijgewerkt naar 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Verbeterde verbindingsgroepering voor de directe modus.
* Verbeterde prefetch verbetering voor niet-orderby partitieoverschrijdende query.
* Verbeterde UUID-generatie.
* Verbeterde consistentielogica voor sessies.
* Ondersteuning toegevoegd voor multipolygon.
* Ondersteuning toegevoegd voor partitiesleutelbereikstatistieken voor verzameling.
* Er is een fout opgelost in ondersteuning voor meerdere regio's.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Verbeterde JSON-serialisatieprestaties.
* Voor deze SDK-versie is de nieuwste versie [van Azure Cosmos DB Emulator vereist.](https://aka.ms/cosmosdb-emulator)

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Interne wijzigingen voor microsoft-vriendenbibliotheken.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Er is een probleem opgelost bij het lezen van sleutelbereiken met één partitie.
* Er is een probleem opgelost bij het parseren van ResourceID dat van invloed is op de database met korte namen.
* Er is een probleem opgelost door partitiesleutelcoderen.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Kritieke foutfixes voor het aanvragen van verwerking tijdens partitiesplitsingen.
* Er is een probleem opgelost met de consistentieniveaus Strong en BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Ondersteuning toegevoegd voor een nieuw consistentieniveau met de naam ConsistentPrefix.
* Er is een fout opgelost in het lezen van verzamelingen in de sessiemodus.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Ondersteuning ingeschakeld voor gepartities met een lage ru/sec van 2500 RU/sec en schalen in stappen van 100 RU/sec.
* Er is een fout in de native assembly opgelost die een NullRef-uitzondering in sommige query's kan veroorzaken.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Er is een fout opgelost in de configuratie van de query-engine die uitzonderingen kan veroorzaken voor query's in de gatewaymodus.
* Er zijn enkele fouten in de sessiecontainer opgelost die direct na het maken van de verzameling een uitzondering 'Eigenaar-resource niet gevonden' kunnen veroorzaken voor aanvragen.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Ondersteuning toegevoegd voor aggregatiequery's (COUNT, MIN, MAX, SUM en AVG). Zie [Aggregatieondersteuning](sql-query-aggregate-functions.md).
* Ondersteuning toegevoegd voor de wijzigingsfeed.
* Ondersteuning toegevoegd voor verzamelen van quotumgegevens via RequestOptions.setPopulateQuotaInfo.
* Ondersteuning toegevoegd voor logboekregistratie van opgeslagen procedurescripts via RequestOptions.setScriptLoggingEnabled.
* Er is een fout opgelost waarbij de query in de DirectHttps-modus mogelijk niet meer reageert wanneer er vertragingsfouten optreden.
* Er is een fout opgelost in de sessieconsistentiemodus.
* Er is een fout opgelost die NullReferenceException in HttpContext kan veroorzaken wanneer de aanvraagsnelheid hoog is.
* Verbeterde prestaties van de DirectHttps-modus.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Eenvoudige proxyondersteuning op basis van client-exemplaren toegevoegd met de API ConnectionPolicy.setProxy().
* DocumentClient.close() API toegevoegd om het DocumentClient-exemplaar correct af te sluiten.
* Verbeterde queryprestaties in de modus voor directe connectiviteit door het queryplan af te leiden van de native assembly in plaats van de gateway.
* Stel FAIL_ON_UNKNOWN_PROPERTIES = false in, zodat gebruikers JsonIgnoreProperties niet hoeven te definiëren in hun POJO.
* Logboekregistratie ge herfactored om SLF4J te gebruiken.
* Er zijn enkele andere fouten in de consistentielezer opgelost.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Er is een fout in het verbindingsbeheer opgelost om verbindingslekken in de modus voor directe connectiviteit te voorkomen.
* Er is een fout in de TOP-query opgelost waarbij nullReference-uitzondering kan worden geretourneerd.
* Verbeterde prestaties door het aantal netwerkoproepen voor de interne caches te verminderen.
* Statuscode, ActivityID en Aanvraag-URI toegevoegd in DocumentClientException voor betere probleemoplossing.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Er is een probleem opgelost in het verbindingsbeheer voor stabiliteit.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Ondersteuning toegevoegd voor het consistentieniveau BoundedStaleness.
* Ondersteuning toegevoegd voor directe connectiviteit voor CRUD-bewerkingen voor gepartitiede verzamelingen.
* Er is een fout opgelost bij het uitvoeren van query's op een database met SQL.
* Er is een fout in de sessiecache opgelost waarbij het sessie-token mogelijk onjuist is ingesteld.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Ondersteuning toegevoegd voor parallelle query's voor kruislingse partities.
* Ondersteuning toegevoegd voor TOP/ORDER BY-query's voor gepartities.
* Ondersteuning toegevoegd voor sterke consistentie.
* Ondersteuning toegevoegd voor op naam gebaseerde aanvragen bij gebruik van directe connectiviteit.
* Opgelost om ActivityId consistent te maken voor alle nieuwe aanvragen.
* Er is een fout met betrekking tot de sessiecache opgelost bij het opnieuw maken van een verzameling met dezelfde naam.
* Polygon en LineString DataTypes toegevoegd tijdens het opgeven van het indexeringsbeleid voor verzamelingen voor ruimtelijke query's met geofencing.
* Problemen met Java Doc voor Java 1.8 opgelost.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Er is een fout opgelost in PartitionKeyDefinitionMap om verzamelingen met één partitie in de cache te cachen en geen extra aanvragen voor partitiesleutels op te halen.
* Er is een fout opgelost om het niet opnieuw te proberen wanneer een onjuiste partitiesleutelwaarde wordt opgegeven.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Ondersteuning toegevoegd voor databaseaccounts voor meerdere regio's.
* Ondersteuning toegevoegd voor automatische nieuwe pogingen voor beperkt aantal aanvragen met opties voor het aanpassen van het maximum aantal nieuwe pogingen en de maximale wachttijd voor nieuwe pogingen.  Zie RetryOptions en ConnectionPolicy.getRetryOptions().
* Aangepaste partitioneringscode op basis van IPartitionResolver afgeschaft. Gebruik gepartitiede verzamelingen voor hogere opslag en doorvoer.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Ondersteuning voor beleid voor opnieuw proberen toegevoegd voor snelheidsbeperking.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Time to Live-ondersteuning (TTL) toegevoegd voor documenten.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Gepartitiesteerde verzamelingen en](partitioning-overview.md) [door de gebruiker gedefinieerde prestatieniveaus geïmplementeerd.](performance-levels.md)

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Er is een fout opgelost in HashPartitionResolver voor het genereren van hash-waarden in little endian om consistent te zijn met andere SDK's.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Voeg Hash & Range-partitie resolvers toe om te helpen bij het sharden van toepassingen over meerdere partities.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementeert Upsert. Er zijn nieuwe upsertXXX-methoden toegevoegd ter ondersteuning van de upsert-functie.
* Implementeert routering op basis van id's. Geen openbare API-wijzigingen, alle wijzigingen intern.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Release overgeslagen om versienummer in overeenstemming met andere SDK's te brengen

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Ondersteunt georuimtelijke index
* Valideert de id-eigenschap voor alle resources. Id's voor resources mogen geen ?, /, #, tekens bevatten \, of eindigen met een spatie.
* Nieuwe header 'voortgang van indextransformatie' toegevoegd aan ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementeert V2-indexeringsbeleid

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Release- en pensioendatums

Microsoft zal ten minste **12 maanden** vóór de buitengebruikstelling van een SDK een melding doen, om de overgang naar een nieuwere/ondersteunde versie te versoepelen. Nieuwe functies en functionaliteit en optimalisaties worden alleen toegevoegd aan de huidige SDK. Daarom is het raadzaam om altijd zo vroeg mogelijk een upgrade uit te voeren naar de nieuwste SDK-versie.

> [!WARNING]
> Na 30 mei 2020 zal Azure Cosmos DB geen fouten meer oplossen, nieuwe functies toevoegen en ondersteuning bieden voor versie 1.x van de Azure Cosmos DB Java SDK voor SQL API. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanaf versie 1.x van de SDK nog steeds behandeld door de Azure Cosmos DB-service.
>
> Na 29 februari 2016 zal Azure Cosmos DB geen fouten meer oplossen, nieuwe functies toevoegen en ondersteuning bieden voor versie 0.x van de Azure Cosmos DB Java SDK voor SQL API. Als u liever geen upgrade wilt uitvoeren, worden aanvragen die zijn verzonden vanaf versie 0.x van de SDK nog steeds door de Azure Cosmos DB service.


| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [2.6.1](#2.6.1) |17 december 2020 |--- |
| [2.6.0](#2.6.0) |16 juli 2020 |--- |
| [2.5.1](#2.5.1) |3 juni 2020 |--- |
| [2.5.0](#2.5.0) |12 mei 2020 |--- |
| [2.4.7](#2.4.7) |20 februari 2020 |--- |
| [2.4.6](#2.4.6) |24 januari 2020 |--- |
| [2.4.5](#2.4.5) |10 november 2019 |--- |
| [2.4.4](#2.4.4) |24 oktober 2019 |--- |
| [2.4.2](#2.4.2) |26 sep 2019 |--- |
| [2.4.1](#2.4.1) |18 jul 2019 |--- |
| [2.4.0](#2.4.0) |4 mei 2019 |--- |
| [2.3.0](#2.3.0) |Apr 24, 2019 |--- |
| [2.2.3](#2.2.3) |Apr 16, 2019 |--- |
| [2.2.2](#2.2.2) |Apr 05, 2019 |--- |
| [2.2.0](#2.2.0) |27 maart 2019 |--- |
| [2.1.3](#2.1.3) |13 maart 2019 |--- |
| [2.1.2](#2.1.2) |09 maart 2019 |--- |
| [2.1.1](#2.1.1) |13 december 2018 |--- |
| [2.1.0](#2.1.0) |20 november 2018 |--- |
| [2.0.0](#2.0.0) |21 september 2018 |--- |
| [1.16.4](#1.16.4) |10 september 2018 |30 mei 2020 |
| [1.16.3](#1.16.3) |09 september 2018 |30 mei 2020 |
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
| [1.9.0](#1.9.0) |3 oktober 2016 |30 mei 2020 |
| [1.8.1](#1.8.1) |30 juni 2016 |30 mei 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 mei 2020 |
| [1.7.1](#1.7.1) |30 april 2016 |30 mei 2020 |
| [1.7.0](#1.7.0) |27 april 2016 |30 mei 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 mei 2020 |
| [1.5.1](#1.5.1) |31 december 2015 |30 mei 2020 |
| [1.5.0](#1.5.0) |4 december 2015 |30 mei 2020 |
| [1.4.0](#1.4.0) |5 oktober 2015 |30 mei 2020 |
| [1.3.0](#1.3.0) |05 oktober 2015 |30 mei 2020 |
| [1.2.0](#1.2.0) |5 augustus 2015 |30 mei 2020 |
| [1.1.0](#1.1.0) |9 juli 2015 |30 mei 2020 |
| 1.0.1 |12 mei 2015 |30 mei 2020 |
| [1.0.0](#1.0.0) |7 april 2015 |30 mei 2020 |
| 0.9.5-prelease |09 maart 2015 |29 februari 2016 |
| 0.9.4-prelease |17 februari 2015 |29 februari 2016 |
| 0.9.3-prelease |13 januari 2015 |29 februari 2016 |
| 0.9.2-prelease |19 december 2014 |29 februari 2016 |
| 0.9.1-prelease |19 december 2014 |29 februari 2016 |
| 0.9.0-prelease |10 december 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
