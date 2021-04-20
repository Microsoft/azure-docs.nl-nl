---
title: Gepartities Azure Service Bus wachtrijen en onderwerpen maken | Microsoft Docs
description: Beschrijft hoe u Service Bus wachtrijen en onderwerpen partitioneert met behulp van meerdere berichtbrokers.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bc41bcf31102b19dd35f62452b956faf4f029551
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750910"
---
# <a name="partitioned-queues-and-topics"></a>Gepartitioneerde wachtrijen en onderwerpen

Azure Service Bus maakt gebruik van meerdere berichtenbrokers voor het verwerken van berichten en meerdere berichtenopslag voor het opslaan van berichten. Een conventionele wachtrij of onderwerp wordt verwerkt door één berichtenbroker en opgeslagen in één berichtenopslag. Service Bus *kunnen wachtrijen* en onderwerpen, of berichtenentiteiten, worden gepartitiefd over meerdere berichtenbrokers en berichtenopslag. Partitioneren betekent dat de algehele doorvoer van een gepartitiestiteit niet langer wordt beperkt door de prestaties van één berichtenbroker of berichtenopslag. Bovendien zorgt een tijdelijke storing van een berichtenopslag er niet voor dat een gepartitiesde wachtrij of onderwerp niet beschikbaar is. Gepartitiese wachtrijen en onderwerpen kunnen alle geavanceerde Service Bus bevatten, zoals ondersteuning voor transacties en sessies.

> [!NOTE]
> Partitionering is beschikbaar bij het maken van entiteiten voor alle wachtrijen en onderwerpen in Basic- of Standard-SKU's. Deze is niet beschikbaar voor de Premium Messaging-SKU, maar alle eerder gepartities in Premium-naamruimten blijven werken zoals verwacht.
 
Het is niet mogelijk om de partitioneringsoptie voor een bestaande wachtrij of bestaand onderwerp te wijzigen; U kunt de optie alleen instellen wanneer u de entiteit maakt.

## <a name="how-it-works"></a>Uitleg

Elke gepart partitioneerde wachtrij of elk onderwerp bestaat uit meerdere partities. Elke partitie wordt opgeslagen in een ander berichtenopslag en verwerkt door een andere berichtenbroker. Wanneer een bericht wordt verzonden naar een gepart partitioneerde wachtrij of onderwerp, Service Bus het bericht toegewezen aan een van de partities. De selectie wordt willekeurig uitgevoerd door Service Bus of met behulp van een partitiesleutel die de afzender kan opgeven.

Wanneer een client een bericht wil ontvangen van een gepartitieste wachtrij of van een abonnement op een gepartitief onderwerp, vraagt Service Bus alle partities op voor berichten en retourneert vervolgens het eerste bericht dat is verkregen van een van de berichtenopslag naar de ontvanger. Service Bus andere berichten in de cache opgeslagen en retourneert ze wanneer er aanvullende ontvangstaanvragen worden ontvangen. Een ontvangende client is niet op de hoogte van de partitionering; het client gerichte gedrag van een gepart partitioneerde wachtrij of onderwerp (bijvoorbeeld lezen, voltooien, uitstellen, deadletter, prefetching) is identiek aan het gedrag van een reguliere entiteit.

De peek-bewerking voor een niet-gepart partitioneerde entiteit retourneert altijd het oudste bericht, maar niet op een gepartitiesteerde entiteit. In plaats daarvan wordt het oudste bericht in een van de partities waarvan de berichtenbroker het eerst heeft gereageerd, weergegeven. Er is geen garantie dat het geretourneerde bericht het oudste bericht is voor alle partities. 

Er zijn geen extra kosten voor het verzenden van een bericht naar of het ontvangen van een gepartitiede wachtrij of onderwerp.

> [!NOTE]
> De peek-bewerking retourneert het oudste bericht van de partitie op basis van het volgnummer. Voor gepartitiestiteiten wordt het volgnummer ten opzichte van de partitie uitgegeven. Zie Message [sequencing and timestamps (Bericht sequencing en tijdstempels) voor meer informatie.](../service-bus-messaging/message-sequencing.md)

