---
title: Interoperabiliteit van Teams-vergadering
titleSuffix: An Azure Communication Services concept document
description: Deelnemen aan Teams-vergaderingen
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 29eafcae9442215e23e80b946fc35314e23100d3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937233"
---
# <a name="teams-interoperability"></a>Interoperabiliteit met Teams

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services kan worden gebruikt voor het bouwen van aangepaste vergaderervaringen die communiceren met Microsoft Teams. Gebruikers van uw Communication Services-oplossing(en) kunnen communiceren met Teams-deelnemers via spraak, video en scherm delen.

Met deze interoperabiliteit kunt u aangepaste Azure-toepassingen maken waarmee gebruikers worden verbonden met Teams-vergaderingen. Gebruikers van uw aangepaste toepassingen hoeven geen Azure Active Directory-identiteiten of Teams-licenties te hebben om deze functie te kunnen ervaren. Dit is ideaal voor het samenbrengen van werknemers (die mogelijk bekend zijn met Teams) en externe gebruikers (met behulp van een aangepaste toepassingservaring) in een naadloze vergaderervaring. Op die manier kunt u ervaringen bouwen zoals de volgende:

1. Werknemers gebruiken Teams om een vergadering te plannen
2. Uw aangepaste Communication Services-toepassing maakt gebruik van de Microsoft Graph-API's voor toegang tot de vergaderingsgegevens
3. Vergaderingsgegevens worden gedeeld met externe gebruikers via uw aangepaste toepassing
4. Externe gebruikers gebruiken uw aangepaste toepassing om deel te nemen aan de Teams-vergadering (via de Aanroepende clientbibliotheek van Communication Services)

De architectuur op hoog niveau voor deze gebruikers-case ziet er als volgt uit: 

![Architectuur voor Teams-interop](..//media/call-flows/teams-interop.png)

Hoewel bepaalde Teams-vergaderingsfuncties zoals opgestoken hand, samen-modus en aparte vergaderruimtes alleen beschikbaar zullen zijn voor Teams-gebruikers, heeft uw aangepaste toepassing wel toegang tot de kernfunctionaliteit van de vergadering voor audio, video en scherm delen.

Wanneer een Communication Services-gebruiker deelneemt aan de Teams-vergadering, wordt de weergavenaam die via de Aanroepende clientbibliotheek wordt verschaft, weergegeven voor Teams-gebruikers. De Communication Services-gebruiker wordt anders behandeld als een anonieme gebruiker in Teams. Uw aangepaste toepassing moet rekening houden met gebruikersverificatie en andere veiligheidsmaatregelen om Teams vergaderingen te beveiligen. Houd rekening met de beveiligingsimplicaties van het deel laten nemen van anonieme gebruikers aan vergaderingen, en gebruik de [Beveiligingshandleiding van Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) om de mogelijkheden te configureren die beschikbaar zijn voor anonieme gebruikers.

Gebruikers van Communication Services kunnen deelnemen aan geplande Teams-vergaderingen, zolang anonieme deelnames zijn ingeschakeld in de [instellingen van vergaderingen](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Teams in Government Clouds (GCC)
Interoperabiliteit tussen Azure Communication Services en Teams-implementaties met behulp van [Microsoft 365 Government Clouds (GCC)](/MicrosoftTeams/plan-for-government-gcc) is op dit moment niet toegestaan. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Voeg u aanroepende app toe aan een Teams-meeting](../../quickstarts/voice-video-calling/get-started-teams-interop.md)