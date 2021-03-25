---
title: Verifiëren bij Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de verschillende manieren waarop een app of service kan worden geverifieerd bij communicatie Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110862"
---
# <a name="authenticate-to-azure-communication-services"></a>Verifiëren bij Azure Communication Services

Elke client interactie met Azure Communication Services moet worden geverifieerd. In een typische architectuur, Zie [client-en server architectuur](./client-and-server-architecture.md), *toegangs sleutels* of *beheerde identiteiten* worden gebruikt voor verificatie.

Een ander type verificatie maakt gebruik van *tokens voor gebruikers toegang* voor het verifiëren van services die gebruikers deelname vereisen. Bijvoorbeeld: de chat-of oproep service maakt gebruik van *tokens voor gebruikers toegang* om gebruikers toe te voegen in een thread en met elkaar gesp rekken te laten lopen.

## <a name="authentication-options"></a>Verificatie opties

In de volgende tabel ziet u de Azure Communication Services Sdk's en de bijbehorende verificatie opties:

| SDK    | Verificatie optie                               |
| ----------------- | ----------------------------------------------------|
| Identiteit          | Toegangs sleutel of beheerde identiteit                      |
| Sms               | Toegangs sleutel of beheerde identiteit                      |
| Telefoon nummers     | Toegangs sleutel of beheerde identiteit                      |
| Aanroepen           | Token voor gebruikers toegang                                   |
| Chat              | Token voor gebruikers toegang                                   |

Elke autorisatie optie wordt hieronder beschreven:

### <a name="access-key"></a>Toegangssleutel

Toegangs sleutel verificatie is geschikt voor service toepassingen die worden uitgevoerd in een vertrouwde service omgeving. Uw toegangs sleutel vindt u in de Azure Communication Services-portal. De service toepassing gebruikt deze als referentie voor het initialiseren van de bijbehorende Sdk's. Bekijk een voor beeld van hoe het wordt gebruikt in de [identiteits-SDK](../quickstarts/access-tokens.md). 

Omdat de toegangs sleutel deel uitmaakt van de connection string van uw resource, is verificatie met een connection string gelijk aan verificatie met een toegangs sleutel.

Als u ACS-Api's hand matig wilt aanroepen met een toegangs sleutel, dan moet u de aanvraag ondertekenen. Het ondertekenen van de aanvraag wordt gedetailleerd uitgelegd in een [zelf studie](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Beheerde identiteit

Beheerde identiteiten bieden superieure beveiliging en gebruiks gemak over andere autorisatie opties. Met Azure AD kunt u bijvoorbeeld voor komen dat u uw account toegangs sleutel in uw code opslaat, zoals u dat wel doet met toegangs sleutel autorisatie. Hoewel u de toegangs sleutel verificatie met communicatie Services-toepassingen kunt blijven gebruiken, raadt micro soft u waar mogelijk naar Azure AD te verplaatsen. 

Als u een beheerde identiteit wilt instellen, [maakt u een geregistreerde toepassing vanuit de Azure cli](../quickstarts/managed-identity-from-cli.md). Het eind punt en de referenties kunnen vervolgens worden gebruikt voor het verifiëren van de Sdk's. Zie voor beelden van de manier waarop [beheerde identiteit](../quickstarts/managed-identity.md) wordt gebruikt.

### <a name="user-access-tokens"></a>Tokens voor gebruikers toegang

Tokens voor gebruikers toegang worden gegenereerd met de identiteits-SDK en zijn gekoppeld aan gebruikers die zijn gemaakt in de identiteits-SDK. Bekijk een voor beeld van het [maken van gebruikers en het genereren van tokens](../quickstarts/access-tokens.md). Vervolgens worden gebruikers toegangs tokens gebruikt om deel nemers te verifiëren die zijn toegevoegd aan conversaties in de chat-of aanroepende SDK. Zie [Chat toevoegen aan uw app](../quickstarts/chat/get-started.md)voor meer informatie. Verificatie van tokens voor gebruikers toegang verschilt in vergelijking met toegangs sleutel en beheerde identiteits verificatie in dat wordt gebruikt voor de verificatie van een gebruiker in plaats van een beveiligde Azure-resource.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Communicatie services-resources](../quickstarts/create-communication-resource.md) 
>  maken en beheren [Een Azure Active Directory beheerde identiteits toepassing maken vanuit Azure cli](../quickstarts/managed-identity-from-cli.md) 
>  [Tokens voor gebruikers toegang maken](../quickstarts/access-tokens.md)

Raadpleeg voor meer informatie de volgende artikelen:
- [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
