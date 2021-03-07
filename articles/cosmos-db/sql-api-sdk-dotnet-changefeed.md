---
title: .NET wijzigingenfeedverwerker-API van Azure Cosmos DB, opmerkingen bij de SDK-release
description: Meer informatie over de wijzigingenfeedverwerker-API en SDK, inclusief release-datums, buitengebruikstellingsdatums en wijzigingen die zijn aangebracht tussen elke versie van de .NET wijzigingenfeedverwerker-SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 43ad7a91719430798d93c4883027cb4c58fd3442
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423845"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET wijzigingenfeedverwerker-SDK: Downloaden en opmerkingen bij de release
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
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
> * [REST](/rest/api
> * [REST Resourceprovider](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK downloaden**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-documentatie**|[Documentatie voor de API-verwijzing van de wijzigingenfeedverwerker-bibliotheek](/dotnet/api/microsoft.azure.documents.changefeedprocessor)|
|**Aan de slag**|[Aan de slag met de .NET-SDK van de wijzigingenfeedverwerker](change-feed.md)|
|**Huidig ondersteund framework**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Als u een wijzigingenfeedprocessor gebruikt, raadpleegt u de meest recente versie 3.x van de [.NET-SDK](change-feed-processor.md), waarvan de wijzigingenfeed in de SDK is ingebouwd. 

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="v2-builds"></a>v2-builds

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Er is lease-archiefcompatibiliteit toegevoegd met [V3 SDK die dynamische migratiepaden mogelijk maakt. Een toepassing kan worden gemigreerd naar V3 SDK en terug worden gemigreerd naar de wijzigingenfeedverwerker-bibliotheek zonder dat er een status verloren gaat.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Er is een case gecorrigeerd waarbij sluitingsreden `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` werd verzonden naar `FeedProcessing.IChangeFeedObserver.CloseAsync` als de partitie niet kan worden gevonden of als de doelreplica niet up-to-date is met de leessessie. In deze cases worden nu sluitingsredenen `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` en `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` gebruikt.
* Er is een nieuwe sluitingsreden `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` toegevoegd die wordt verzonden om de waarnemer van de wijzigingsfeed te sluiten wanneer de doelreplica niet up-to-date is met de leessessie.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Er is een nieuwe methode `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` toegevoegd en een bijbehorende openbare interface `ICheckpointPartitionProcessorFactory`. Hiermee kan een implementatie van de interface `IPartitionProcessor` het ingebouwde mechanisme voor controlepunten gebruiken. De nieuwe factory is vergelijkbaar met de bestaande `IPartitionProcessorFactory`, behalve dat de methode `Create` ook de parameter `ILeaseCheckpointer` neemt.
* Slechts een van de twee methoden, `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` of `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`, kan worden gebruikt voor hetzelfde `ChangeFeedProcessorBuilder`-exemplaar.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Verbeteringen in stabiliteit en diagnose:
  * Er is ondersteuning toegevoegd te detecteren als het lezen van de wijzigingenfeed te lang duurt. Wanneer het langer duurt dan de waarde die is opgegeven door de eigenschap `ChangeFeedProcessorOptions.ChangeFeedTimeout`, worden de volgende stappen uitgevoerd:
    * De bewerking voor het lezen van de wijzigingenfeed op de problematische partitie wordt afgebroken.
    * Het exemplaar van de wijzigingenfeedverwerker beëindigt het eigendom van de problematische lease. De verwijderde lease wordt opgehaald tijdens de volgende stap voor het verkrijgen van leases, die wordt uitgevoerd door hetzelfde of een ander exemplaar van de wijzigingenfeedverwerker. Op deze manier wordt het lezen van de wijzigingenfeed opnieuw gestart.
    * Er wordt een probleem gerapporteerd aan de statusmonitor. De standaard statusmonitor verzendt alle gerapporteerde problemen naar het traceringslogboek.
  * Er is een nieuwe openbare eigenschap toegevoegd: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. De standaardwaarde van deze eigenschap is 10 minuten.
  * Er is een nieuwe openbare opsommingswaarde toegevoegd: `Monitoring.MonitoredOperation.ReadChangeFeed`. Wanneer de waarde van `HealthMonitoringRecord.Operation` is ingesteld op `Monitoring.MonitoredOperation.ReadChangeFeed`, wordt hiermee aangegeven dat het statusprobleem betrekking heeft op het lezen van de wijzigingenfeed.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Een verbeterde strategie voor belastingsverdeling voor het scenario waarbij het ophalen van alle leases langer duurt dan het verloopinterval van de lease, bijvoorbeeld door netwerkproblemen:
  * In dit scenario wordt het belastingsverdelingsalgoritme gebruikt om leases onterecht als verlopen te beschouwen, waardoor leases van actieve eigenaars worden gestolen. Dit kan onnodig herverdeling van veel leases tot gevolg hebben.
  * Dit probleem wordt opgelost in deze release door te voorkomen dat er bij een conflict een nieuwe poging wordt gedaan tijdens het ophalen van de verlopen lease die de eigenaar niet heeft gewijzigd en het ophalen van de verlopen lease uit te stellen naar de volgende iteratie van de belastingsverdeling.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Verbeterde verwerking van uitzonderingen voor waarnemers.
* Uitgebreidere informatie over waarnemersfouten:
  * Wanneer een waarnemer wordt gesloten vanwege een uitzondering die is opgetreden door de ProcessChangesAsync van de waarnemer, ontvangt de CloseAsync nu de reden dat de parameter is ingesteld op ChangeFeedObserverCloseReason.ObserverError.
  * Er zijn traceringen toegevoegd om fouten binnen de gebruikerscode in een waarnemer te identificeren.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Er is ondersteuning toegevoegd voor het verwerken van splitsingen in verzamelingen die gebruikmaken van de doorvoer van een gedeelde database.
  * Deze release corrigeert een probleem dat zich kan voordoen tijdens het splitsen in verzamelingen met behulp van de doorvoer van een gedeelde database, wanneer het resultaat wordt gesplitst in een partitie waarbij wordt herverdeeld met slechts één aangemaakt sleutelbereik van de onderliggende partitie, in plaats van twee. Als dit gebeurt, kan het zijn dat de wijzigingenfeedverwerker vastloopt tijdens het verwijderen van de lease voor het oude partitiesleutelbereik en geen nieuwe leases maakt. Het probleem is opgelost in deze release.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* De nieuwe eigenschap ChangeFeedProcessorOptions.StartContinuation is toegevoegd ter ondersteuning van het starten van een wijzigingenfeed vanuit de aanvraag van een voortzettingstoken. Dit wordt alleen gebruikt wanneer de leaseverzameling leeg is of als er voor een lease geen voortzettingstoken is ingesteld. Voor leases in een leaseverzameling waarvoor een voortzettingstoken is ingesteld, wordt het voortzettingstoken gebruikt en wordt ChangeFeedProcessorOptions.StartContinuation genegeerd.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Er is ondersteuning toegevoegd voor het gebruik van een aangepast archief om voortzettingstokens per partitie te behouden.
  * Zo kan een aangepast lease-archief bijvoorbeeld een Azure Cosmos DB-lease-verzameling zijn die op een aangepaste manier is gepartitioneerd.
  * Aangepaste lease-archieven kunnen het nieuwe uitbreidbaarheidspunt ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager) en de openbare interface ILeaseStoreManager gebruiken.
  * De ILeaseManager-interface is geherstructureerd in interfaces met meerdere rollen.
* Kleine wijziging die fouten veroorzaakt: uitbreidbaarheidspunt ChangeFeedProcessorBuilder.WithLeaseManager (ILeaseManager) is verwijderd, gebruik in plaats daarvan ChangeFeedProcessorBuilder.WithLeaseStoreManager (ILeaseStoreManager).

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Deze release corrigeert een probleem dat zich voordoet tijdens het verwerken van een splitsing in een bewaakte verzameling en het gebruik van een gepartitioneerde lease-verzameling. Bij het verwerken van een lease voor een gesplitste partitie, mag de lease die overeenkomt met die partitie, niet worden verwijderd. Het probleem is opgelost in deze release.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Schattingsberekening hersteld voor accounts met meerdere schrijfregio's en een nieuwe sessie-tokenindeling.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Er is ondersteuning toegevoegd voor gepartitioneerde lease-verzamelingen. De partitiesleutel moet worden gedefinieerd als /id.
* Kleine wijziging die fouten veroorzaakt: de methoden van de IChangeFeedDocumentClient-interface en de ChangeFeedDocumentClient-klasse zijn gewijzigd zodat deze de RequestOptions- en CancellationToken-parameters bevatten. IChangeFeedDocumentClient is een geavanceerd uitbreidbaarheidspunt waarmee u een aangepaste implementatie van de documentclient kunt leveren voor gebruik met een Change Feed Processor, bijvoorbeeld een DocumentClient voorzien en alle aanroepen naar deze server onderscheppen om extra tracering, foutafhandeling, enzovoort uit te voeren. Met deze update moet de code die IChangeFeedDocumentClient implementeert, worden gewijzigd zodat er nieuwe parameters in de implementatie worden opgenomen.
* Kleine diagnostische verbeteringen.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Er is een nieuwe API toegevoegd, taak&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator. GetEstimatedRemainingWorkPerPartitionAsync (). Deze kan worden gebruikt om de geschatte hoeveelheid werk voor elke partitie te verkrijgen.
* Ondersteunt Microsoft.Azure.DocumentDB SDK 2.0. Vereist Microsoft.Azure.DocumentDB 2.0 of hoger.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* De openbare eigenschap ChangeFeedEventHost.HostName is toegevoegd voor compatibiliteit met v1.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Er is een racevoorwaarde opgelost die plaatsvindt tijdens het splitsen van de partitie. De racevoorwaarde kan leiden tot het verkrijgen van een lease en deze onmiddellijk verliezen tijdens het splitsen van partities en leidt tot conflicten. Het probleem met de racevoorwaarde is opgelost met deze release.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3-prerelease
* De volgende problemen zijn verholpen:
  * Wanneer er een partitie wordt gesplitst, kan er dubbele verwerking worden uitgevoerd van documenten die zijn gewijzigd vóór de splitsing.
  * De GetEstimatedRemainingWork-API heeft 0 geretourneerd wanneer er geen leases in de lease-verzameling aanwezig waren.

* De volgende uitzonderingen worden openbaar gemaakt. Uitbreidingen die IPartitionProcessor implementeren, kunnen deze uitzonderingen genereren.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2-prerelease
* Kleine API-wijzigingen:
  * ChangeFeedProcessorOptions.IsAutoCheckpointEnabled dat als verouderd is gemarkeerd, is verwijderd.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1-prerelease
* Verbeteringen in stabiliteit:
  * Betere verwerking van de initialisatie van het lease-archief. Wanneer het lease-archief leeg is, kan slechts één exemplaar van de processor deze initialiseren, de anderen wachten.
  * Stabielere/efficiëntere leasevernieuwing/-release. Het vernieuwen en vrijgeven van een lease met één partitie is onafhankelijk van het vernieuwen van andere. In v1 werd dit voor alle partities opeenvolgend uitgevoerd.
* Nieuwe v2 API:
  * Opbouwfunctie-patroon voor flexibele constructie van de processor: de ChangeFeedProcessorBuilder-klasse.
    * Kan elke combinatie van parameters hanteren.
    * Kan DocumentClient-exemplaar voor bewaking en/of leaseverzameling hanteren (niet beschikbaar in v1).
  * IChangeFeedObserver.ProcessChangesAsync accepteert nu CancellationToken (voortzettingstoken).
  * IRemainingWorkEstimator - de schatting van het resterende werk kan los van de processor worden gebruikt.
  * Nieuwe uitbreidbaarheidspunten:
    * IPartitionLoadBalancingStrategy - voor aangepaste belastingsverdeling van partities tussen exemplaren van de processor.
    * ILease, ILeaseManager - voor aangepast lease-beheer.
    * IPartitionProcessor - voor aangepaste verwerkingswijzigingen op een partitie.
* Logboekregistratie - gebruikt [LibLog](https://github.com/damianh/LibLog)-bibliotheek.
* 100% compatibel met eerdere versies v1 API.
* Nieuwe codebasis.
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet.md) versies 1.21.1 en hoger.

### <a name="v1-builds"></a>v1-builds

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Meer logboekregistratie toegevoegd.
* Er is een DocumentClient-lek opgelost tijdens het meerder keren aanroepen van de schatting van het werk in behandeling.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Oplossingen voor de schatting van werk in behandeling.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Verbeteringen in stabiliteit.
  * Oplossing voor het verwerken van een probleem met geannuleerde taken dat kan leiden tot het stoppen van waarnemers op sommige partities.
* Ondersteuning voor het handmatig plaatsen van controlepunten.
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet.md) versies 1.21 en hoger.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Voegt ondersteuning toe voor .NET Standard 2.0. Het pakket ondersteunt nu `netstandard2.0` en `net451` framework-monikers.
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet.md) versies 1.17.0 en hoger.
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet-core.md) versies 1.5.1 en hoger.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Hiermee wordt een probleem opgelost met het berekenen van de schatting van de resterende hoeveelheid werk wanneer de wijzigingenfeed leeg was of omdat er geen werk in behandeling was.
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet.md) versies 1.13.2 en hoger.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Er is een methode toegevoegd voor het verkrijgen van een schatting van het resterende werk dat moet worden verwerkt in de wijzigingenfeed.
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet.md) versies 1.13.2 en hoger.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* GA SDK
* Compatibel met [SQL .NET SDK](sql-api-sdk-dotnet.md) versies 1.14.1 en hoger.

