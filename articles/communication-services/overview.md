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
ms.openlocfilehash: 0efdf48e78d0cc48e288bea354f5de5f9635c760
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106837"
---
# <a name="what-is-azure-communication-services"></a>Wat is Azure Communication Services?

> [!IMPORTANT]
> Toepassingen die u met Azure Communication Services bouwt, kunnen communiceren met micro soft teams. Ga voor meer informatie naar onze [teams Interop](./quickstarts/voice-video-calling/get-started-teams-interop.md) -documentatie.


Met Azure Communication Services kunt u eenvoudig realtime multimediafuncties voor spraak, video en IP-telefonie toevoegen aan uw toepassingen. Met de SDK-bibliotheken voor communicatie Services kunt u ook chat-en SMS-functionaliteit toevoegen aan uw communicatie oplossingen.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

U kunt Communication Services gebruiken voor spraak-, video-, tekst- en gegevenscommunicatie in verschillende scenario's:

- Browser-naar-browser-, browser-naar-app-en app-naar-app-communicatie
- Gebruikers die communiceren met bots of andere services
- Gebruikers en bots die communiceren over het openbaar telefoonnetwerk

Gemengde scenario's worden ondersteund. Een Communication Services-toepassing kan het bijvoorbeeld mogelijk maken voor gebruikers om tegelijkertijd via browsers en traditionele telefoonapparaten te spreken. Communicatie Services kan ook gecombineerd worden met Azure Bot Service om door bots aangestuurde interactieve telefoonbeantwoordingssystemen te bouwen.

## <a name="common-scenarios"></a>Algemene scenario's

De volgende resources zijn een goede plaats om aan de slag te gaan met Azure Communication Services.
<br>

| Resource                               |Beschrijving                           |
|---                                    |---                                   |
|**[Een Communication Services-resource maken](./quickstarts/create-communication-resource.md)**|U kunt Azure Communication Services gaan gebruiken met behulp van de SDK van Azure Portal of Communication Services om uw eerste communicatie Services-resource in te richten. Zodra u de verbindingsreeks van uw Communication Services-resource hebt, kunt u uw eerste toegangstokens voor gebruikers inrichten.|
|**[Een telefoonnummer aanvragen](./quickstarts/telephony-sms/get-phone-number.md)**|U kunt Azure Communication Services gebruiken om telefoonnummers in te richten en uit te voeren. Deze telefoon nummers kunnen worden gebruikt om uitgaande oproepen te initiëren en oplossingen voor SMS-communicatie te bouwen.|

Nadat u een communicatie Services-resource hebt gemaakt, kunt u beginnen met het bouwen van client scenario's, zoals spraak-en video gesprekken of chat tekst.

| Resource                               |Beschrijving                           |
|---                                    |---                                   |
|**[Uw eerste toegangstokens voor gebruikers maken](./quickstarts/access-tokens.md)**|Toegangstokens voor gebruikers worden gebruikt om services te verifiëren bij uw Azure Communication Services-resource. Deze tokens zijn ingericht en opnieuw uitgegeven met de Communication Services SDK.|
|**[Aan de slag met spraak- en videogesprekken](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Met Azure Communication Services kunt u spraak-en video gesprekken aan uw apps toevoegen met behulp van de aanroepende SDK. Deze bibliotheek werkt op basis van WebRTC en stelt u in staat om peer-to-peer, met multimedia en realtime te communiceren binnen uw toepassing.|
|**[Voeg u aanroepende app toe aan een Teams-meeting](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services kan worden gebruikt voor het bouwen van aangepaste vergaderervaringen die communiceren met Microsoft Teams. Gebruikers van uw oplossingen voor communicatie Services kunnen communiceren met teams deel nemers over spraak, video, chatten en het delen van het scherm.|
|**[Aan de slag met chat](./quickstarts/chat/get-started.md)**|De Azure Communication Services chat SDK kan worden gebruikt om realtime chatten te integreren in uw toepassingen.|
|**[Een SMS-bericht verzenden vanuit uw app](./quickstarts/telephony-sms/send.md)**|Met de SMS SDK van Azure Communication Services kunt u SMS-berichten verzenden en ontvangen van uw .NET-en Java script-toepassingen.|

## <a name="samples"></a>Voorbeelden

In de volgende voor beelden wordt end-to-end-gebruik van de SDK-bibliotheken van Azure Communication Services gedemonstreerd. U kunt deze voorbeelden gebruiken om uw eigen Communication Services-oplossingen te bootstrappen.
<br>

| Voorbeeldnaam                               | Description                           |
|---                                    |---                                   |
|**[Hero-voorbeeld van groepsgesprek](./samples/calling-hero-sample.md)**|Zie hoe de SDK-bibliotheken voor communicatie Services kunnen worden gebruikt voor het bouwen van een groep.|
|**[Het Hero-voorbeeld van groepschat](./samples/chat-hero-sample.md)**|Zie hoe de SDK-bibliotheken voor communicatie Services kunnen worden gebruikt om een groeps ervaring voor groepen te maken.|


## <a name="platforms-and-sdk-libraries"></a>Platforms en SDK-bibliotheken

In de volgende bronnen vindt u meer informatie over de SDK-bibliotheken van Azure Communication Services:

| Resource                               | Beschrijving                           |
|---                                    |---                                   |
|**[SDK-bibliotheken en REST-Api's](./concepts/sdk-options.md)**|De mogelijkheden van Azure Communication Services zijn conceptueel ingedeeld in zes gebieden, die allemaal worden vertegenwoordigd door een SDK. U kunt bepalen welke SDK-bibliotheken u wilt gebruiken op basis van uw realtime communicatie behoeften.|
|**[Overzicht van de SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Raadpleeg de communicatie Services Calling SDK Overview (Engelstalig).|
|**[Overzicht van chat-SDK](./concepts/chat/sdk-features.md)**|Bekijk het overzicht van de communicatie services-Chat-SDK.|
|**[SMS-SDK-overzicht](./concepts/telephony-sms/sdk-features.md)**|Bekijk het overzicht van de SMS SDK voor communicatie Services.|

## <a name="other-microsoft-communication-services"></a>Andere micro soft-communicatie Services

Er zijn twee andere communicatieproducten van Microsoft die u zou kunnen gebruiken, en die momenteel niet rechtstreeks compatibel zijn met Communication Services:

 - Met [Microsoft Graph Cloud Communication api's](/graph/cloud-communications-concept-overview) kunnen organisaties communicatie ervaringen bouwen die zijn gekoppeld aan Azure Active Directory gebruikers met Microsoft 365-licenties. Dit is ideaal voor toepassingen die zijn gekoppeld aan Azure Active Directory of wanneer u productiviteitservaringen in Microsoft Teams wilt uitbreiden. Er zijn ook API's voor het ontwerpen van toepassingen en aanpassingen in de [Teams-ervaring.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Met Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) kunt u gemakkelijker chat- en gegevenscommunicatie met lage latentie toevoegen aan games. Hoewel u Communication Services kunt gebruiken voor chatten en netwerksystemen, biedt PlayFab een op maat gemaakte optie die gratis is op Xbox.


## <a name="next-steps"></a>Volgende stappen

 - [Een Communication Services-resource maken](./quickstarts/create-communication-resource.md)