## <a name="enable-partitioning"></a>Partitionering inschakelen

Als u gepartitiede wachtrijen en onderwerpen met Azure Service Bus wilt gebruiken, gebruikt u azure SDK versie 2.2 of hoger of geeft u of `api-version=2013-10` hoger op in uw HTTP-aanvragen.

### <a name="standard"></a>Standard

In de standard messaging-laag kunt u Service Bus wachtrijen en onderwerpen maken in grootten van 1, 2, 3, 4 of 5 GB (de standaardwaarde is 1 GB). Als partitioneren is ingeschakeld, maakt Service Bus 16 kopieën (16 partities) van de entiteit, elk van dezelfde opgegeven grootte. Als u dus een wachtrij van 5 GB maakt, wordt met 16 partities de maximale wachtrijgrootte (5 \* 16) = 80 GB. U kunt de maximale grootte van uw gepart partitioneerde wachtrij of onderwerp bekijken door de vermelding ervan te bekijken op de [Azure Portal][Azure portal], op de **blade** Overzicht voor die entiteit.

### <a name="premium"></a>Premium

In een Premium-naamruimte worden partitioneringsentiteiten niet ondersteund. U kunt echter nog steeds Service Bus wachtrijen en onderwerpen maken in grootten van 1, 2, 3, 4, 5, 10, 20, 40 of 80 GB (de standaardwaarde is 1 GB). U kunt de grootte van uw wachtrij of onderwerp bekijken door de vermelding ervan te bekijken op de [Azure Portal][Azure portal], op de **blade** Overzicht voor die entiteit.

### <a name="create-a-partitioned-entity"></a>Een gepart partitioneerde entiteit maken

Er zijn verschillende manieren om een gepartitieste wachtrij of onderwerp te maken. Wanneer u de wachtrij of het onderwerp vanuit uw toepassing maakt, kunt u partitionering voor de wachtrij of het onderwerp inschakelen door respectievelijk de eigenschap [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] of [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] in te stellen op **true**. Deze eigenschappen moeten worden ingesteld op het moment dat de wachtrij of het onderwerp wordt gemaakt en zijn alleen beschikbaar in de oudere [WindowsAzure.ServiceBus-bibliotheek.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Zoals eerder vermeld, is het niet mogelijk om deze eigenschappen te wijzigen in een bestaande wachtrij of een bestaand onderwerp. Bijvoorbeeld:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

U kunt ook een gepart partitioneerde wachtrij of onderwerp maken in de [Azure Portal.][Azure portal] Wanneer u een wachtrij of onderwerp in de portal maakt,  wordt standaard de optie **Partitionering** inschakelen in de wachtrij of het dialoogvenster Maken ingeschakeld. U kunt deze optie alleen uitschakelen in een entiteit van de Standard-laag; partitionering in de Premium-laag wordt niet ondersteund en het selectievakje heeft geen effect. 

## <a name="use-of-partition-keys"></a>Gebruik van partitiesleutels

Wanneer een bericht in een gepartitiese wachtrij of onderwerp wordt geplaatst, controleert Service Bus op de aanwezigheid van een partitiesleutel. Als er een wordt gevonden, wordt de partitie geselecteerd op basis van die sleutel. Als er geen partitiesleutel wordt gevonden, wordt de partitie geselecteerd op basis van een intern algoritme.

### <a name="using-a-partition-key"></a>Een partitiesleutel gebruiken

Voor sommige scenario's, zoals sessies of transacties, moeten berichten worden opgeslagen in een specifieke partitie. Voor al deze scenario's is het gebruik van een partitiesleutel vereist. Alle berichten die gebruikmaken van dezelfde partitiesleutel worden toegewezen aan dezelfde partitie. Als de partitie tijdelijk niet beschikbaar is, Service Bus een foutmelding.

Afhankelijk van het scenario worden verschillende berichteigenschappen gebruikt als partitiesleutel:

**SessionId:** als voor een bericht de [eigenschap SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) is ingesteld, gebruikt Service Bus **SessionID** als partitiesleutel. Op deze manier worden alle berichten die deel uitmaken van dezelfde sessie verwerkt door dezelfde berichtenbroker. Met sessies Service Bus u de volgorde van berichten en de consistentie van sessie-staten garanderen.

**PartitionKey:** als een bericht de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) heeft, maar niet de eigenschap [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) gebruikt Service Bus de [eigenschapswaarde PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) als partitiesleutel. Als voor het bericht zowel de [sessionid](/dotnet/api/microsoft.azure.servicebus.message.sessionid) als de [partitionkey-eigenschappen](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) zijn ingesteld, moeten beide eigenschappen identiek zijn. Als de [eigenschap PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) is ingesteld op een andere waarde dan de eigenschap [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) retourneert Service Bus een ongeldige bewerkingsuitslag. De [eigenschap PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) moet worden gebruikt als een afzender niet-sessiebewuste transactionele berichten verzendt. De partitiesleutel zorgt ervoor dat alle berichten die binnen een transactie worden verzonden, worden verwerkt door dezelfde berichtenbroker.

