---
title: Azure Service Bus berichtensessies | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u sessies gebruikt om gezamenlijke en geordende verwerking van niet-gebonden reeksen gerelateerde berichten mogelijk te maken.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: e22dfb2aa7372a227f70fd2bfa8f72d2161cda17
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750748"
---
# <a name="message-sessions"></a>Berichtsessies
Microsoft Azure Service Bus maken gezamenlijke en geordende verwerking van niet-gebonden reeksen gerelateerde berichten mogelijk. Sessies kunnen worden gebruikt in **de patronen first in, first out (FIFO)** en **request-response.** In dit artikel wordt beschreven hoe u sessies gebruikt om deze patronen te implementeren bij het gebruik van Service Bus. 

> [!NOTE]
> De basislaag van Service Bus biedt geen ondersteuning voor sessies. De Standard- en Premium-lagen ondersteunen sessies. Zie Prijzen voor Service Bus [verschillen tussen deze lagen.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="first-in-first-out-fifo-pattern"></a>FIFO-patroon (First-in, first out)
Als u een FIFO-garantie wilt realiseren in Service Bus, gebruikt u sessies. Service Bus is niet prescriptief over de aard van de relatie tussen de berichten en definieert ook geen bepaald model om te bepalen waar een berichtenreeks begint of eindigt.

Elke afzender kan een sessie maken bij het verzenden  van berichten naar een onderwerp of wachtrij door de eigenschap sessie-id in te stellen op een toepassings-gedefinieerde id die uniek is voor de sessie. Op het niveau van het AMQP 1.0-protocol wordt deze waarde toe te staan aan de *eigenschap group-id.*

Bij sessiebewuste wachtrijen of abonnementen ontstaan sessies wanneer er ten minste één bericht met de sessie-id is. Zodra een sessie bestaat, is er geen gedefinieerde tijd of API voor wanneer de sessie verloopt of verdwijnt. Theoretisch gezien kan er vandaag een bericht worden ontvangen voor een sessie, het volgende bericht binnen een jaar en als de sessie-id overeenkomt, is de sessie hetzelfde vanuit het Service Bus perspectief.

Normaal gesproken heeft een toepassing echter een duidelijk beeld van waar een set gerelateerde berichten begint en eindigt. Service Bus stelt geen specifieke regels in. Uw toepassing kan bijvoorbeeld de eigenschap **Label** instellen voor het eerste bericht om te **starten,** voor tussenliggende berichten naar inhoud **en** voor het laatste bericht om te **eindigen.** De relatieve positie van de inhoudsberichten kan worden berekend als het huidige bericht *SequenceNumber* delta van het **startbericht** *SequenceNumber.*

> [!IMPORTANT]
> Wanneer Sessies zijn ingeschakeld voor een wachtrij of  een abonnement, kunnen de clienttoepassingen geen reguliere berichten meer verzenden/ontvangen. Alle berichten moeten worden verzonden als onderdeel van een sessie (door de sessie-id in te stellen) en ontvangen door de sessie te accepteren.

De API's voor sessies bestaan op wachtrij- en abonnements-clients. Er is een imperatieve model dat bepaalt wanneer sessies en berichten worden ontvangen, en een op handler gebaseerd model dat de complexiteit van het beheren van de ontvangstlus verbergt. 

Gebruik voor voorbeelden koppelingen in de [sectie Volgende](#next-steps) stappen. 

### <a name="session-features"></a>Sessiefuncties

Sessies bieden gelijktijdige de-multiplexing van interleaved berichtstromen met behoud en garantie voor geordende levering.

![Een diagram dat laat zien hoe de functie Sessies geordende levering behoudt.][1]

Een sessieontvanger wordt gemaakt door een client die een sessie accepteert. Wanneer de sessie wordt geaccepteerd en wordt gehouden door een client, houdt de client een exclusieve vergrendeling op alle berichten met de **sessie-id** van die sessie in de wachtrij of het abonnement. Het bevat ook exclusieve vergrendelingen voor alle berichten met de **sessie-id** die later wordt ontvangen.

De vergrendeling wordt vrijgegeven wanneer u de verwante methoden voor sluiten aanroept op de ontvanger of wanneer de vergrendeling verloopt. Er zijn ook methoden voor de ontvanger om de vergrendelingen te vernieuwen. In plaats daarvan kunt u de functie voor automatische vergrendelingsvernieuwing gebruiken, waarbij u de tijdsduur kunt opgeven waarvoor u de vergrendeling steeds wilt vernieuwen. De sessievergrendeling moet worden behandeld als een exclusieve vergrendeling van een bestand, wat betekent dat de toepassing de sessie moet sluiten zodra deze niet meer nodig is en/of geen verdere berichten verwacht.

Wanneer meerdere gelijktijdige ontvangers uit de wachtrij halen, worden de berichten van een bepaalde sessie verzonden naar de specifieke ontvanger die momenteel de vergrendeling voor die sessie bevat. Met deze bewerking wordt een interleavede berichtenstroom in één wachtrij of abonnement op een schone manier uit multiplexed naar verschillende ontvangers en kunnen deze ontvangers ook op verschillende clientmachines worden geplaatst, omdat het vergrendelingsbeheer aan de servicezijde gebeurt, binnen Service Bus.

In de vorige afbeelding ziet u drie gelijktijdige sessieontvangers. Eén sessie met = 4 heeft geen actieve client die eigenaar is, wat betekent dat er geen berichten `SessionId` worden bezorgd vanuit deze specifieke sessie. Een sessie fungeert op veel manieren als een subwachtrij.

De sessievergrendeling die door de sessieontvanger wordt vastgehouden, is een overkoepelende oplossing voor de berichtvergrendelingen die worden gebruikt door de *afwikkelingsmodus peek-lock.* Slechts één ontvanger kan een sessie vergrendelen. Een ontvanger kan veel in-flight-berichten hebben, maar de berichten worden op volgorde ontvangen. Het verlaten van een bericht zorgt ervoor dat hetzelfde bericht opnieuw wordt weergegeven bij de volgende ontvangstbewerking.

### <a name="message-session-state"></a>Status van berichtsessie

Wanneer werkstromen worden verwerkt in grootschalige cloudsystemen met hoge beschikbaarheid, moet de werkstroom-handler die aan een bepaalde sessie is gekoppeld, in staat zijn om onverwachte fouten te herstellen en kan gedeeltelijk voltooid werk op een ander proces of op een andere computer worden hervat vanaf het begin van het werk.

De sessietoestandfaciliteit maakt een toepassingsdefininieerde aantekening van een berichtsessie binnen de broker mogelijk, zodat de vastgelegde verwerkingstoestand ten opzichte van die sessie direct beschikbaar wordt wanneer de sessie wordt verkregen door een nieuwe processor.

Vanuit het Service Bus perspectief is de status van de berichtsessie een ondoorzichtig binair object dat gegevens van de grootte van één bericht kan bevatten. Dit is 256 kB voor Service Bus Standard en 1 MB voor Service Bus Premium. De verwerkingstoestand ten opzichte van een sessie kan binnen de sessietoestand worden gehouden, of de sessietoestand kan wijzen op een opslaglocatie of databaserecord die dergelijke informatie bevat.

De methoden voor het beheren van de sessietoestand, SetState en GetState, vindt u op het object sessieontvanger. Een sessie die eerder geen sessietoestand had, retourneert een null-verwijzing voor GetState. De eerder ingesteld sessietoestand kan worden geweerd door null door te geven aan de Methode SetState op de ontvanger.

De sessietoestand blijft zolang deze niet wordt geweerd (null wordt **geretourneerd),** zelfs als alle berichten in een sessie worden gebruikt.

De sessietoestand in een wachtrij of in een abonnement telt mee voor het opslagquotum van die entiteit. Wanneer de toepassing is voltooid met een sessie, wordt het daarom aanbevolen dat de toepassing de behouden status opschoont om externe beheerkosten te voorkomen.

### <a name="impact-of-delivery-count"></a>Impact van het aantal leveringen

De definitie van het aantal leveringen per bericht in de context van sessies verschilt enigszins van de definitie als er geen sessies zijn. Hier is een tabel waarin wordt samengevat wanneer het aantal leveringen wordt verhoogd.

| Scenario | Is het aantal leveringen van het bericht verhoogd? |
|----------|---------------------------------------------|
| De sessie wordt geaccepteerd, maar de sessievergrendeling verloopt (vanwege een time-out) | Yes |
| Sessie wordt geaccepteerd, de berichten in de sessie worden niet voltooid (zelfs als ze zijn vergrendeld) en de sessie wordt gesloten | No |
| Sessie wordt geaccepteerd, berichten worden voltooid en vervolgens wordt de sessie expliciet gesloten | N.v.t. (Dit is de standaardstroom. Hier worden berichten verwijderd uit de sessie) |

## <a name="request-response-pattern"></a>Patroon Aanvraag-antwoord
Het [aanvraag-antwoordpatroon](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) is een goed opgezet integratiepatroon waarmee de afzendertoepassing een aanvraag kan verzenden en een manier biedt voor de ontvanger om een antwoord correct terug te sturen naar de afzendertoepassing. Dit patroon heeft doorgaans een korte wachtrij of een onderwerp nodig waar de toepassing antwoorden op kan verzenden. In dit scenario bieden sessies een eenvoudige alternatieve oplossing met vergelijkbare semantiek. 

Meerdere toepassingen kunnen hun aanvragen verzenden naar één aanvraagwachtrij, met een specifieke headerparameter ingesteld om de zezendertoepassing uniek te identificeren. De ontvangertoepassing kan de aanvragen verwerken die in de wachtrij komen en antwoorden verzenden in de wachtrij met sessie ingeschakeld, waarbij de sessie-id wordt instellen op de unieke id die de afzender heeft verzonden op het aanvraagbericht. De toepassing die de aanvraag heeft verzonden, kan vervolgens berichten ontvangen op de specifieke sessie-id en de antwoorden correct verwerken.

> [!NOTE]
> De toepassing die de eerste aanvragen verzendt, moet de sessie-id kennen en deze gebruiken om de sessie te accepteren, zodat de sessie waarop het antwoord wordt verwacht, is vergrendeld. Het is een goed idee om een GUID te gebruiken die het exemplaar van de toepassing uniek identificeert als een sessie-id. Er mag geen sessie-handler of time-out zijn opgegeven op de sessie-ontvanger voor de wachtrij om ervoor te zorgen dat antwoorden kunnen worden vergrendeld en verwerkt door specifieke ontvangers.

## <a name="next-steps"></a>Volgende stappen
U kunt berichtsessies inschakelen tijdens het maken van een wachtrij met Azure Portal, PowerShell, CLI, Resource Manager sjabloon, .NET, Java, Python en JavaScript. Zie Berichtsessies inschakelen [voor meer informatie.](enable-message-sessions.md) 

Probeer de voorbeelden in de taal van uw keuze om de Azure Service Bus verkennen. 

- [Azure Service Bus-clientbibliotheekvoorbeelden voor Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus clientbibliotheekvoorbeelden voor TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Voorbeelden van Azure.Messaging.ServiceBus voor .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Zoek voorbeelden voor de oudere .NET- en Java-clientbibliotheken hieronder:
- [Microsoft.Azure.ServiceBus-voorbeelden voor .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

