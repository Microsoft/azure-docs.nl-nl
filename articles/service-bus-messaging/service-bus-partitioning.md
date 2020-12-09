---
title: Gepartitioneerde Azure Service Bus-wacht rijen en-onderwerpen maken | Microsoft Docs
description: Hierin wordt beschreven hoe u Service Bus-wacht rijen en-onderwerpen partitioneert met meerdere bericht brokers.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fd845ba24fd96ad6de566a7f55b25bd7129074d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930429"
---
# <a name="partitioned-queues-and-topics"></a>Gepartitioneerde wachtrijen en onderwerpen

Azure Service Bus maakt gebruik van meerdere bericht Brokers voor het verwerken van berichten en meerdere berichten archieven om berichten op te slaan. Een conventionele wachtrij of onderwerp wordt verwerkt door één Message Broker en opgeslagen in één berichten archief. Met Service Bus *partities* kunnen wacht rijen en onderwerpen, of *Messa ging-entiteiten*, worden gepartitioneerd in meerdere bericht brokers en berichten archieven. Partitioneren betekent dat de algemene door Voer van een gepartitioneerde entiteit niet langer wordt beperkt door de prestaties van een bericht Broker of berichten archief. Daarnaast wordt een tijdelijke onderbreking van een berichten archief niet weer gegeven in een gepartitioneerde wachtrij of onderwerp niet beschikbaar. Gepartitioneerde wacht rijen en onderwerpen kunnen alle geavanceerde functies van Service Bus bevatten, zoals ondersteuning voor trans acties en sessies.

> [!NOTE]
> Partitioneren is beschikbaar bij het maken van entiteiten voor alle wacht rijen en onderwerpen in Basic-of Standard-Sku's. Het is niet beschikbaar voor de SKU Premium Messa ging, maar eerder bestaande gepartitioneerde entiteiten in Premium-naam ruimten blijven werken zoals verwacht.
 
Het is niet mogelijk om de partitie optie te wijzigen voor een bestaande wachtrij of elk onderwerp. u kunt de optie alleen instellen wanneer u de entiteit maakt.

## <a name="how-it-works"></a>Uitleg

Elke gepartitioneerde wachtrij of elk onderwerp bestaat uit meerdere partities. Elke partitie wordt opgeslagen in een ander berichten archief en verwerkt door een andere Message Broker. Wanneer een bericht wordt verzonden naar een gepartitioneerde wachtrij of onderwerp, wordt het bericht Service Bus toegewezen aan een van de partities. De selectie wordt wille keurig uitgevoerd door Service Bus of door gebruik te maken van een partitie sleutel die door de afzender kan worden opgegeven.

Wanneer een client een bericht wil ontvangen van een gepartitioneerde wachtrij of van een abonnement op een gepartitioneerd onderwerp, Service Bus alle partities voor berichten doorzoekt, wordt het eerste bericht geretourneerd dat is verkregen van een van de berichten archieven naar de ontvanger. Service Bus worden de andere berichten in de cache opgeslagen en geretourneerd wanneer er extra ontvangst aanvragen worden ontvangen. Een ontvangende client is niet op de hoogte van de partitie. het client gedrag van een gepartitioneerde wachtrij of onderwerp (bijvoorbeeld lezen, volt ooien, uitstellen, deadletter, vooraf ophalen) is identiek aan het gedrag van een normale entiteit.

De Peek-bewerking voor een niet-gepartitioneerde entiteit retourneert altijd het oudste bericht, maar niet in een gepartitioneerde entiteit. In plaats daarvan wordt het oudste bericht geretourneerd in een van de partities waarvan de Message Broker het eerst heeft gereageerd. Er wordt niet gegarandeerd dat het geretourneerde bericht het oudste is in alle partities. 

