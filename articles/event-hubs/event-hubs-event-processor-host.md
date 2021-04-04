---
title: Gebeurtenissen ontvangen met Event processor host-Azure Event Hubs | Microsoft Docs
description: In dit artikel wordt de Event processor host in azure Event Hubs beschreven, waarmee u het beheer van controle punten, leasing en het lezen van gebeurtenissen met een parallelle activiteit vereenvoudigt.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: de5d8f0f8bf9f64a473b18a50434cac83e8e38c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622059"
---
# <a name="event-processor-host"></a>Gebeurtenisprocessorhost
> [!NOTE]
> Dit artikel is van toepassing op de oude versie van Azure Event Hubs SDK. Zie [Balancing Partition load over meerdere exemplaren van uw toepassing](event-processor-balance-partition-load.md)voor de huidige versie van de SDK. Zie deze migratie handleidingen voor meer informatie over het migreren van uw code naar de nieuwere versie van de SDK. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Azure Event Hubs is een krachtige telemetrie-opname service die kan worden gebruikt voor het streamen van miljoenen gebeurtenissen tegen lage kosten. In dit artikel wordt beschreven hoe u opgenomen gebeurtenissen kunt gebruiken met behulp van de *Event processor host* (EPH). een intelligente Consumer-agent die het beheer van de controle punten, leasing en parallelle gebeurtenis lezers vereenvoudigt.  

De te schalen sleutel voor Event Hubs is het idee van gepartitioneerde gebruikers. In tegens telling tot het patroon van [concurrerende gebruikers](/previous-versions/msp-n-p/dn568101(v=pandp.10)) , maakt het gepartitioneerde consument patroon een hoge schaal door de knel punt conflicten te verwijderen en end-to-end-parallellisme te vereenvoudigen.

## <a name="home-security-scenario"></a>Home Security-scenario

Als voorbeeld scenario kunt u een bedrijf voor thuis beveiliging overwegen dat 100.000 huizen bewaakt. Elke minuut haalt gegevens op uit verschillende Sens oren, zoals een bewegings detector, een deur/venster open sensor, glas plaat detector, enz., geïnstalleerd in elk thuis netwerk. Het bedrijf biedt een website voor inwoners om de activiteit van hun huis in bijna realtime te bewaken.

Elke sensor pusht gegevens naar een Event Hub. De Event Hub is geconfigureerd met 16 partities. Op het verbruiks einde hebt u een mechanisme nodig dat deze gebeurtenissen kan lezen, consolideert (filter, aggregatie, enzovoort) en de aggregatie naar een opslag-Blob kan dumpen, die vervolgens wordt geprojecteerd op een gebruiks vriendelijke webpagina.

## <a name="write-the-consumer-application"></a>De consumenten toepassing schrijven

Bij het ontwerpen van de gebruiker in een gedistribueerde omgeving moet het scenario de volgende vereisten afhandelen:

1. **Schalen:** Maak meerdere consumenten, waarbij elke consument eigenaar is van een aantal Event Hubs partities.
2. **Taak verdeling:** De consumenten dynamisch te verhogen of te verlagen. Wanneer bijvoorbeeld een nieuw sensor type (bijvoorbeeld een kool monoxide-detector) wordt toegevoegd aan elk thuis, neemt het aantal gebeurtenissen toe. In dat geval verhoogt de operator (a humane) het aantal consumenten exemplaren. Vervolgens kan de groep consumenten het aantal partities waarover ze beschikken, opnieuw verdelen om de belasting te delen met de nieuw toegevoegde consumenten.
3. **Naadloos hervatten bij fouten:** Als een Consumer (**Consumer a**) uitvalt (bijvoorbeeld als de virtuele machine die de consument als host voor de klant heeft gevonden, plotseling vastloopt), moeten andere gebruikers de partities kunnen ophalen die eigendom zijn van de **consument a** en door gaan. Daarnaast moet het voortzettings punt, een *controle punt* of *Offset*, worden opgegeven op het exacte punt waar de **Consumer a** mislukt, of iets voor dat.
4. **Gebeurtenissen gebruiken:** Hoewel de voor gaande drie punten van toepassing zijn op het beheer van de gebruiker, moet er code zijn om de gebeurtenissen te gebruiken en iets nuttig te doen. u kunt deze bijvoorbeeld samen voegen en uploaden naar Blob Storage.