## <a name="release--retirement-dates"></a>Release- en buitengebruikstellingsdatums

Microsoft zal ten minste **12 maanden** vóór de buitengebruikstelling van een SDK een melding doen, om de overgang naar een nieuwere/ondersteunde versie te versoepelen. Nieuwe functies en functionaliteiten en optimaliseringen worden alleen toegevoegd aan de huidige SDK. Het wordt daarom aangeraden altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren.

> [!WARNING]
> Na 31 augustus 2022 worden er bij Azure Cosmos DB geen foutoplossingen meer uitgevoerd, geen nieuwe functies toegevoegd en geen ondersteuning meer geboden voor versie 1.x van de Azure Cosmos DB .NET of .NET Core-SDK voor SQL API. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanaf versie 1.x van de SDK nog steeds behandeld door de Azure Cosmos DB-service.

<br/>

| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11 augustus 2020 |--- |
| [2.3.1](#2.3.1) |30 juli 2020 |--- |
| [2.3.0](#2.3.0) |2 april 2020 |--- |
| [2.2.8](#2.2.8) |28 oktober 2019 |--- |
| [2.2.7](#2.2.7) |14 mei 2019 |--- |
| [2.2.6](#2.2.6) |29 januari 2019 |--- |
| [2.2.5](#2.2.5) |13 december 2018 |--- |
| [2.2.4](#2.2.4) |29 november 2018 |--- |
| [2.2.3](#2.2.3) |19 november 2018 |--- |
| [2.2.2](#2.2.2) |31 oktober 2018 |--- |
| [2.2.1](#2.2.1) |24 oktober 2018 |--- |
| [1.3.3](#1.3.3) |08 mei 2018 |--- |
| [1.3.2](#1.3.2) |18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 maart 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |29 augustus 2017 |--- |
| [1.1.0](#1.1.0) |13 augustus 2017 |--- |
| [1.0.0](#1.0.0) |07 juli 2017 |--- |

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook

Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
