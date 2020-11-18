---
title: De verdeling van partities verdelen over meerdere exemplaren-Azure Event Hubs | Microsoft Docs
description: Hierin wordt beschreven hoe u de belasting van partities op meerdere exemplaren van uw toepassing kunt verdelen met behulp van een gebeurtenis processor en de Azure Event Hubs SDK.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 03aeebb376c74e62a1bd935ac1fec4f178b63f4f
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685134"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>De verdeling van partities verdelen over meerdere exemplaren van uw toepassing
U kunt de toepassing voor het verwerken van gebeurtenissen schalen door meerdere exemplaren van de toepassing uit te voeren en de belasting tussen zichzelf te verdelen. In de oudere versies heeft [EventProcessorHost](event-hubs-event-processor-host.md) u de mogelijkheid om de belasting te verdelen tussen meerdere exemplaren van uw programma en controlepunt gebeurtenissen tijdens ontvangst. In de nieuwere versies (5,0 en hoger), **EventProcessorClient** (.net en Java) of **EventHubConsumerClient** (python en Java script) kunt u hetzelfde doen. Het ontwikkelings model wordt eenvoudiger gemaakt met behulp van gebeurtenissen. U abonneert u op de gebeurtenissen waarin u bent geïnteresseerd door een gebeurtenis-handler te registreren. Als u de oude versie van de client bibliotheek gebruikt, raadpleegt u de volgende migratie handleidingen: [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)en [Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

In dit artikel wordt een voorbeeld scenario beschreven voor het gebruik van meerdere instanties voor het lezen van gebeurtenissen van een Event Hub. vervolgens krijgt u informatie over de functies van de client voor gebeurtenis verwerking. Hiermee kunt u gebeurtenissen van meerdere partities tegelijk ontvangen en taak verdeling met andere consumenten die gebruikmaken van dezelfde Event Hub en Consumer-groep.

> [!NOTE]
> De te schalen sleutel voor Event Hubs is het idee van gepartitioneerde gebruikers. In tegens telling tot het patroon van [concurrerende gebruikers](/previous-versions/msp-n-p/dn568101(v=pandp.10)) , maakt het gepartitioneerde consument patroon een hoge schaal door de knel punt conflicten te verwijderen en end-to-end-parallellisme te vereenvoudigen.

## <a name="example-scenario"></a>Voorbeeldscenario

Als voorbeeld scenario kunt u een bedrijf voor thuis beveiliging overwegen dat 100.000 huizen bewaakt. Elke minuut haalt gegevens op uit verschillende Sens oren, zoals een bewegings detector, een deur/venster open sensor, glas afbreek detector, enzovoort, geïnstalleerd in elk thuis netwerk. Het bedrijf biedt een website voor inwoners om de activiteit van hun huis in bijna realtime te bewaken.

Elke sensor pusht gegevens naar een Event Hub. De Event Hub is geconfigureerd met 16 partities. Op het verbruiks einde hebt u een mechanisme nodig dat deze gebeurtenissen kan lezen, consolideert (filter, aggregatie, enzovoort) en de aggregatie naar een opslag-Blob kan dumpen, die vervolgens wordt geprojecteerd op een gebruiks vriendelijke webpagina.

## <a name="write-the-consumer-application"></a>De consumenten toepassing schrijven

Bij het ontwerpen van de gebruiker in een gedistribueerde omgeving moet het scenario de volgende vereisten afhandelen:

1. **Schalen:** Maak meerdere consumenten, waarbij elke consument eigenaar is van een aantal Event Hubs partities.
2. **Taak verdeling:** De consumenten dynamisch te verhogen of te verlagen. Wanneer bijvoorbeeld een nieuw sensor type (bijvoorbeeld een kool monoxide-detector) wordt toegevoegd aan elk thuis, neemt het aantal gebeurtenissen toe. In dat geval verhoogt de operator (a humane) het aantal consumenten exemplaren. Vervolgens kan de groep consumenten het aantal partities waarover ze beschikken, opnieuw verdelen om de belasting te delen met de nieuw toegevoegde consumenten.
3. **Naadloos hervatten bij fouten:** Als een Consumer (**Consumer a**) niet kan worden uitgevoerd (bijvoorbeeld de virtuele machine die de consument als host heeft van een storing), kunnen andere gebruikers de partities ophalen die eigendom zijn van **Consumer a** en door gaan. Daarnaast moet het voortzettings punt, een *controle punt* of *Offset*, worden opgegeven op het exacte punt waar de **Consumer a** mislukt, of iets voor dat.
4. **Gebeurtenissen gebruiken:** Hoewel de voor gaande drie punten van toepassing zijn op het beheer van de gebruiker, moet er code zijn om de gebeurtenissen te gebruiken en iets nuttig te doen. U kunt deze bijvoorbeeld samen voegen en uploaden naar Blob Storage.

## <a name="event-processor-or-consumer-client"></a>Gebeurtenis processor of consumenten client

U hoeft niet uw eigen oplossing te bouwen om aan deze vereisten te voldoen. De Azure Event Hubs Sdk's bieden deze functionaliteit. In .NET-of Java-Sdk's gebruikt u een event processor-client (EventProcessorClient) en in Python-en Java script-Sdk's gebruikt u EventHubConsumerClient. In de oude versie van SDK was het event processor host (EventProcessorHost) die deze functies ondersteunt.

Voor het meren deel van de productie scenario's wordt u aangeraden de gebeurtenis verwerker-client te gebruiken voor het lezen en verwerken van gebeurtenissen. De processor-client is bedoeld om een robuuste ervaring te bieden voor het verwerken van gebeurtenissen voor alle partities van een Event Hub in een uitvoerings-en fout tolerante manier, terwijl u een manier hebt om de voortgang ervan te controlepunt. Event processor-clients kunnen samen werken in de context van een Consumer groep voor een bepaalde Event Hub. Clients beheren automatisch distributie en taak verdeling als instanties beschikbaar of niet beschikbaar zijn voor de groep.

## <a name="partition-ownership-tracking"></a>Het bijhouden van de eigendom van partities

Een gebeurtenis processor instantie is doorgaans eigenaar en verwerkt gebeurtenissen van een of meer partities. Het eigendom van partities wordt gelijkmatig verdeeld over alle actieve gebeurtenis processor instanties die zijn gekoppeld aan een combi natie van Event Hub en Consumer groep. 

Elke gebeurtenis processor krijgt een unieke id en eigendom van de claims van partities door een vermelding toe te voegen aan of bij te werken in een controlepunt opslag. Alle gebeurtenis verwerkers communiceren periodiek met deze Store om de eigen verwerkings status bij te werken en om meer te weten te komen over andere actieve instanties. Deze gegevens worden vervolgens gebruikt om de belasting te verdelen over de actieve processors. Nieuwe exemplaren kunnen worden toegevoegd aan de verwerkings groep om omhoog te schalen. Wanneer instanties uitvallen, hetzij als gevolg van fouten of als u omlaag wilt schalen, wordt het eigendom van de partitie op de juiste wijze overgedragen aan andere actieve processors.

De partitie van de eigendoms records in de controlepunt opslag houden de naam van Event Hubs naam ruimte, de Event Hub name, de gebruikers groep, de gebeurtenis processor-id (ook wel eigenaar genoemd), de partitie-ID en het tijdstip van de laatste wijziging.



| Event Hubs-naamruimte               | Event Hub-naam | **Consumenten groep** | Eigenaar                                | Partitie-id | Tijdstip laatst gewijzigd  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Elk gebeurtenis processor exemplaar verkrijgt het eigendom van een partitie en begint met de verwerking van de partitie van het laatste bekende [controle punt](# Checkpointing). Als een processor mislukt (VM wordt afgesloten), worden deze door andere instanties gedetecteerd door te kijken naar het tijdstip van de laatste wijziging. Andere exemplaren proberen eigenaar te worden van de partities die voorheen eigendom waren van het inactieve exemplaar, en het archief van het controle punt garandeert dat slechts één van de exemplaren slaagt bij het claimen van het eigendom van een partitie. Op een bepaald moment is er dus Maxi maal één processor die gebeurtenissen van een partitie ontvangt.

## <a name="receive-messages"></a>Berichten ontvangen

Wanneer u een gebeurtenis processor maakt, geeft u de functies op waarmee gebeurtenissen en fouten worden verwerkt. Elke aanroep van de functie waarmee gebeurtenissen worden verwerkt, levert één gebeurtenis op uit een specifieke partitie. Het is uw verantwoordelijkheid om deze gebeurtenis af te handelen. Als u ervoor wilt zorgen dat de Consumer elk bericht ten minste één keer verwerkt, moet u uw eigen code schrijven met een nieuwe poging logica. Maar wees voorzichtig met verontreinigde berichten.

We raden u aan om dingen relatief snel te doen. Dat wil zeggen, zo weinig mogelijk verwerking. Als u naar opslag moet schrijven en een route ring wilt uitvoeren, is het beter om twee consumenten groepen te gebruiken en twee gebeurtenis processors te hebben.

## <a name="checkpointing"></a>Controlepunten maken

*Controle punten* is een proces waarbij een gebeurtenis processor de positie van de laatste uitgevoerde gebeurtenis binnen een partitie markeert of doorvoert. Het markeren van een controle punt wordt doorgaans uitgevoerd binnen de functie waarmee de gebeurtenissen worden verwerkt en plaatsvindt per partitie binnen een Consumer groep. 

Als een gebeurtenis processor de verbinding met een partitie verbreekt, kan een ander exemplaar de verwerking van de partitie op het controle punt hervatten dat eerder is vastgelegd door de laatste processor van die partitie in die Consumer groep. Wanneer de processor verbinding maakt, wordt de offset door gegeven aan de Event Hub om de locatie op te geven waar u wilt beginnen met lezen. Op deze manier kunt u controle punten gebruiken om gebeurtenissen te markeren als ' voltooid ' door downstream-toepassingen en om tolerantie te bieden wanneer een gebeurtenis processor uitvalt. Het is mogelijk om terug te gaan naar oudere gegevens door een lagere verschuiving van dit controle punt te specificeren. 

Wanneer het controle punt wordt uitgevoerd om een gebeurtenis te markeren als verwerkt, wordt een vermelding in het opslag punt archief toegevoegd of bijgewerkt met de offset en het Volg nummer van de gebeurtenis. Gebruikers moeten bepalen hoe vaak het controle punt moet worden bijgewerkt. Het bijwerken na elke geslaagde gebeurtenis kan gevolgen hebben voor prestaties en kosten omdat hiermee een schrijf bewerking wordt geactiveerd voor de onderliggende controlepunt opslag. Het is ook mogelijk om een controle punt te maken voor elke gebeurtenis, een bericht in de wachtrij waarin een Service Bus wachtrij een betere optie is dan een Event Hub. Het idee achter Event Hubs is dat u ten minste één keer op grote schaal krijgt. Door uw downstream-systemen idempotent te maken, is het eenvoudig om fouten te herstellen of opnieuw op te starten. Dit leidt ertoe dat dezelfde gebeurtenissen meerdere keren worden ontvangen.

> [!NOTE]
> Als u Azure Blob Storage gebruikt als controlepunt opslag in een omgeving die ondersteuning biedt voor een andere versie van de Storage BLOB SDK dan die welke meestal beschikbaar zijn op Azure, moet u code gebruiken om de API-versie van de opslag service te wijzigen in de specifieke versie die wordt ondersteund door die omgeving. Als u bijvoorbeeld [Event hubs uitvoert op een Azure stack hub versie 2002](/azure-stack/user/event-hubs-overview), is de hoogste beschik bare versie van de opslag service versie 2017-11-09. In dit geval moet u code gebruiken om de API-versie van de Storage-service te richten op 2017-11-09. Zie voor een voor beeld van het richten op een specifieke opslag-API-versie de volgende voor beelden op GitHub: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) of  [type script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Thread veiligheid en processor instanties

De functie waarmee de gebeurtenissen worden verwerkt, wordt standaard opeenvolgend aangeroepen voor een bepaalde partitie. Volgende gebeurtenissen en aanroepen van deze functie van dezelfde partitie wachtrij worden achter de schermen geplaatst, terwijl de gebeurtenis pomp blijft draaien op de achtergrond van andere threads. Gebeurtenissen van verschillende partities kunnen gelijktijdig worden verwerkt en alle gedeelde statussen die worden geopend via partities, moeten worden gesynchroniseerd.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende Quick Start:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
