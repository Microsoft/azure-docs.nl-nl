---
title: Asynchrone berichten Service Bus | Microsoft Docs
description: Meer informatie over hoe Azure Service Bus asynchronism ondersteunt via een archief-en doorstuur mechanisme met wacht rijen, onderwerpen en abonnementen.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e37c18b95bca7ef1e6e8f0d74976bb73b214624a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500628"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchrone bericht patronen en hoge Beschik baarheid

Asynchrone berichten kunnen op verschillende manieren worden geïmplementeerd. Met wacht rijen, onderwerpen en abonnementen ondersteunt Azure Service Bus asynchronism via een mechanisme voor opslaan en door sturen. Bij een normale (synchrone) bewerking verzendt u berichten naar wacht rijen en onderwerpen en ontvangt u berichten van wacht rijen en abonnementen. Toepassingen die u schrijft, zijn afhankelijk van deze entiteiten die altijd beschikbaar zijn. Wanneer de status van de entiteit wordt gewijzigd vanwege diverse omstandigheden, hebt u een manier nodig om een beperkte functionaliteits entiteit te bieden die aan de meeste behoeften kan voldoen.

Toepassingen gebruiken meestal asynchrone bericht patronen om een aantal communicatie scenario's mogelijk te maken. U kunt toepassingen bouwen waarin clients berichten kunnen verzenden naar Services, zelfs wanneer de service niet wordt uitgevoerd. Voor toepassingen die bursts-communicatie ervaren, kan een wachtrij de belasting helpen inverdelen door een plek voor buffer communicatie te bieden. Ten slotte kunt u een eenvoudige, maar effectief load balancer krijgen om berichten over meerdere computers te verdelen.

Als u de beschik baarheid van een van deze entiteiten wilt behouden, moet u rekening houden met een aantal verschillende manieren waarop deze entiteiten niet beschikbaar kunnen worden gesteld voor een duurzaam berichten systeem. In het algemeen zien we dat de entiteit niet meer beschikbaar is voor toepassingen die we op de volgende verschillende manieren schrijven:

* Kan geen berichten verzenden.
* Kan geen berichten ontvangen.
* Entiteiten kunnen niet worden beheerd (maken, ophalen, bijwerken of verwijderen van entiteiten).
* Kan geen verbinding maken met de service.

Voor elk van deze fouten bestaan er verschillende fout modi die ervoor zorgen dat een toepassing op een zekere mate van verminderde functionaliteit nog meer werk kan uitvoeren. Een systeem dat bijvoorbeeld berichten kan verzenden, maar niet ontvangen, kan nog steeds orders van klanten ontvangen, maar kan deze orders niet verwerken. In dit onderwerp worden mogelijke problemen beschreven die zich kunnen voordoen en hoe deze problemen worden verholpen. Service Bus heeft een aantal beperkende maat regelen geïntroduceerd waarbij u zich moet aanmelden. in dit onderwerp worden ook de regels besproken voor het gebruik van deze opt-in-oplossingen.

## <a name="reliability-in-service-bus"></a>Betrouw baarheid in Service Bus
Er zijn verschillende manieren voor het afhandelen van bericht-en entiteits problemen en er zijn richt lijnen voor het juiste gebruik van deze oplossingen. Om inzicht te krijgen in de richt lijnen, moet u eerst begrijpen wat er kan mislukken in Service Bus. Vanwege het ontwerp van Azure Systems moeten al deze problemen kort worden bewaard. Op hoog niveau worden de verschillende oorzaken van niet-beschik baarheid als volgt weer gegeven:

* Beperking van een extern systeem waarop Service Bus afhankelijk is. Beperking wordt veroorzaakt door interacties met opslag-en reken resources.
* Probleem voor een systeem waarvan Service Bus afhankelijk is. Bijvoorbeeld, een bepaald deel van de opslag kan problemen ondervinden.
* Fout bij het Service Bus van één subsysteem. In deze situatie kan een reken knooppunt een inconsistente status krijgen en moet het knoop punt opnieuw worden opgestart, waardoor alle entiteiten de taak verdeling naar andere knoop punten. Dit kan een korte periode van trage bericht verwerking veroorzaken.
* Uitval van Service Bus in een Azure-Data Center. Dit is een ' onherstelbare fout ' waarbij het systeem gedurende enkele minuten of enkele uren onbereikbaar is.

