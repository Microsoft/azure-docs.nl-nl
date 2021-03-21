---
title: Azure Service Bus-bericht sessies | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u met behulp van sessies gezamenlijke en geordende verwerking van gerelateerde berichten kunt maken.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 6d316571d69d2e1e73ddca4ccca53c116ee8fa5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680750"
---
# <a name="message-sessions"></a>Berichtsessies
Microsoft Azure Service Bus-sessies maken gezamenlijke en geordende verwerking van niet-gebonden reeksen van gerelateerde berichten mogelijk. Sessies kunnen worden gebruikt in de **eerste in, first out (FIFO)** en **aanvraag/antwoord-** patronen. In dit artikel wordt beschreven hoe u met behulp van sessies deze patronen kunt implementeren wanneer u Service Bus gebruikt. 

> [!NOTE]
> De laag basis van Service Bus biedt geen ondersteuning voor sessies. De Standard-en Premium-lagen ondersteunen sessies. Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor verschillen tussen deze lagen.

## <a name="first-in-first-out-fifo-pattern"></a>FIFO-patroon (First-in, first out)
Gebruik sessies om een FIFO-garantie in Service Bus te realiseren. Service Bus is niet een voor Schrift over de aard van de relatie tussen de berichten en definieert ook niet een bepaald model om te bepalen waar een bericht reeks begint of eindigt.

Elke afzender kan een sessie maken bij het indienen van berichten in een onderwerp of wachtrij door de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) in te stellen op een door de toepassing gedefinieerde id die uniek is voor de sessie. Op het AMQP 1,0-protocol niveau wordt deze waarde toegewezen aan de eigenschap *Group-ID* .

