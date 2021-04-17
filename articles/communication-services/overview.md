---
title: Wat is Azure Communication Services?
description: Ontdek hoe u met Azure Communication Services uitgebreide gebruikerservaringen kunt ontwikkelen met communicatie in real time.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588102"
---
# <a name="what-is-azure-communication-services"></a>Wat is Azure Communication Services?

Azure Communication Services kunt u eenvoudig realtime spraak-, video- en telefooncommunicatie toevoegen aan uw toepassingen. Communication Services SDK's kunt u ook sms-functionaliteit toevoegen aan uw communicatieoplossingen. Azure Communication Services is identiteitsagnostisch en u hebt volledige controle over hoe eindgebruikers worden geïdentificeerd en geverifieerd. U kunt mensen verbinden met het communicatiegegevensvlak of services (bots).

Toepassingen zijn onder andere:

- **Business to Consumer (B2C).** Werknemers en services van een bedrijf kunnen communiceren met consumenten met behulp van spraak-, video- en rich text-chat in een aangepaste browser of mobiele toepassing. Een organisatie kan sms-berichten verzenden en ontvangen, of een Interactive Voice Response System (IVR) gebruiken met behulp van een telefoonnummer dat u via Azure verkrijgt. [Dankzij integratie met Microsoft Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md) kunnen consumenten deelnemen aan Teams-vergaderingen die worden gehost door werknemers; ideaal voor scenario's voor externe gezondheidszorg, bankieren en productondersteuning waarbij werknemers mogelijk al bekend zijn met Teams.
- **Consument naar consument.** Bouw aantrekkelijke sociale ruimten voor interactie van gebruiker tot consument met spraak-, video- en rich text-chat. Elk type gebruikersinterface kan worden gebouwd op Azure Communication Services SDK's, maar er zijn volledige toepassingsvoorbeelden en UI-assets beschikbaar om u snel aan de slag te helpen.

Bekijk onze Microsoft [Mechanics-video](https://www.youtube.com/watch?v=apBX7ASurgM) of de onderstaande resources voor meer informatie.

## <a name="common-scenarios"></a>Algemene scenario's

<br>

| Resource                               |Beschrijving                           |
|---                                    |---                                   |
|**[Een Communication Services-resource maken](./quickstarts/create-communication-resource.md)**|Begin met Azure Communication Services met behulp van de Azure Portal of Communication Services SDK om uw eerste resource in Communication Services in te stellen. Zodra u de verbindingsreeks van uw Communication Services-resource hebt, kunt u uw eerste toegangstokens voor gebruikers inrichten.|
|**[Een telefoonnummer aanvragen](./quickstarts/telephony-sms/get-phone-number.md)**|U kunt Azure Communication Services gebruiken om telefoonnummers in te richten en uit te voeren. Deze telefoonnummers kunnen worden gebruikt voor het initiëren of ontvangen van telefoongesprekken en het bouwen van sms-oplossingen.|
|**[Een SMS-bericht verzenden vanuit uw app](./quickstarts/telephony-sms/send.md)**|De Azure Communication Services SMS SDK wordt gebruikt voor het verzenden en ontvangen van sms-berichten van servicetoepassingen.|

Nadat u een Communication Services resource hebt gemaakt, kunt u beginnen met het bouwen van clientscenario's, zoals spraak- en videogesprekken of tekstchat:

| Resource                               |Beschrijving                           |
|---                                    |---                                   |
|**[Uw eerste toegangstokens voor gebruikers maken](./quickstarts/access-tokens.md)**|Tokens voor gebruikerstoegang worden gebruikt om clients te verifiëren aan de hand van Azure Communication Services resource. Deze tokens worden ingericht en opnieuw uitgegeven met behulp van de Communication Services SDK.|
|**[Aan de slag met spraak- en videogesprekken](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services kunt u spraak- en video-oproepen toevoegen aan uw browser of systeemeigen apps met behulp van de aanroepen van de SDK. |
|**[Voeg u aanroepende app toe aan een Teams-meeting](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services kan worden gebruikt voor het bouwen van aangepaste vergaderervaringen die communiceren met Microsoft Teams. Gebruikers van uw Communication Services kunnen communiceren met Teams-deelnemers via spraak, video, chat en schermdeling.|
|**[Aan de slag met chat](./quickstarts/chat/get-started.md)**|De Azure Communication Services Chat SDK wordt gebruikt om uitgebreide realtime tekstchat toe te voegen aan uw toepassingen.|

## <a name="samples"></a>Voorbeelden

In de volgende voorbeelden wordt het end-to-end-gebruik van de Azure Communication Services. Gebruik deze voorbeelden om uw eigen Communication Services bootstrap.
<br>

| Voorbeeldnaam                               | Description                           |
|---                                    |---                                   |
|**[Hero-voorbeeld van groepsgesprek](./samples/calling-hero-sample.md)**| Download een ontworpen toepassingsvoorbeeld voor groepsroepen voor browsers, iOS- en Android-apparaten. |
|**[Het Hero-voorbeeld van groepschat](./samples/chat-hero-sample.md)**| Download een ontworpen toepassingsvoorbeeld voor groepstekstchat voor browsers. |


## <a name="platforms-and-sdk-libraries"></a>Platforms en SDK-bibliotheken

Meer informatie over de Azure Communication Services SDK's vindt u in de onderstaande resources. REST API's zijn beschikbaar voor de meeste functionaliteit als u uw eigen clients wilt bouwen of op een andere manier toegang wilt krijgen tot de service via internet.

| Resource                               | Beschrijving                           |
|---                                    |---                                   |
|**[SDK-bibliotheken en REST API's](./concepts/sdk-options.md)**|Azure Communication Services zijn conceptueel ingedeeld in zes gebieden, die elk worden vertegenwoordigd door een SDK. U kunt bepalen welke SDK-bibliotheken u wilt gebruiken op basis van uw realtime communicatiebehoeften.|
|**[Overzicht van het aanroepen van de SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Bekijk het Communication Services Calling SDK( Overzicht van de SDK voor oproepen).|
|**[Overzicht van chat-SDK](./concepts/chat/sdk-features.md)**|Bekijk het overzicht Communication Services Chat SDK.|
|**[Overzicht van sms-SDK](./concepts/telephony-sms/sdk-features.md)**|Bekijk het overzicht Communication Services SMS SDK.|

## <a name="other-microsoft-communication-services"></a>Andere Microsoft Communication Services

Er zijn twee andere Microsoft-communicatieproducten die u kunt overwegen te gebruiken en die op dit moment niet rechtstreeks Communication Services zijn:

 - [Microsoft Graph Cloud Communication-API's](/graph/cloud-communications-concept-overview) kunnen organisaties communicatie-ervaringen bouwen die zijn gekoppeld aan Azure Active Directory gebruikers met Microsoft 365 licenties. Dit is ideaal voor toepassingen die zijn gekoppeld aan Azure Active Directory of wanneer u productiviteitservaringen in Microsoft Teams wilt uitbreiden. Er zijn ook API's voor het ontwerpen van toepassingen en aanpassingen in de [Teams-ervaring.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Met Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) kunt u gemakkelijker chat- en gegevenscommunicatie met lage latentie toevoegen aan games. Hoewel u Communication Services kunt gebruiken voor chatten en netwerksystemen, biedt PlayFab een op maat gemaakte optie die gratis is op Xbox.


## <a name="next-steps"></a>Volgende stappen

 - [Een Communication Services-resource maken](./quickstarts/create-communication-resource.md)