**MessageId:** als voor de wachtrij of het onderwerp de eigenschap [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) is ingesteld op **true** en de [eigenschappen SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) of [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) niet zijn ingesteld, fungeert de [eigenschapswaarde MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) als de partitiesleutel. (De Microsoft .NET en AMQP-bibliotheken wijzen automatisch een bericht-id toe als de verzendende toepassing dat niet doet.) In dit geval worden alle kopieën van hetzelfde bericht verwerkt door dezelfde berichtenbroker. Met deze id Service Bus dubbele berichten detecteren en elimineren. Als de [eigenschap RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) niet is ingesteld op **true**, Service Bus de eigenschap [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) niet als een partitiesleutel.

### <a name="not-using-a-partition-key"></a>Geen partitiesleutel gebruiken

Als er geen partitiesleutel is, Service Bus berichten op een round robin-manier distribueren naar alle partities van de gepartities van de gepartities van de wachtrij of het onderwerp. Als de gekozen partitie niet beschikbaar is, Service Bus het bericht toe aan een andere partitie. Op deze manier slaagt de verzendbewerking ondanks dat een berichtenopslag tijdelijk niet beschikbaar is. U kunt echter niet de gegarandeerde volgorde bereiken die een partitiesleutel biedt.

Zie dit artikel voor een uitgebreidere bespreking van de balans tussen beschikbaarheid (geen partitiesleutel) en consistentie (met behulp van [een partitiesleutel).](../event-hubs/event-hubs-availability-and-consistency.md) Deze informatie geldt ook voor gepartitiesteerde Service Bus entiteiten.

Als u Service Bus tijd wilt geven om het bericht in een andere partitie te onderverwerken, moet de [Waarde van OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) die is opgegeven door de client die het bericht verzendt, langer zijn dan 15 seconden. Het wordt aanbevolen om de eigenschap [OperationTimeout in te](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) stellen op de standaardwaarde van 60 seconden.

Een partitiesleutel 'pint' een bericht vast aan een specifieke partitie. Als de berichtenopslag met deze partitie niet beschikbaar is, wordt Service Bus een foutbericht weergegeven. Als er geen partitiesleutel is, Service Bus een andere partitie kiezen en de bewerking slaagt. Daarom wordt u aangeraden geen partitiesleutel op te leveren, tenzij dit vereist is.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Geavanceerde onderwerpen: transacties gebruiken met gepartitiestiteiten