Event Hubs biedt deze functionaliteit in plaats van uw eigen oplossing te maken met behulp van de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -interface en de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -klasse.

## <a name="ieventprocessor-interface"></a>IEventProcessor-interface

Voor de eerste keer gebruiken toepassingen de  [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -interface, die vier methoden heeft: [openasync, CloseAsync, ProcessErrorAsync en ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Deze interface bevat de werkelijke code voor het gebruiken van de gebeurtenissen die Event Hubs verzendt. De volgende code toont een eenvoudige implementatie:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Vervolgens instantiërt u een [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar. Afhankelijk van de overbelasting, worden de volgende para meters gebruikt bij het maken van het [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar in de constructor:

- **hostnaam:** de naam van elke consument instantie. Elk exemplaar van **EventProcessorHost** moet een unieke waarde voor deze variabele hebben binnen een Consumer groep. Zorg er dus voor dat u deze waarde niet vast kunt coderen.
- **eventHubPath:** De naam van de Event Hub.
- **consumerGroupName:** Event Hubs gebruikt **$default** als de naam van de standaard Consumer groep, maar het is een goed idee om een Consumer groep te maken voor uw specifieke verwerkings aspect.
- **eventHubConnectionString:** Het connection string naar de Event Hub, dat kan worden opgehaald uit de Azure Portal. Deze connection string moet **Luister** machtigingen hebben voor de Event hub.
- **storageConnectionString:** Het opslag account dat wordt gebruikt voor intern resource beheer.

Tot slot registreren consumenten het [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar met de Event hubs-service. Bij het registreren van een gebeurtenis processor klasse met een instantie van EventProcessorHost wordt de verwerking van gebeurtenissen gestart. Registratie geeft de Event Hubs-service de opdracht te verwachten dat gebeurtenissen van sommige van de partities door de Consumer-app worden gebruikt en dat de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementatie code moet worden aangeroepen wanneer de gebeurtenissen worden gepusht. 

> [!NOTE]
> De consumerGroupName is hoofdletter gevoelig.  Wijzigingen in de consumerGroupName kunnen leiden tot het lezen van alle partities vanaf het begin van de stroom.

### <a name="example"></a>Voorbeeld

Stel dat er vijf virtuele machines (Vm's) zijn toegewezen voor het gebruiken van gebeurtenissen en een eenvoudige console toepassing in elke VM, wat het werkelijke verbruiks werk doet. Elke console toepassing maakt vervolgens één [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar en registreert het met de Event hubs-service.

In dit voorbeeld scenario gaan we ervan uit dat 16 partities worden toegewezen aan de 5 **EventProcessorHost** -instanties. Sommige **EventProcessorHost** -instanties hebben mogelijk een aantal meer partities dan andere. Voor elke partitie waarvan een **EventProcessorHost** -exemplaar eigenaar is, wordt een exemplaar van de `SimpleEventProcessor` klasse gemaakt. Daarom zijn er 16 exemplaren van `SimpleEventProcessor` Algemeen, met één toegewezen aan elke partitie.

De volgende lijst bevat een overzicht van dit voor beeld:

- 16 Event Hubs partities.
- 5 Vm's, 1 consumenten-app (bijvoorbeeld Consumer.exe) in elke VM.
- 5 EPH exemplaren geregistreerd, 1 in elke VM door Consumer.exe.
- 16 `SimpleEventProcessor` objecten gemaakt door de 5 EPH-instanties.
- 1 Consumer.exe-app kan vier `SimpleEventProcessor` objecten bevatten, omdat het 1 EPH-exemplaar een eigenaar van vier partities kan zijn.

## <a name="partition-ownership-tracking"></a>Het bijhouden van de eigendom van partities

Het eigendom van een partitie aan een EPH-exemplaar (of een consumer) wordt gevolgd door het Azure Storage-account dat is ingesteld voor tracering. U kunt de tracking als volgt visualiseren als een eenvoudige tabel. U kunt de daad werkelijke implementatie zien door de blobs te controleren onder het gegeven opslag account:

| **Naam van consumentengroep** | **Partitie-id** | **Hostnaam (eigenaar)** | **Verwervings tijd (of eigendom) van de lease** | **Offset in partitie (controle punt)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumenten \_ VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumenten \_ VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumenten \_ VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumenten \_ VM3 | 2018-04-15T01:22:56 | 976 |

Hier verkrijgt elke host eigenaar van een partitie voor een bepaalde duur (de duur van de lease). Als een host uitvalt (VM wordt afgesloten), verloopt de lease. Andere hosts proberen het eigendom van de partitie te verkrijgen en een van de hosts slaagt. Met dit proces wordt de lease op de partitie opnieuw ingesteld met een nieuwe eigenaar. Op deze manier kan slechts één lezer tegelijk lezen van elke partitie binnen een Consumer groep.

## <a name="receive-messages"></a>Berichten ontvangen

Elke aanroep van [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) levert een verzameling gebeurtenissen. Het is uw verantwoordelijkheid om deze gebeurtenissen te verwerken. Als u er zeker van wilt zijn dat de processor host elk bericht ten minste één keer verwerkt, moet u uw eigen code voor het opnieuw proberen van het proces schrijven. Maar wees voorzichtig met verontreinigde berichten.

Het is raadzaam om dingen relatief snel uit te voeren. dat wil zeggen, zo weinig mogelijk verwerking. Gebruik in plaats daarvan consumenten groepen. Als u naar opslag moet schrijven en een route ring wilt uitvoeren, is het beter om twee consumenten groepen te gebruiken en twee [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementaties te hebben die afzonderlijk worden uitgevoerd.

Op een bepaald moment tijdens uw verwerking wilt u mogelijk bijhouden wat u hebt gelezen en voltooid. Bijhouden is essentieel als u de Lees bewerking opnieuw moet starten, zodat u niet terugkeert naar het begin van de stroom. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) vereenvoudigt dit bijhouden met behulp van *controle punten*. Een controle punt is een locatie, of offset, voor een bepaalde partitie, binnen een bepaalde consumenten groep, op welk moment u tevreden bent over het verwerken van de berichten. Het markeren van een controle punt in **EventProcessorHost** wordt bereikt door het aanroepen van de methode [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) op het [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) -object. Deze bewerking wordt uitgevoerd in de [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) -methode, maar kan ook worden uitgevoerd in [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Controlepunten maken

De methode [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) heeft twee Overloads: de eerste, zonder para meters, de controle punten voor de hoogste gebeurtenis verschuiving in de verzameling die wordt geretourneerd door [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Deze offset is een ' hoog water merk '. Hierbij wordt ervan uitgegaan dat u alle recente gebeurtenissen hebt verwerkt wanneer u deze aanroept. Als u deze methode op deze manier gebruikt, moet u er rekening mee houden dat u deze zou moeten aanroepen nadat de andere code voor de gebeurtenis verwerking is geretourneerd. Met de tweede overbelasting kunt u een [Event Data](/dotnet/api/microsoft.azure.eventhubs.eventdata) -exemplaar opgeven voor het controle punt. Met deze methode kunt u een ander type water merk gebruiken om een controle punt te maken. Met dit water merk kunt u een ' laag water '-markering implementeren: de laagste geordende gebeurtenis die u zeker hebt, is verwerkt. Deze overbelasting wordt gegeven om flexibiliteit in te scha kelen voor offset beheer.

Wanneer het controle punt wordt uitgevoerd, wordt een JSON-bestand met partitie-specifieke informatie (met name de offset) geschreven naar het opslag account dat is opgegeven in de constructor naar [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Dit bestand wordt doorlopend bijgewerkt. Het is essentieel om controle punten in context te overwegen: het is niet verstandig om elk bericht van het controle punt te maken. Het opslag account dat wordt gebruikt voor controle punten, zou deze belasting waarschijnlijk niet verwerken, maar belang rijker is dat elke gebeurtenis een bericht in de wachtrij plaatst waarvoor een Service Bus wachtrij mogelijk een betere optie is dan een Event Hub. Het idee achter Event Hubs is dat u ten minste één keer op grote schaal krijgt. Door uw downstream-systemen idempotent te maken, is het eenvoudig om fouten te herstellen of opnieuw op te starten. Dit leidt ertoe dat dezelfde gebeurtenissen meerdere keren worden ontvangen.

## <a name="thread-safety-and-processor-instances"></a>Thread veiligheid en processor instanties

[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) is standaard thread veilig en gedraagt zich op synchrone wijze met betrekking tot het exemplaar van [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Wanneer gebeurtenissen voor een partitie arriveren, wordt [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) aangeroepen voor het **IEventProcessor** -exemplaar voor die partitie en worden verdere aanroepen naar **ProcessEventsAsync** voor de partitie geblokkeerd. Volgende berichten en aanroepen van de **ProcessEventsAsync** -wachtrij worden achter de schermen geplaatst, terwijl de bericht pomp blijft draaien op de achtergrond van andere threads. Deze thread beveiliging verwijdert de nood zaak van thread-safe-verzamelingen en verbetert de prestaties aanzienlijk.

## <a name="shut-down-gracefully"></a>Probleemloos afsluiten

Ten slotte maakt [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) een schone afsluiting mogelijk van alle partitie lezers en moet deze altijd worden aangeroepen wanneer een instantie van [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)wordt afgesloten. Als u dit niet doet, kan dit leiden tot vertragingen bij het starten van andere exemplaren van **EventProcessorHost** vanwege verval-en epoche conflicten. Het epoche-beheer wordt uitvoerig besproken in de sectie [epoche](#epoch) van het artikel. 

## <a name="lease-management"></a>Lease beheer
Bij het registreren van een gebeurtenis processor klasse met een instantie van EventProcessorHost wordt de verwerking van gebeurtenissen gestart. Het exemplaar van de host verkrijgt leases op sommige partities van de Event hub, waardoor er mogelijk een deel van andere exemplaren van een host wordt verzameld, op een manier die een gelijkmatige verdeling van partities voor alle exemplaren van de host convergeert. Voor elke geleasde partitie maakt het exemplaar van de host een exemplaar van de gegeven gebeurtenis processor klasse, ontvangt vervolgens gebeurtenissen van die partitie en geeft deze door aan het gebeurtenis verwerkings exemplaar. Naarmate er meer instanties worden toegevoegd en er meer leases worden vastgemaakt, wordt de belasting van alle gebruikers in EventProcessorHost uiteindelijk evenwichtig verdeeld.

Zoals eerder is uitgelegd, vereenvoudigt de tracerings tabel het automatisch schalen van [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Als instantie van **EventProcessorHost** wordt gestart, worden zo veel mogelijk leases opgehaald en worden gebeurtenissen gelezen. Wanneer de leases bijna verlopen, probeert **EventProcessorHost** ze te vernieuwen door een reserve ring te plaatsen. Als de lease beschikbaar is voor vernieuwing, gaat de processor door met lezen, maar als dat niet het geval is, wordt de lezer gesloten en wordt [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) aangeroepen. **CloseAsync** is een goede tijd om een definitieve opschoning voor die partitie uit te voeren.

**EventProcessorHost** bevat een eigenschap [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) . Met deze eigenschap kunt u de controle over lease beheer. Stel deze opties in voordat u uw [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementatie registreert.

## <a name="control-event-processor-host-options"></a>Opties voor het beheren van de gebeurtenis processor host

Daarnaast neemt één overbelasting van [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) een [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) -object als para meter. Gebruik deze para meter om het gedrag van [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) zelf te beheren. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definieert vier eigenschappen en één gebeurtenis:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): de maximale grootte van de verzameling die u wilt ontvangen in een aanroep van [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Deze grootte is niet het minimum, alleen de maximum grootte. Als er minder berichten moeten worden ontvangen, wordt **ProcessEventsAsync** uitgevoerd met zo veel als beschikbaar.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): een waarde die wordt gebruikt door het onderliggende AMQP-kanaal om de bovengrens te bepalen van het aantal berichten dat de client moet ontvangen. Deze waarde moet groter zijn dan of gelijk zijn aan [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): als deze para meter **True** is, wordt [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) aangeroepen wanneer de onderliggende aanroep voor het ontvangen van gebeurtenissen op een partitie een time-out heeft. Deze methode is handig voor het nemen van op tijd gebaseerde acties tijdens peri Oden van inactiviteit op de partitie.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Hiermee kan een functie-of lambda-expressie worden ingesteld, die wordt aangeroepen om de initiële offset te bieden wanneer een lezer begint met het lezen van een partitie. Zonder deze offset op te geven, begint de lezer bij het oudste evenement, tenzij een JSON-bestand met een offset al is opgeslagen in het opslag account dat is opgegeven aan de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -constructor. Deze methode is handig als u het gedrag van het opstarten van de lezer wilt wijzigen. Wanneer deze methode wordt aangeroepen, bevat de object parameter de partitie-ID waarvoor de lezer wordt gestart.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Hiermee kunt u meldingen ontvangen over onderliggende uitzonde ringen die optreden in [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Als dingen niet werken zoals u verwacht, is deze gebeurtenis een goede plaats om te beginnen met zoeken.

## <a name="epoch"></a>Epoch

De receive-epoche werkt als volgt:

### <a name="with-epoch"></a>Met epoche
Epoche is een unieke id (epoche waarde) die door de service wordt gebruikt om het eigendom van een partitie/lease af te dwingen. U maakt een op epoche gebaseerde ontvanger met behulp van de methode [CreateEpochReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) . Met deze methode wordt een op epoche gebaseerde ontvanger gemaakt. De ontvanger wordt gemaakt voor een specifieke Event Hub partitie van de opgegeven Consumer groep.

De epoche-functie biedt gebruikers de mogelijkheid om ervoor te zorgen dat er op elk moment maar één ontvanger op een Consumer groep is, met de volgende regels:

- Als er geen bestaande ontvanger is voor een Consumer groep, kan de gebruiker een ontvanger maken met elke epoche waarde.
- Als er een ontvanger is met een epoche waarde E1 en er een nieuwe ontvanger wordt gemaakt met een epoche waarde E2 waarbij E1 <= E2, wordt de ontvanger met E1 automatisch losgekoppeld, ontvanger met E2 wordt gemaakt.
- Als er een ontvanger is met een epoche waarde E1 en er een nieuwe ontvanger wordt gemaakt met een epoche waarde E2 waarbij E1 > E2, wordt het maken van E2 met een fout gegenereerd: er bestaat al een ontvanger met epoche E1.

### <a name="no-epoch"></a>Geen epoche
U maakt een ontvanger op basis van een niet-epoche met behulp van de methode [CreateReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) . 

Er zijn enkele scenario's in de verwerking van stromen waarbij gebruikers meerdere ontvangers willen maken op één consumer groep. Om dergelijke scenario's te ondersteunen, hebben we de mogelijkheid om een ontvanger zonder epoche te maken. in dit geval kunnen we Maxi maal vijf gelijktijdige ontvangers voor de consumenten groep toestaan.

### <a name="mixed-mode"></a>Gemengde modus
Het gebruik van de toepassing wordt niet aanbevolen, waarbij u een ontvanger maakt met epoche en vervolgens overschakelt naar No-epoche of vice-versa op dezelfde Consumer groep. Als dit probleem zich voordoet, wordt het echter door de service verwerkt met de volgende regels:

- Als er al een ontvanger is gemaakt met epoche E1 en actief gebeurtenissen ontvangt en er een nieuwe ontvanger wordt gemaakt zonder epoche, mislukt het maken van een nieuwe ontvanger. Epoche-ontvangers hebben altijd voor rang op het systeem.
- Als er al een ontvanger is gemaakt met epoche E1 en de verbinding is verbroken, en er een nieuwe ontvanger wordt gemaakt zonder epoche op een nieuwe MessagingFactory, zal het maken van een nieuwe ontvanger slagen. Er wordt hier een voor behoud weer gegeven dat ons systeem na ongeveer 10 minuten de verbinding van de ontvanger moet detecteren.
- Als er een of meer ontvangers zijn gemaakt zonder epoche, en er een nieuwe ontvanger wordt gemaakt met epoche E1, worden alle oude ontvangers losgekoppeld van elkaar.


> [!NOTE]
> We raden u aan verschillende consumenten groepen te gebruiken voor toepassingen die gebruikmaken van epoches en voor gebruikers die geen epoches gebruiken om fouten te voor komen. 


## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de Event processor host, raadpleegt u de volgende artikelen voor meer informatie over Event Hubs:

- Aan de slag met Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Programmeer handleiding voor Event Hubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Event Hubs-voorbeelden op Github](https://github.com/Azure/azure-event-hubs/tree/master/samples)
