---
title: Azure Service Bus-bericht sessies | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u met behulp van sessies gezamenlijke en geordende verwerking van gerelateerde berichten kunt maken.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: c9a1c4fdccbbc8b38805e23d4895448959126f10
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308469"
---
# <a name="message-sessions"></a>Berichtsessies
Microsoft Azure Service Bus-sessies maken gezamenlijke en geordende verwerking van niet-gebonden reeksen van gerelateerde berichten mogelijk. Sessies kunnen worden gebruikt in de **eerste in, first out (FIFO)** en **aanvraag/antwoord-** patronen. In dit artikel wordt beschreven hoe u met behulp van sessies deze patronen kunt implementeren wanneer u Service Bus gebruikt. 

> [!NOTE]
> De laag basis van Service Bus biedt geen ondersteuning voor sessies. De Standard-en Premium-lagen ondersteunen sessies. Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor verschillen tussen deze lagen.

## <a name="first-in-first-out-fifo-pattern"></a>FIFO-patroon (First-in, first out)
Gebruik sessies om een FIFO-garantie in Service Bus te realiseren. Service Bus is niet een voor Schrift over de aard van de relatie tussen de berichten en definieert ook niet een bepaald model om te bepalen waar een bericht reeks begint of eindigt.

Elke afzender kan een sessie maken bij het indienen van berichten in een onderwerp of wachtrij door de eigenschap **sessie-id** in te stellen op een door de toepassing gedefinieerde id die uniek is voor de sessie. Op het AMQP 1,0-protocol niveau wordt deze waarde toegewezen aan de eigenschap *Group-ID* .

In sessie-wacht rijen of-abonnementen zijn sessies aanwezig wanneer er ten minste één bericht met de sessie-ID is. Als er een sessie bestaat, is er geen tijd of API voor wanneer de sessie verloopt of verdwijnt. Theoretisch kan een bericht worden ontvangen voor een sessie van vandaag, het volgende bericht in de tijd van het jaar en als de sessie-ID overeenkomt, is de sessie hetzelfde als in het Service Bus perspectief.

Normaal gesp roken bevat een toepassing echter een duidelijk begrip van waar een reeks gerelateerde berichten begint en eindigt. Service Bus stelt geen specifieke regels in. Uw toepassing kan bijvoorbeeld de eigenschap **Label** instellen voor het eerste bericht dat moet worden **gestart**, voor tussenliggende berichten tot **inhoud** en voor het laatste bericht dat moet worden **beëindigd**. De relatieve positie van de inhouds berichten kan worden berekend als het huidige bericht *SequenceNumber* Delta van het **begin** bericht *SequenceNumber*.

U schakelt het onderdeel in door de eigenschap [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) in te stellen voor de wachtrij of het abonnement via Azure Resource Manager, of door de vlag in te stellen in de portal. Het is vereist voordat u de gerelateerde API-bewerkingen probeert te gebruiken.

In de portal kunt u sessies inschakelen tijdens het maken van een entiteit (wachtrij of abonnement), zoals wordt weer gegeven in de volgende voor beelden. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Sessie inschakelen op het moment dat de wachtrij wordt gemaakt":::

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Sessie inschakelen op het moment dat het abonnement wordt gemaakt":::


> [!IMPORTANT]
> Wanneer sessies zijn ingeschakeld voor een wachtrij of een abonnement, kunnen de client toepassingen ***geen*** normale berichten meer verzenden/ontvangen. Alle berichten moeten worden verzonden als onderdeel van een sessie (door de sessie-id in te stellen) en worden ontvangen door de sessie te accepteren.

De Api's voor sessies bestaan op de wachtrij-en abonnements-clients. Er is een essentieel model dat bepaalt wanneer er sessies en berichten worden ontvangen en een op een handler gebaseerd model waarmee de complexiteit van het beheer van de receive-lus wordt verborgen. 