In sessie-wacht rijen of-abonnementen kunnen sessies aanwezig zijn wanneer er ten minste één bericht is met de sessie- [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Als er een sessie bestaat, is er geen tijd of API voor wanneer de sessie verloopt of verdwijnt. In theorie kan een bericht worden ontvangen voor een sessie, het volgende bericht in de tijd van het jaar en als de sessie- **id** overeenkomt met het komt overeen met het service bus perspectief.

Normaal gesp roken bevat een toepassing echter een duidelijk begrip van waar een reeks gerelateerde berichten begint en eindigt. Service Bus stelt geen specifieke regels in.

Een voor beeld van het afbakenen van een reeks voor het overdragen van een bestand is het instellen van de **Label** eigenschap voor het eerste bericht dat moet worden **gestart**, voor tussenliggende berichten tot **inhoud** en voor het laatste bericht dat moet worden **beëindigd**. De relatieve positie van de inhouds berichten kan worden berekend als het huidige bericht *SequenceNumber* Delta van het **begin** bericht *SequenceNumber*.

Met de sessie functie in Service Bus kan een specifieke ontvangst bewerking worden uitgevoerd, in de vorm van [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) in de C#-en Java-api's. U schakelt het onderdeel in door de eigenschap [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) in te stellen voor de wachtrij of het abonnement via Azure Resource Manager, of door de vlag in te stellen in de portal. Het is vereist voordat u de gerelateerde API-bewerkingen probeert te gebruiken.

Stel in de Portal de vlag in met het volgende selectie vakje:

![Scherm afbeelding van het dialoog venster wachtrij maken met de optie voor het inschakelen van sessies, geselecteerd en in rood beschreven.][2]

> [!NOTE]
> Wanneer sessies zijn ingeschakeld voor een wachtrij of een abonnement, kunnen de client toepassingen ***geen*** normale berichten meer verzenden/ontvangen. Alle berichten moeten worden verzonden als onderdeel van een sessie (door de sessie-id in te stellen) en ontvangen door de sessie te ontvangen.

De Api's voor sessies bestaan op de wachtrij-en abonnements-clients. Er is een essentieel model dat bepaalt wanneer er sessies en berichten worden ontvangen en een op een handler gebaseerd model, vergelijkbaar met *OnMessage*, waarmee de complexiteit van het beheer van de receive-lus wordt verborgen.

### <a name="session-features"></a>Sessie functies

Sessies bieden gelijktijdige demultiplexing van Interleaved-berichten stromen tijdens het behoud en het garanderen van de bestelde levering.

![Een diagram dat laat zien hoe de sessie-functie de bestelde levering behoudt.][1]

Er wordt een [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -ontvanger gemaakt door de client die een sessie accepteert. De client roept [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) of [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) op in C#. In het reactieve call back-model registreert het een sessie-handler.

Wanneer het [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -object wordt geaccepteerd en terwijl het wordt bewaard door een-client, bevat die client een exclusieve vergren deling voor alle berichten met de [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) van die sessie die voor komt in de wachtrij of het abonnement, en ook op alle berichten met die **SessionID** die nog steeds arriveren terwijl de sessie wordt gehouden.

De vergren deling wordt vrijgegeven wanneer **Close** of **CloseAsync** wordt aangeroepen, of wanneer de vergren deling verloopt in gevallen waarin de toepassing de sluit bewerking niet kan uitvoeren. De sessie vergrendeling moet worden behandeld als een exclusieve vergren deling van een bestand, wat inhoudt dat de toepassing de sessie moet sluiten zodra deze niet meer nodig is en/of geen verdere berichten verwacht.

Wanneer meerdere gelijktijdige ontvangers uit de wachtrij halen, worden de berichten die tot een bepaalde sessie behoren, verzonden naar de specifieke ontvanger die momenteel de vergren deling voor die sessie bevat. Met deze bewerking wordt een Interleaved-berichten stroom in één wachtrij of abonnement schoon gemultiplext voor verschillende ontvangers en kunnen ontvangers ook live op verschillende client computers worden uitgevoerd, omdat het vergrendelings beheer aan de service zijde in Service Bus.

In de vorige afbeelding worden drie sessie ontvangers gelijktijdig weer gegeven. Een sessie met `SessionId` = 4 heeft geen actieve, eigenaarste client, wat betekent dat er geen berichten worden bezorgd vanuit deze specifieke sessie. Een sessie werkt op verschillende manieren als een subwachtrij.

De sessie vergrendeling die wordt gehouden door de sessie ontvanger is een paraplu voor de bericht vergrendelingen die worden gebruikt door de modus voor het weer geven van *vergren delingen* . Er kan slechts één ontvanger een sessie vergren delen. Een ontvanger kan veel in-Flight berichten bevatten, maar de berichten worden in de aangegeven volg orde ontvangen. Het afbreken van een bericht zorgt ervoor dat hetzelfde bericht opnieuw wordt weer gegeven met de volgende ontvangst bewerking.

### <a name="message-session-state"></a>Status van de bericht sessie

Wanneer werk stromen worden verwerkt in Cloud systemen met hoge Beschik baarheid, moet de werk stroom-handler die aan een bepaalde sessie is gekoppeld, kunnen worden hersteld van onverwachte fouten en kan het gedeeltelijk voltooide werk worden hervat op een ander proces of op een andere computer dan waar het werk begon.

De functie sessie status maakt een door de toepassing gedefinieerde aantekening van een bericht sessie binnen de Broker mogelijk, zodat de vastgelegde verwerkings status ten opzichte van die sessie direct beschikbaar wordt wanneer de sessie wordt verkregen door een nieuwe processor.

Vanuit het Service Bus perspectief is de status van de bericht sessie een ondoorzichtig binair object dat gegevens kan bevatten van de grootte van één bericht, 256 KB voor Service Bus standaard en 1 MB voor Service Bus Premium. De verwerkings status ten opzichte van een sessie kan worden vastgehouden binnen de sessie status, of de sessie status kan verwijzen naar een opslag locatie of database record die dergelijke informatie bevat.

De Api's voor het beheren van de sessie status, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) en [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), kunt u vinden in het object [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) in zowel de C#-als Java-api's. Een sessie die eerder geen sessie statusset had, retourneert een **Null** -verwijzing voor **GetState**. Het wissen van de eerder ingestelde sessie status is voltooid met [SetState (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

De sessie status blijft ongewijzigd, omdat deze niet is gewist ( **Null** wordt geretourneerd), zelfs als alle berichten in een sessie worden verbruikt.

Alle bestaande sessies in een wachtrij of abonnement kunnen worden geïnventariseerd met de **SessionBrowser** -methode in de Java API en met [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) op de [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) en [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) in de .NET Framework-client.

De sessie status die is opgeslagen in een wachtrij of in een abonnement, telt de opslag limiet van die entiteit. Wanneer de toepassing is voltooid met een sessie, wordt het aanbevolen om de status van de toepassing op te schonen om te voor komen dat de kosten voor externe beheer worden bespaard.

### <a name="impact-of-delivery-count"></a>Impact van het aantal levering

De definitie van het aantal leveringen per bericht in de context van sessies verschilt enigszins van de definitie in afwezigheid van sessies. Hier volgt een tabel met een samen vatting van het aantal leverings stappen.

| Scenario | Is het aantal leveringen van het bericht verhoogd |
|----------|---------------------------------------------|
| Sessie wordt geaccepteerd, maar de sessie vergrendeling verloopt (vanwege een time-out) | Ja |
| Sessie wordt geaccepteerd, de berichten in de sessie worden niet voltooid (zelfs als ze zijn vergrendeld) en de sessie wordt gesloten | Nee |
| Sessie wordt geaccepteerd, berichten worden voltooid en de sessie wordt vervolgens expliciet gesloten | N.v.t. (het is de standaard stroom). Hier worden berichten uit de sessie verwijderd) |

## <a name="request-response-pattern"></a>Aanvraag-antwoord patroon
Het [patroon aanvraag/antwoord](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) is een goed opgezet integratie patroon waarmee de toepassing Sender een aanvraag kan verzenden en biedt de ontvanger een manier om een antwoord te verzenden naar de toepassing van de afzender. Voor dit patroon is doorgaans een wachtrij of onderwerp vereist voor het verzenden van antwoorden naar de toepassing. In dit scenario bieden sessies een eenvoudige alternatieve oplossing met vergelijk bare semantiek. 

Meerdere toepassingen kunnen hun aanvragen verzenden naar één aanvraag wachtrij, waarbij een specifieke header parameter is ingesteld op unieke identificatie van de afzender toepassing. De ontvanger van de toepassing kan de aanvragen verwerken die in de wachtrij binnenkomen en reacties verzenden op de wachtrij met ingeschakelde sessies, waarbij de sessie-ID wordt ingesteld op de unieke id die de afzender heeft verzonden op het aanvraag bericht. De toepassing die de aanvraag heeft verzonden, kan vervolgens berichten ontvangen over de specifieke sessie-ID en de antwoorden op de juiste manier verwerken.

> [!NOTE]
> De toepassing die de eerste aanvragen verzendt, moet weten over de sessie-ID en gebruiken `SessionClient.AcceptMessageSession(SessionID)` om de sessie te vergren delen waarop het antwoord wordt verwacht. Het is een goed idee om een GUID te gebruiken die het exemplaar van de toepassing uniek identificeert als een sessie-id. Er mag geen sessie-handler of `AcceptMessageSession(timeout)` in de wachtrij staan om ervoor te zorgen dat antwoorden beschikbaar zijn om te worden vergrendeld en verwerkt door specifieke ontvangers.

## <a name="next-steps"></a>Volgende stappen

- Zie de voor beelden van [micro soft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) of [micro soft. ServiceBus. Messa ging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) voor een voor beeld waarin de .NET Framework-client wordt gebruikt voor het afhandelen van sessie bewuste berichten. 

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
