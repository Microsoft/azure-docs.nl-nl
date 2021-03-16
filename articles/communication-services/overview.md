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
ms.openlocfilehash: 40dacdf243505447e9831239ef1252602a3e0f99
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491687"
---
# <a name="what-is-azure-communication-services"></a>Wat is Azure Communication Services?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

> [!IMPORTANT]
> Toepassingen die u met Azure Communication Services bouwt, kunnen communiceren met micro soft teams. Ga voor meer informatie naar onze [teams Interop](./quickstarts/voice-video-calling/get-started-teams-interop.md) -documentatie.


Met Azure Communication Services kunt u eenvoudig realtime multimediafuncties voor spraak, video en IP-telefonie toevoegen aan uw toepassingen. De Communication Services-clientbibliotheken bieden u ook de mogelijkheid om chat- en sms-functionaliteit toe te voegen aan uw communicatie-oplossingen.

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

De volgende resources zijn een goed startpunt als u geen ervaring hebt met Azure Communication Services:
<br>

| Resource                               |Beschrijving                           |
|---                                    |---                                   |
|**[Voeg u aanroepende app toe aan een Teams-meeting](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services kan worden gebruikt voor het bouwen van aangepaste vergaderervaringen die communiceren met Microsoft Teams. Gebruikers van uw oplossingen voor communicatie Services kunnen communiceren met teams deel nemers over spraak, video, chatten en het delen van het scherm.|
|**[Een Communication Services-resource maken](./quickstarts/create-communication-resource.md)**|U kunt Azure Communication Services gaan gebruiken met behulp van de client bibliotheek Azure Portal of communicatie Services om uw eerste communicatie Services-resource in te richten. Zodra u de verbindingsreeks van uw Communication Services-resource hebt, kunt u uw eerste toegangstokens voor gebruikers inrichten.|
|**[Uw eerste toegangstokens voor gebruikers maken](./quickstarts/access-tokens.md)**|Toegangstokens voor gebruikers worden gebruikt om services te verifiëren bij uw Azure Communication Services-resource. Deze tokens zijn ingericht en opnieuw uitgegeven met de communicatie Services-client bibliotheek.|
|**[Een telefoonnummer aanvragen](./quickstarts/telephony-sms/get-phone-number.md)**|U kunt Azure Communication Services gebruiken om telefoonnummers in te richten en uit te voeren. Deze telefoon nummers kunnen worden gebruikt om uitgaande oproepen te initiëren en oplossingen voor SMS-communicatie te bouwen.|
|**[Een SMS-bericht verzenden vanuit uw app](./quickstarts/telephony-sms/send.md)**|Met de SMS-clientbibliotheek van Azure Communication Services kunt u SMS-berichten verzenden en ontvangen van uw .NET-en Javascript-toepassingen.|
|**[Aan de slag met spraak- en videogesprekken](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Met Azure Communication Services kunt u spraak- en videogesprekken aan uw apps toevoegen met behulp van de clientbibliotheek Oproepen. Deze bibliotheek werkt op basis van WebRTC en stelt u in staat om peer-to-peer, met multimedia en realtime te communiceren binnen uw toepassing.|
|**[Aan de slag met chat](./quickstarts/chat/get-started.md)**|De Chat-clientbibliotheek van Azure Communication Services kunnen worden gebruikt om uitgebreide realtime chatmogelijkheden in uw toepassingen in te integreren.|


## <a name="samples"></a>Voorbeelden

In de volgende voorbeelden wordt end-to-end gebruik van de Azure Communication Services-clientbibliotheken gedemonstreerd. U kunt deze voorbeelden gebruiken om uw eigen Communication Services-oplossingen te bootstrappen.
<br>

| Voorbeeldnaam                               | Description                           |
|---                                    |---                                   |
|**[Hero-voorbeeld van groepsgesprek](./samples/calling-hero-sample.md)**|Ontdek hoe de Communication Services-clientbibliotheken kunnen worden gebruikt om een ervaring voor groepsgesprekken te ontwerpen.|
|**[Het Hero-voorbeeld van groepschat](./samples/chat-hero-sample.md)**|Ontdek hoe de Communication Services-clientbibliotheken kunnen worden gebruikt om een ervaring voor groepschats te ontwerpen.|


## <a name="platforms-and-client-libraries"></a>Platformen en clientbibliotheken

In de volgende resources vindt u meer informatie over de Azure Communication Services-clientbibliotheken:

| Resource                               | Beschrijving                           |
|---                                    |---                                   |
|**[Clientbibliotheken en REST API's](./concepts/sdk-options.md)**|De mogelijkheden van Azure Communication Services zijn conceptueel ingedeeld in zes gebieden, die elk worden vertegenwoordigd door een clientbibliotheek. U kunt bepalen welke clientbibliotheken u wilt gebruiken op basis van uw realtime communicatiebehoeften.|
|**[Overzicht van de oproepen-clientbibliotheek](./concepts/voice-video-calling/calling-sdk-features.md)**|Bekijk het overzicht van de Communication Services-oproepen-clientbibliotheek.|
|**[Overzicht van de chat-clientbibliotheek](./concepts/chat/sdk-features.md)**|Bekijk het overzicht van de Communication Services-chat-clientbibliotheek.|
|**[Overzicht van de sms-clientbibliotheek](./concepts/telephony-sms/sdk-features.md)**|Bekijk het overzicht van de Communication Services-sms-clientbibliotheek.|

## <a name="compare-azure-communication-services"></a>Azure Communication Services vergelijken

Er zijn twee andere communicatieproducten van Microsoft die u zou kunnen gebruiken, en die momenteel niet rechtstreeks compatibel zijn met Communication Services:

 - [Microsoft Graph Cloud Communication API's](/graph/cloud-communications-concept-overview) stellen organisaties in staat om communicatie-ervaringen te bouwen die gekoppeld zijn aan Azure Active Directory-gebruikers met M365-licenties. Dit is ideaal voor toepassingen die zijn gekoppeld aan Azure Active Directory of wanneer u productiviteitservaringen in Microsoft Teams wilt uitbreiden. Er zijn ook API's voor het ontwerpen van toepassingen en aanpassingen in de [Teams-ervaring.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Met Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) kunt u gemakkelijker chat- en gegevenscommunicatie met lage latentie toevoegen aan games. Hoewel u Communication Services kunt gebruiken voor chatten en netwerksystemen, biedt PlayFab een op maat gemaakte optie die gratis is op Xbox.


## <a name="next-steps"></a>Volgende stappen

 - [Een Communication Services-resource maken](./quickstarts/create-communication-resource.md)