Berichten die worden verzonden als onderdeel van een transactie moeten een partitiesleutel opgeven. De sleutel kan een van de volgende eigenschappen zijn: [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)of [MessageId.](/dotnet/api/microsoft.azure.servicebus.message.messageid) Alle berichten die worden verzonden als onderdeel van dezelfde transactie, moeten dezelfde partitiesleutel opgeven. Als u een bericht probeert te verzenden zonder een partitiesleutel binnen een transactie, Service Bus een ongeldige bewerkingsuitslag retourneren. Als u meerdere berichten probeert te verzenden binnen dezelfde transactie die verschillende partitiesleutels hebben, Service Bus een ongeldige bewerkingsuitslag retourneren. Bijvoorbeeld:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Als een van de eigenschappen die als partitiesleutel fungeren, wordt Service Bus bericht vastgemaakt aan een specifieke partitie. Dit gedrag doet zich voor of een transactie wordt gebruikt. U wordt aangeraden geen partitiesleutel op te geven als dit niet nodig is.

## <a name="using-sessions-with-partitioned-entities"></a>Sessies gebruiken met gepartitiestiteiten

Als u een transactioneel bericht wilt verzenden naar een sessiebewust onderwerp of wachtrij, moet voor het bericht de [eigenschap SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) zijn ingesteld. Als de [eigenschap PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ook is opgegeven, moet deze identiek zijn aan de [eigenschap SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Als deze verschillen, Service Bus een ongeldige bewerkings uitzondering.

In tegenstelling tot reguliere (niet-gepart partitioneerde) wachtrijen of onderwerpen, is het niet mogelijk om één transactie te gebruiken om meerdere berichten naar verschillende sessies te verzenden. Als dit wordt geprobeerd, Service Bus een ongeldige bewerkings uitzondering. Bijvoorbeeld:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisch doorsturen van berichten met gepartitiestiteiten

Service Bus biedt ondersteuning voor het automatisch doorsturen van berichten van, naar of tussen gepartitiestiteiten. Als u automatisch doorsturen van berichten wilt inschakelen, stelt u de eigenschap [QueueDescription.ForwardTo][QueueDescription.ForwardTo] in voor de bronwachtrij of het abonnement. Als in het bericht een partitiesleutel wordt opgegeven[(SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)of [MessageId),](/dotnet/api/microsoft.azure.servicebus.message.messageid)wordt die partitiesleutel gebruikt voor de doelentiteit.

## <a name="considerations-and-guidelines"></a>Overwegingen en richtlijnen
* **Functies met hoge consistentie:** als een entiteit gebruikmaakt van functies zoals sessies, duplicaatdetectie of expliciet beheer van de partitioneringssleutel, worden de berichtenbewerkingen altijd doorgeleid naar een specifieke partitie. Als een van de partities te maken heeft met veel verkeer of als de onderliggende opslag niet in orde is, mislukken deze bewerkingen en wordt de beschikbaarheid verminderd. Over het algemeen is de consistentie nog steeds veel hoger dan niet-gepartitiestiteiten; alleen een subset van het verkeer ondervindt problemen, in tegenstelling tot al het verkeer. Zie deze bespreking van beschikbaarheid en consistentie [voor meer informatie.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Beheer:** Bewerkingen zoals Maken, Bijwerken en Verwijderen moeten worden uitgevoerd op alle partities van de entiteit. Als een partitie niet in orde is, kan dit leiden tot fouten voor deze bewerkingen. Voor de bewerking Get moeten gegevens, zoals het aantal berichten, worden geaggregeerd uit alle partities. Als een partitie niet in orde is, wordt de beschikbaarheidsstatus van de entiteit gerapporteerd als beperkt.
* **Scenario's voor berichten met** een laag volume: in dergelijke scenario's, met name bij het gebruik van het HTTP-protocol, moet u mogelijk meerdere ontvangstbewerkingen uitvoeren om alle berichten te verkrijgen. Voor ontvangstaanvragen voert de front-end een ontvangst uit op alle partities en worden alle ontvangen antwoorden in de cache opgeslagen. Een volgende ontvangstaanvraag voor dezelfde verbinding zou voordeel hebben van deze caching en de latentie voor ontvangen is lager. Als u echter meerdere verbindingen hebt of HTTP gebruikt, wordt er voor elke aanvraag een nieuwe verbinding tot stand brengen. Daarom is er geen garantie dat het op hetzelfde knooppunt zou landen. Als alle bestaande berichten zijn vergrendeld en in de cache zijn opgeslagen in een andere front-end, retourneert de ontvangstbewerking **null**. Berichten verlopen uiteindelijk en u kunt ze opnieuw ontvangen. HTTP keep-alive wordt aanbevolen. Wanneer u partitionering gebruikt in scenario's met een laag volume, kan het langer duren dan verwacht om bewerkingen te ontvangen. Daarom raden we u aan in deze scenario's geen partitionering te gebruiken. Verwijder alle bestaande gepartities en maak ze opnieuw met partitionering uitgeschakeld om de prestaties te verbeteren.
* **Bladeren/berichten bekijken:** alleen beschikbaar in de oudere [WindowsAzure.ServiceBus-bibliotheek.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) retourneert niet altijd het aantal berichten dat is opgegeven in de [eigenschap MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Er zijn twee veelvoorkomende redenen voor dit gedrag. Een van de redenen is dat de geaggregeerde grootte van de verzameling berichten de maximale grootte van 256 kB overschrijdt. Een andere reden is dat als voor de wachtrij of het onderwerp de eigenschap [EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) is ingesteld op **true**, een partitie mogelijk niet voldoende berichten heeft om het aangevraagde aantal berichten te voltooien. Als een toepassing een specifiek aantal berichten wil ontvangen, moet deze [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) herhaaldelijk aanroepen totdat dat aantal berichten wordt ontvangen of er geen berichten meer zijn om te bekijken. Zie de documentatie [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) of [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API voor meer informatie, inclusief codevoorbeelden.

## <a name="latest-added-features"></a>Meest recente toegevoegde functies

* Regel toevoegen of verwijderen wordt nu ondersteund met gepartitiestiteiten. Anders dan niet-gepartitiestiteiten, worden deze bewerkingen niet ondersteund onder transacties. 
* AMQP wordt nu ondersteund voor het verzenden en ontvangen van berichten naar en van een gepartitiestiteit.
* AMQP wordt nu ondersteund voor de volgende bewerkingen: [Batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch)verzenden, [Batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch) [ontvangen,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive)Ontvangen op volgnummer, [Peek,](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek)Vernieuwingsvergrendeling, [](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync)Planningsbericht, Geplande bericht [annuleren,](/dotnet/api/microsoft.azure.servicebus.ruledescription)Regel [toevoegen,](/dotnet/api/microsoft.azure.servicebus.ruledescription)Regel verwijderen, [Sessievernieuwing](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock)vergrendelen, Sessietoestand [instellen,](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)Sessietoestand opsluizen en Sessies [](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock) [](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync)opsnoemen. [](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate) [](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)

## <a name="partitioned-entities-limitations"></a>Beperkingen voor gepartitiestiteiten

Momenteel Service Bus de volgende beperkingen opgelegd voor gepart partitioneerde wachtrijen en onderwerpen:

* Gepartitiede wachtrijen en onderwerpen worden niet ondersteund in de Premium Messaging-laag. Sessies worden ondersteund in de premier-laag met behulp van SessionId. 
* Gepartitiede wachtrijen en onderwerpen bieden geen ondersteuning voor het verzenden van berichten die deel uitmaken van verschillende sessies in één transactie.
* Service Bus staat momenteel maximaal 100 gepartitioneerde wachtrijen en onderwerpen per naamruimte toe. Elke gepart partitioneerde wachtrij of elk onderwerp telt mee voor het quotum van 10.000 entiteiten per naamruimte (is niet van toepassing op de Premium-laag).

## <a name="next-steps"></a>Volgende stappen
U kunt partitioneren inschakelen met behulp van Azure Portal, PowerShell, CLI, Resource Manager sjabloon, .NET, Java, Python en JavaScript. Zie Partitionering inschakelen [voor meer informatie.](enable-partitions.md) 

Meer informatie over de belangrijkste concepten van de AMQP 1.0-berichtspecificatie in de [AMQP 1.0-protocolhandleiding.](service-bus-amqp-protocol-guide.md)

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