Er zijn geen extra kosten verbonden aan het verzenden van een bericht naar of het ontvangen van een bericht van een gepartitioneerde wachtrij of onderwerp.
>[!NOTE]
> De bewerking Peek retourneert het oudste bericht van het gedeelte op basis van de SequenceNumber. Voor partioned-entiteiten wordt het Volg nummer uitgegeven ten opzichte van de partitie. Zie [bericht sequentiëren en tijds tempels](../service-bus-messaging/message-sequencing.md)voor meer informatie.

## <a name="enable-partitioning"></a>Partitionering inschakelen

Als u gepartitioneerde wacht rijen en onderwerpen met Azure Service Bus wilt gebruiken, gebruikt u de Azure SDK-versie 2,2 of hoger of geeft u `api-version=2013-10` of later op in uw HTTP-aanvragen.

### <a name="standard"></a>Standard

U kunt in de laag Standard Messa ging Service Bus-wacht rijen en-onderwerpen maken in een grootte van 1, 2, 3, 4 of 5 GB (de standaard waarde is 1 GB). Als partitioneren is ingeschakeld, maakt Service Bus 16 kopieën (16 partities) van de entiteit, die elk dezelfde grootte hebben. Als u een wachtrij maakt met een grootte van 5 GB, met 16 partities wordt de maximale wachtrij grootte (5 \* 16) = 80 GB. U kunt de maximale grootte van de gepartitioneerde wachtrij of het onderwerp zien door te kijken naar het item in het [Azure Portal][Azure portal]op de Blade **overzicht** voor die entiteit.

### <a name="premium"></a>Premium

In een naam ruimte van een Premium-laag worden het partitioneren van entiteiten niet ondersteund. U kunt echter nog steeds Service Bus-wacht rijen en-onderwerpen maken in een grootte van 1, 2, 3, 4, 5, 10, 20, 40 of 80 GB (de standaard waarde is 1 GB). U kunt de grootte van uw wachtrij of onderwerp zien door de vermelding te bekijken op het [Azure Portal][Azure portal]op de Blade **overzicht** voor die entiteit.

### <a name="create-a-partitioned-entity"></a>Een gepartitioneerde entiteit maken

Er zijn verschillende manieren voor het maken van een gepartitioneerde wachtrij of onderwerp. Wanneer u de wachtrij of het onderwerp maakt vanuit uw toepassing, kunt u partitioneren voor de wachtrij of het onderwerp inschakelen door respectievelijk de eigenschap [QueueDescription. EnablePartitioning][QueueDescription.EnablePartitioning] of [TopicDescription. EnablePartitioning][TopicDescription.EnablePartitioning] in te stellen op **True**. Deze eigenschappen moeten worden ingesteld op het moment dat de wachtrij of het onderwerp wordt gemaakt en zijn alleen beschikbaar in de oudere [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -bibliotheek. Zoals eerder vermeld, is het niet mogelijk om deze eigenschappen te wijzigen voor een bestaande wachtrij of onderwerp. Bijvoorbeeld:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

U kunt ook een gepartitioneerde wachtrij of een onderwerp in de [Azure Portal][Azure portal]maken. Wanneer u een wachtrij of onderwerp maakt in de portal, wordt de optie **partitioneren inschakelen** in het dialoog venster wachtrij of onderwerp **maken** standaard ingeschakeld. U kunt deze optie alleen uitschakelen in een entiteit met een standaard laag. de Premium-laag wordt niet ondersteund en het selectie vakje heeft geen effect. 

## <a name="use-of-partition-keys"></a>Gebruik van partitie sleutels

Wanneer een bericht in een gepartitioneerde wachtrij of een onderwerp wordt geplaatst, wordt Service Bus gecontroleerd op de aanwezigheid van een partitie sleutel. Als er een wordt gevonden, wordt de partitie geselecteerd op basis van die sleutel. Als er geen partitie sleutel wordt gevonden, wordt de partitie geselecteerd op basis van een intern algoritme.

### <a name="using-a-partition-key"></a>Een partitie sleutel gebruiken

In sommige scenario's, zoals sessies of trans acties, moeten berichten worden opgeslagen in een specifieke partitie. Al deze scenario's vereisen het gebruik van een partitie sleutel. Alle berichten die gebruikmaken van dezelfde partitie sleutel worden toegewezen aan dezelfde partitie. Als de partitie tijdelijk niet beschikbaar is, Service Bus een fout geretourneerd.

Afhankelijk van het scenario worden verschillende bericht eigenschappen gebruikt als een partitie sleutel:

**Sessie-id**: als voor een bericht de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) is ingesteld, gebruikt Service Bus **SessionID** als de partitie sleutel. Op deze manier worden alle berichten die deel uitmaken van dezelfde sessie, verwerkt door dezelfde Message Broker. Met sessies kunnen Service Bus de rang schikking van berichten garanderen en de consistentie van de sessie status.