Voor voor beelden gebruikt u de koppelingen in de sectie [volgende stappen](#next-steps) . 

### <a name="session-features"></a>Sessie functies

Sessies bieden gelijktijdige demultiplexing van Interleaved-berichten stromen tijdens het behoud en het garanderen van de bestelde levering.

![Een diagram dat laat zien hoe de sessie-functie de bestelde levering behoudt.][1]

Een sessie ontvanger wordt gemaakt door een client die een sessie accepteert. Wanneer de sessie wordt geaccepteerd en bewaard door een client, bevat de client een exclusieve vergren deling voor alle berichten met de **sessie-id** van die sessie in de wachtrij of het abonnement. Het bevat ook exclusieve vergren delingen op alle berichten met de **sessie-id** die later wordt bereikt.

De vergren deling wordt vrijgegeven wanneer u de aan het sluiten gerelateerde methoden aanroept op de ontvanger of wanneer de vergren deling verloopt. Er zijn methoden op de ontvanger om ook de vergren delingen te vernieuwen. In plaats daarvan kunt u de functie voor het vernieuwen van automatische vergren deling gebruiken, waarbij u de tijds duur kunt opgeven waarvoor u de vergren deling wilt blijven verlengen. De sessie vergrendeling moet worden behandeld als een exclusieve vergren deling van een bestand, wat inhoudt dat de toepassing de sessie moet sluiten zodra deze niet meer nodig is en/of geen verdere berichten verwacht.

Wanneer meerdere gelijktijdige ontvangers uit de wachtrij halen, worden de berichten die tot een bepaalde sessie behoren, verzonden naar de specifieke ontvanger die momenteel de vergren deling voor die sessie bevat. Met deze bewerking wordt een Interleaved-berichten stroom in één wachtrij of abonnement schoon gemultiplext voor verschillende ontvangers en kunnen ontvangers ook live op verschillende client computers worden uitgevoerd, omdat het vergrendelings beheer aan de service zijde in Service Bus.

In de vorige afbeelding worden drie sessie ontvangers gelijktijdig weer gegeven. Een sessie met `SessionId` = 4 heeft geen actieve, eigenaarste client, wat betekent dat er geen berichten worden bezorgd vanuit deze specifieke sessie. Een sessie werkt op verschillende manieren als een subwachtrij.

De sessie vergrendeling die wordt gehouden door de sessie ontvanger is een paraplu voor de bericht vergrendelingen die worden gebruikt door de modus voor het weer geven van *vergren delingen* . Er kan slechts één ontvanger een sessie vergren delen. Een ontvanger kan veel in-Flight berichten bevatten, maar de berichten worden in de aangegeven volg orde ontvangen. Het afbreken van een bericht zorgt ervoor dat hetzelfde bericht opnieuw wordt weer gegeven met de volgende ontvangst bewerking.

### <a name="message-session-state"></a>Status van de bericht sessie

Wanneer werk stromen worden verwerkt in Cloud systemen met hoge Beschik baarheid, moet de werk stroom-handler die aan een bepaalde sessie is gekoppeld, kunnen worden hersteld van onverwachte fouten en kan het gedeeltelijk voltooide werk worden hervat op een ander proces of op een andere computer dan waar het werk begon.

De functie sessie status maakt een door de toepassing gedefinieerde aantekening van een bericht sessie binnen de Broker mogelijk, zodat de vastgelegde verwerkings status ten opzichte van die sessie direct beschikbaar wordt wanneer de sessie wordt verkregen door een nieuwe processor.

Vanuit het Service Bus perspectief is de status van de bericht sessie een ondoorzichtig binair object dat gegevens kan bevatten van de grootte van één bericht, 256 KB voor Service Bus standaard en 1 MB voor Service Bus Premium. De verwerkings status ten opzichte van een sessie kan worden vastgehouden binnen de sessie status, of de sessie status kan verwijzen naar een opslag locatie of database record die dergelijke informatie bevat.

De methoden voor het beheren van de sessie status, SetState en GetState, vindt u op het sessie-receiver-object. Een sessie die eerder geen sessie status had, retourneert een null-verwijzing voor GetState. De eerder ingestelde sessie status kan worden gewist door null door te geven aan de SetState-methode op de ontvanger.

De sessie status blijft ongewijzigd, omdat deze niet is gewist ( **Null** wordt geretourneerd), zelfs als alle berichten in een sessie worden verbruikt.

De sessie status die is opgeslagen in een wachtrij of in een abonnement, telt de opslag limiet van die entiteit. Wanneer de toepassing is voltooid met een sessie, wordt het aanbevolen om de status van de toepassing op te schonen om te voor komen dat de kosten voor externe beheer worden bespaard.

### <a name="impact-of-delivery-count"></a>Impact van het aantal levering

De definitie van het aantal leveringen per bericht in de context van sessies verschilt enigszins van de definitie in afwezigheid van sessies. Hier volgt een tabel met een samen vatting van het aantal leverings stappen.

| Scenario | Is het aantal leveringen van het bericht verhoogd |
|----------|---------------------------------------------|
| Sessie wordt geaccepteerd, maar de sessie vergrendeling verloopt (vanwege een time-out) | Yes |
| Sessie wordt geaccepteerd, de berichten in de sessie worden niet voltooid (zelfs als ze zijn vergrendeld) en de sessie wordt gesloten | No |
| Sessie wordt geaccepteerd, berichten worden voltooid en de sessie wordt vervolgens expliciet gesloten | N.v.t. (het is de standaard stroom). Hier worden berichten uit de sessie verwijderd) |

