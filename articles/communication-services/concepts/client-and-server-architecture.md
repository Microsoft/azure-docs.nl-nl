---
title: Architectuur van client en server
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de architectuur van communicatie Services.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 27f444c6889767e0333384a779883805d1e8f348
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485907"
---
# <a name="client-and-server-architecture"></a>Client-en server architectuur

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Elke Azure Communication Services-toepassing heeft **client toepassingen** die gebruikmaken van **Services** voor het vergemakkelijken van de connectiviteit van personen naar personen. Op deze pagina ziet u algemene architecturale elementen in verschillende scenario's.

## <a name="user-access-management"></a>Beheer van gebruikers toegang

Voor client bibliotheken van Azure Communication Services moet `user access tokens` u beveiligde communicatie Services-bronnen gebruiken. `User access tokens` moet worden gegenereerd en beheerd door een vertrouwde service vanwege de gevoelige aard van het token en de connection string die nodig zijn om ze te genereren. Fout bij het goed beheren van toegangs tokens kan leiden tot extra kosten als gevolg van misbruik van resources. Het wordt sterk aanbevolen om een vertrouwde service voor gebruikers beheer te gebruiken. De vertrouwde service genereert de tokens en geeft deze door aan de client met behulp van de juiste versleuteling. Hieronder vindt u een voor beeld van een architectuur stroom:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagram waarin de architectuur van de gebruikers toegangs token wordt weer gegeven.":::

Bekijk de [best mogelijke procedures voor identiteits beheer](../../security/fundamentals/identity-management-best-practices.md) voor meer informatie.

## <a name="browser-communication"></a>Browser communicatie

Azure Communications java script-client bibliotheken kunnen webtoepassingen inschakelen met de interactie van tekst, spraak en video. De toepassing communiceert rechtstreeks met Azure Communication Services via de client bibliotheek om toegang te krijgen tot het gegevens vlak en om in realtime tekst, spraak en video communicatie te leveren. Hieronder vindt u een voor beeld van een architectuur stroom:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagram waarin de browser wordt weer gegeven voor de browser architectuur voor communicatie Services.":::

## <a name="native-app-communication"></a>Systeem eigen app-communicatie

Veel scenario's worden het beste geleverd met systeem eigen toepassingen. Azure Communication Services ondersteunt zowel browser-naar-app-als app-to-app-communicatie.  Bij het bouwen van een systeem eigen toepassing, met push meldingen kunnen gebruikers oproepen ontvangen, zelfs wanneer de toepassing niet wordt uitgevoerd. Met Azure Communication Services kunt u eenvoudig geïntegreerde push meldingen verzenden naar Google Firebase, Apple Push Notification Service en Windows push meldingen. Hieronder vindt u een voor beeld van een architectuur stroom:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagram waarin de communicatie Services-architectuur voor systeem eigen app-communicatie wordt weer gegeven.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Spraak-en SMS-verkeer via het open bare telefoon netwerk (PSTN)

Door te communiceren via het telefoon systeem kan het bereik van uw toepassing aanzienlijk toenemen. Ter ondersteuning van PSTN-spraak-en SMS-scenario's kunt u met Azure Communication Services [telefoon nummers](../quickstarts/telephony-sms/get-phone-number.md) rechtstreeks van de Azure Portal ophalen of rest-api's en client bibliotheken gebruiken. Zodra de telefoon nummers zijn verkregen, kunnen ze worden gebruikt om klanten te bereiken met behulp van PSTN-aanroepen en SMS-berichten in zowel binnenkomende als uitgaande scenario's. Hieronder vindt u een voor beeld van een architectuur stroom:

> [!Note]
> Tijdens de open bare preview is de inrichting van telefoon nummers in de Verenigde Staten beschikbaar voor klanten met facturerings adressen die zich binnen de Verenigde Staten en Canada bevinden.

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagram dat de PSTN-architectuur van communicatie Services weergeeft.":::

Zie voor meer informatie over PSTN-telefoon nummers [telefoon nummer typen](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>Mensen die communiceren met bots en andere services

Azure Communication Services ondersteunt Human-to-System-communicatie, met tekst-en spraak kanalen, met services die rechtstreeks toegang hebben tot het gegevens vlak van Azure Communication Services. U kunt bijvoorbeeld een bot laten beantwoorden van binnenkomende telefoon gesprekken of deel nemen aan een web-chat. Azure Communication Services biedt client bibliotheken die deze scenario's mogelijk maken voor aanroepen en chatten. Hieronder vindt u een voor beeld van een architectuur stroom:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagram van de architectuur van de communicatie Services-bot.":::

## <a name="networking"></a>Netwerken

Mogelijk wilt u wille keurige gegevens uitwisselen tussen gebruikers, bijvoorbeeld om een gedeelde gemengde realiteit of gaming ervaring te synchroniseren. Het realtime gegevens vlak dat wordt gebruikt voor tekst-, spraak-en video communicatie, is op twee manieren beschikbaar voor u:

- **Client bibliotheek aanroepen** : apparaten in een aanroep hebben toegang tot api's voor het verzenden en ontvangen van gegevens via het aanroep kanaal. Dit is de eenvoudigste manier om gegevens communicatie toe te voegen aan een bestaande interactie.
- **Stun/turn** : Azure Communication Services maakt standaarden compatibel met stun en schakelt services beschikbaar voor u. Hierdoor kunt u een sterk aangepaste transport laag maken boven op deze gestandaardiseerde primitieven. U kunt uw eigen standaard client ontwerpen of gebruikmaken van open-source bibliotheken zoals [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Tokens voor gebruikerstoegang maken](../quickstarts/access-tokens.md)

Raadpleeg voor meer informatie de volgende artikelen:

- Meer informatie over [verificatie](../concepts/authentication.md)
- Meer informatie over [telefoon nummer typen](../concepts/telephony-sms/plan-solution.md)

- [Chat aan uw app toevoegen](../quickstarts/chat/get-started.md)
- [Spraakoproep aan uw app toevoegen](../quickstarts/voice-video-calling/getting-started-with-calling.md)