**PartitionKey**: als een bericht de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) heeft, maar niet de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) is ingesteld, gebruikt Service Bus de waarde van de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) als de partitie sleutel. Als de eigenschappen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) en [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) zijn ingesteld voor het bericht, moeten beide eigenschappen identiek zijn. Als de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) is ingesteld op een andere waarde dan de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , retourneert service bus een ongeldige bewerkings uitzondering. De eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) moet worden gebruikt als een afzender niet-sessie bewuste transactionele berichten verzendt. De partitie sleutel zorgt ervoor dat alle berichten die binnen een trans actie worden verzonden, worden verwerkt door dezelfde berichten Broker.

**MessageId**: als de eigenschap [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) van de wachtrij of het onderwerp is ingesteld op **True** en de eigenschappen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) of [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) niet zijn ingesteld, fungeert de waarde van de eigenschap [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) als de partitie sleutel. (De Microsoft .NET-en AMQP-bibliotheken wijzen automatisch een bericht-ID toe als de verzendende toepassing dat niet doet.) In dit geval worden alle kopieën van hetzelfde bericht verwerkt door dezelfde Message Broker. Met deze ID kan Service Bus dubbele berichten detecteren en verwijderen. Als de eigenschap [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) niet is ingesteld op **True**, beschouwt service bus de [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) -eigenschap niet als een partitie sleutel.

### <a name="not-using-a-partition-key"></a>Geen partitie sleutel gebruiken

Als er geen partitie sleutel is, verdeelt Service Bus berichten in een Round-Robin naar alle partities van de gepartitioneerde wachtrij of het onderwerp. Als de gekozen partitie niet beschikbaar is, Service Bus wijst het bericht toe aan een andere partitie. Op deze manier slaagt de verzend bewerking ondanks de tijdelijke niet-beschik baarheid van een berichten archief. U krijgt echter niet de gegarandeerde volg orde die een partitie sleutel biedt.

Zie [dit artikel](../event-hubs/event-hubs-availability-and-consistency.md)voor een uitgebreidere bespreking van de verhouding tussen Beschik baarheid (geen partitie sleutel) en consistentie (met behulp van een partitie sleutel). Deze informatie is gelijkelijk van toepassing op gepartitioneerde Service Bus entiteiten.

Om Service Bus voldoende tijd te geven om het bericht in een andere partitie in te zetten, moet de [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) -waarde die is opgegeven door de client die het bericht verzendt, langer dan 15 seconden zijn. Het is raadzaam om de eigenschap [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) in te stellen op de standaard waarde van 60 seconden.

Een partitie sleutel ' pin ' is een bericht naar een specifieke partitie. Als het berichten archief dat deze partitie bevat, niet beschikbaar is, Service Bus een fout geretourneerd. Als er geen partitie sleutel is, kan Service Bus een andere partitie kiezen en de bewerking slaagt. Daarom is het raadzaam dat u geen partitie sleutel opgeeft, tenzij dit vereist is.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Geavanceerde onderwerpen: trans acties gebruiken met gepartitioneerde entiteiten