## <a name="request-response-pattern"></a>Aanvraag-antwoord patroon
Het [patroon aanvraag/antwoord](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) is een goed opgezet integratie patroon waarmee de toepassing Sender een aanvraag kan verzenden en biedt de ontvanger een manier om een antwoord te verzenden naar de toepassing van de afzender. Voor dit patroon is doorgaans een wachtrij of onderwerp vereist voor het verzenden van antwoorden naar de toepassing. In dit scenario bieden sessies een eenvoudige alternatieve oplossing met vergelijk bare semantiek. 

Meerdere toepassingen kunnen hun aanvragen verzenden naar één aanvraag wachtrij, waarbij een specifieke header parameter is ingesteld op unieke identificatie van de afzender toepassing. De ontvanger van de toepassing kan de aanvragen verwerken die in de wachtrij binnenkomen en reacties verzenden op de wachtrij met ingeschakelde sessies, waarbij de sessie-ID wordt ingesteld op de unieke id die de afzender heeft verzonden op het aanvraag bericht. De toepassing die de aanvraag heeft verzonden, kan vervolgens berichten ontvangen over de specifieke sessie-ID en de antwoorden op de juiste manier verwerken.

> [!NOTE]
> De toepassing die de eerste aanvragen verzendt, moet weten over de sessie-ID en deze gebruiken om de sessie te accepteren, zodat de sessie waarop de reactie wordt verwacht, wordt vergrendeld. Het is een goed idee om een GUID te gebruiken die het exemplaar van de toepassing uniek identificeert als een sessie-id. Er mag geen sessie-handler of een time-out zijn opgegeven op de sessie ontvanger voor de wachtrij om ervoor te zorgen dat antwoorden beschikbaar zijn om te worden vergrendeld en verwerkt door specifieke ontvangers.

## <a name="next-steps"></a>Volgende stappen

- [Azure. Messa ging. ServiceBus-voor beelden voor .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Azure Service Bus-client bibliotheek voor Java-voor beelden](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-client bibliotheek voor python-voor beelden](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-client bibliotheek voor Java script-voor beelden](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus-client bibliotheek voor type script-voor beelden](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Micro soft. Azure. ServiceBus-voor beelden voor .net (voor beelden van](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) sessies en SessionState)  

Zie [service bus wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md)voor meer informatie over Service Bus berichten.

[1]: ./media/message-sessions/sessions.png