> [!NOTE]
> De term **opslag** kan zowel Azure Storage als SQL Azure betekenen.
> 
> 

Service Bus bevat een aantal beperkingen voor deze problemen. In de volgende secties worden elk probleem en de bijbehorende oplossingen besproken.

### <a name="throttling"></a>Beperking
Met Service Bus kunt u met beperking het beheer van de gezamenlijke bericht snelheid inschakelen. Elk afzonderlijk Service Bus knoop punt is een groot aantal entiteiten. Elk van deze entiteiten voert de vereisten op het systeem uit met betrekking tot CPU, geheugen, opslag en andere facetten. Wanneer een van deze facetten het gebruik detecteert dat de gedefinieerde drempel waarden overschrijdt, kan Service Bus een bepaalde aanvraag weigeren. De aanroeper ontvangt een [ServerBusyException][ServerBusyException] en nieuwe pogingen na 10 seconden.

Als oplossing moet de code de fout lezen en alle pogingen van het bericht gedurende ten minste tien seconden stoppen. Aangezien de fout kan optreden in delen van de klant toepassing, wordt ervan uitgegaan dat elk stuk de pogings logica onafhankelijk uitvoert. De code kan de waarschijnlijkheid verminderen door partitioneren in te scha kelen voor een wachtrij of onderwerp.

### <a name="issue-for-an-azure-dependency"></a>Probleem voor een Azure-afhankelijkheid
Andere onderdelen in azure kunnen af en toe Service problemen ondervinden. Wanneer een systeem dat Service Bus gebruikt, bijvoorbeeld wordt bijgewerkt, kan dat systeem tijdelijk minder mogelijkheden ondervinden. Om dit soort problemen te omzeilen, Service Bus regel matig onderzoeken en oplossingen implementeren. De neven effecten van deze oplossingen worden weer gegeven. Als u bijvoorbeeld tijdelijke problemen met opslag wilt afhandelen, implementeert Service Bus een systeem waarmee het verzenden van berichten consistent kan worden uitgevoerd. Vanwege de aard van de oplossing kan een verzonden bericht tot wel 15 minuten worden weer gegeven in de betrokken wachtrij of het abonnement en klaar zijn voor een ontvangst bewerking. Over het algemeen hebben de meeste entiteiten geen last van dit probleem. Gezien het aantal entiteiten in Service Bus in azure, is deze oplossing soms nodig voor een kleine subset van Service Bus klanten.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus fout op één subsysteem
In geval van elke toepassing kan een intern onderdeel van Service Bus inconsistent worden. Als Service Bus dit detecteert, worden gegevens uit de toepassing verzameld om te helpen bij het vaststellen van de oorzaak van het probleem. Zodra de gegevens zijn verzameld, wordt de toepassing opnieuw gestart in een poging deze te herstellen naar een consistente status. Dit proces vindt redelijk snel plaats en de resultaten van een entiteit die niet langer dan een paar minuten beschikbaar is, zijn echter veel korter.

In deze gevallen genereert de client toepassing een uitzonde ring [System. TimeoutException][System.TimeoutException] of [MessagingException][MessagingException] . Service Bus bevat een oplossing voor dit probleem in de vorm van een automatische pogings logica van de client. Zodra de periode voor opnieuw proberen is uitgeput en het bericht niet is bezorgd, kunt u de andere procedure in het artikel verkennen met betrekking tot het [afhandelen van storingen en rampen][handling outages and disasters].

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van asynchrone berichten in Service Bus hebt geleerd, leest u meer informatie over het [afhandelen van storingen en rampen][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[handling outages and disasters]: service-bus-outages-disasters.md