Berichten die worden verzonden als onderdeel van een transactie moeten een partitiesleutel opgeven. De sleutel kan een van de volgende eigenschappen hebben: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)of [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Alle berichten die worden verzonden als onderdeel van dezelfde trans actie, moeten dezelfde partitie sleutel opgeven. Als u probeert een bericht zonder partitie sleutel binnen een trans actie te verzenden, retourneert Service Bus een ongeldige bewerkings uitzondering. Als u probeert meerdere berichten te verzenden binnen dezelfde trans actie die verschillende partitie sleutels hebben, retourneert Service Bus een ongeldige bewerkings uitzondering. Bijvoorbeeld:

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

Als een van de eigenschappen die als partitie sleutel fungeren, is ingesteld, Service Bus het bericht aan een specifieke partitie vastmaken. Dit gedrag treedt op als er al dan niet een trans actie wordt gebruikt. Het is raadzaam om geen partitie sleutel op te geven als dit niet nodig is.

## <a name="using-sessions-with-partitioned-entities"></a>Sessies gebruiken met gepartitioneerde entiteiten

Als u een transactioneel bericht wilt verzenden naar een sessie bewust onderwerp of wachtrij, moet de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) worden ingesteld op het bericht. Als de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ook is opgegeven, moet deze identiek zijn aan de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Als ze verschillen, Service Bus een ongeldige bewerkings uitzondering geretourneerd.

In tegens telling tot normale (niet-gepartitioneerde) wacht rijen of onderwerpen is het niet mogelijk één trans actie te gebruiken om meerdere berichten naar verschillende sessies te verzenden. Als dit wordt geprobeerd, wordt door Service Bus een ongeldige bewerkings uitzondering geretourneerd. Bijvoorbeeld:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisch door sturen van berichten met gepartitioneerde entiteiten

Service Bus ondersteunt automatisch door sturen van berichten van, naar of tussen gepartitioneerde entiteiten. Als u automatisch door sturen van berichten wilt inschakelen, stelt u de eigenschap [QueueDescription. ForwardTo][QueueDescription.ForwardTo] in op de bron wachtrij of het-abonnement. Als in het bericht een partitie sleutel ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)of [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)) wordt opgegeven, wordt die partitie sleutel gebruikt voor de doel entiteit.

## <a name="considerations-and-guidelines"></a>Overwegingen en richt lijnen
* **Hoge consistentie functies**: als een entiteit gebruikmaakt van functies zoals sessies, duplicaten detectie of expliciete controle van de partitie sleutel, worden de berichten bewerkingen altijd gerouteerd naar een specifieke partitie. Als een van de partities veel verkeer heeft of als het onderliggende archief beschadigd is, worden deze bewerkingen niet in orde en beschik baarheid gereduceerd. Over het algemeen is de consistentie nog veel hoger dan niet-gepartitioneerde entiteiten; alleen een subset van verkeer ondervindt problemen, in tegens telling tot al het verkeer. Zie deze [bespreking van Beschik baarheid en consistentie](../event-hubs/event-hubs-availability-and-consistency.md)voor meer informatie.
* **Beheer**: bewerkingen zoals maken, bijwerken en verwijderen moeten op alle partities van de entiteit worden uitgevoerd. Als een partitie een slechte status heeft, kan dit leiden tot fouten bij deze bewerkingen. Voor de Get-bewerking moet informatie zoals het aantal berichten worden geaggregeerd van alle partities. Als een partitie een slechte status heeft, wordt de beschikbaarheids status van de entiteit gerapporteerd als beperkt.
* **Scenario's met weinig volume bericht**: voor dergelijke scenario's, met name wanneer u het HTTP-protocol gebruikt, moet u mogelijk meerdere ontvangst bewerkingen uitvoeren om alle berichten te verkrijgen. Voor ontvangen aanvragen voert de front-end een receive uit op alle partities en worden alle ontvangen antwoorden in de cache opgeslagen. Een volgende receive-aanvraag voor dezelfde verbinding zou profiteren van deze cache en de latentie van ontvangen berichten is lager. Als u echter meerdere verbindingen hebt of HTTP gebruikt, wordt er voor elke aanvraag een nieuwe verbinding tot stand gebracht. Zo is er geen garantie dat deze op hetzelfde knoop punt zou worden gegrond. Als alle bestaande berichten zijn vergrendeld en in de cache worden opgeslagen in een andere front-end, retourneert de receive-bewerking **Null**. Berichten verlopen uiteindelijk en u kunt ze opnieuw ontvangen. HTTP Keep-Alive wordt aanbevolen. Wanneer u partitioneren in scenario's met een laag volume gebruikt, kan het langer duren voordat ontvangst bewerkingen worden uitgevoerd. Daarom wordt u aangeraden geen partitionering te gebruiken in deze scenario's. Verwijder alle bestaande gepartitioneerde entiteiten en maak ze opnieuw met het partitioneren uitgeschakeld om de prestaties te verbeteren.
* **Bladeren/bekijken van berichten**: alleen beschikbaar in de oudere [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -bibliotheek. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) retourneert niet altijd het aantal berichten dat is opgegeven in de eigenschap [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) . Er zijn twee veelvoorkomende redenen voor dit gedrag. Een reden is dat de cumulatieve grootte van de verzameling berichten de maximale grootte van 256 KB overschrijdt. Een andere reden is dat als voor de wachtrij of onderwerp de [eigenschap EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) is ingesteld op **True**, een partitie mogelijk niet genoeg berichten bevat om het gevraagde aantal berichten te volt ooien. In het algemeen, als een toepassing een specifiek aantal berichten wil ontvangen, moet [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) herhaaldelijk worden aangeroepen totdat het aantal berichten wordt opgehaald, of er zijn geen berichten meer om te bekijken. Zie de documentatie voor [QueueClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) of [SubscriptionClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API (Engelstalig) voor meer informatie, waaronder code voorbeelden.

## <a name="latest-added-features"></a>Nieuwste extra functies

* Een regel toevoegen of verwijderen wordt nu ondersteund met gepartitioneerde entiteiten. Andere dan niet-gepartitioneerde entiteiten worden deze bewerkingen niet ondersteund onder trans acties. 
* AMQP wordt nu ondersteund voor het verzenden en ontvangen van berichten van en naar een gepartitioneerde entiteit.
* AMQP wordt nu ondersteund voor de volgende bewerkingen: [batch verzenden](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [batch ontvangen](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [ontvangen op Volg nummer](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek)weer geven [, vergren delen](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [plannings bericht](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [gepland bericht annuleren](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [regel toevoegen](/dotnet/api/microsoft.azure.servicebus.ruledescription), [regel verwijderen](/dotnet/api/microsoft.azure.servicebus.ruledescription), [sessie vernieuwen](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), sessie [status instellen](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), sessie [status ophalen](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)en [sessies opsommen](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Beperkingen van gepartitioneerde entiteiten

Op dit moment Service Bus worden de volgende beperkingen opgelegd voor gepartitioneerde wacht rijen en onderwerpen:

* Gepartitioneerde wacht rijen en onderwerpen worden niet ondersteund in de laag Premium Messa ging. Sessies worden ondersteund in de premier-laag door gebruik te maken van SessionId. 
* Gepartitioneerde wacht rijen en onderwerpen bieden geen ondersteuning voor het verzenden van berichten die deel uitmaken van verschillende sessies in één trans actie.
* Service Bus staat momenteel maximaal 100 gepartitioneerde wachtrijen en onderwerpen per naamruimte toe. Elke gepartitioneerde wachtrij of elk onderwerp telt op het quotum van 10.000 entiteiten per naam ruimte (is niet van toepassing op de Premium-laag).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de basis concepten van de AMQP 1,0 Messa ging-specificatie in de [AMQP 1,0 protocol Guide](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
