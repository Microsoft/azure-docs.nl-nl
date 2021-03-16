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
ms.openlocfilehash: 83976ed9d6f80b6c785cb84e74a0755472f9579f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561801"
---
# <a name="authenticate-to-azure-communication-services"></a>Verifiëren bij Azure Communication Services

Elke client interactie met Azure Communication Services moet worden geverifieerd. In een typische architectuur, Zie [client-en server architectuur](./client-and-server-architecture.md), *toegangs sleutels* of *beheerde identiteiten* worden gebruikt voor verificatie.

Een ander type verificatie maakt gebruik van *tokens voor gebruikers toegang* voor het verifiëren van services die gebruikers deelname vereisen. Bijvoorbeeld: de chat-of oproep service maakt gebruik van *tokens voor gebruikers toegang* om gebruikers toe te voegen in een thread en met elkaar gesp rekken te laten lopen.

## <a name="authentication-options"></a>Verificatie opties:

De volgende tabel bevat de client bibliotheken van Azure Communication Services en de bijbehorende verificatie opties:

| Client bibliotheek    | Verificatie optie                               |
| ----------------- | ----------------------------------------------------|
| Identiteit          | Toegangs sleutel of beheerde identiteit                      |
| Sms               | Toegangs sleutel of beheerde identiteit                      |
| Telefoon nummers     | Toegangs sleutel of beheerde identiteit                      |
| Aanroepen           | Token voor gebruikers toegang                                   |
| Chat              | Token voor gebruikers toegang                                   |

Elke autorisatie optie wordt hieronder beschreven:

- **Toegangs sleutel** verificatie is geschikt voor service toepassingen die worden uitgevoerd in een vertrouwde service omgeving. De toegangs sleutel vindt u in de Azure Communication Services-portal en de service toepassing gebruikt deze als referentie voor het initialiseren van de bijbehorende client bibliotheken. Bekijk een voor beeld van hoe het wordt gebruikt in de [identiteits-client bibliotheek](../quickstarts/access-tokens.md). Omdat de toegangs sleutel deel uitmaakt van de connection string van uw resource, is verificatie met een connection string gelijk aan verificatie met een toegangs sleutel.

- **Beheerde identiteits** verificatie biedt superieure beveiliging en gebruiks gemak over andere autorisatie opties. Met Azure AD kunt u bijvoorbeeld voor komen dat u uw account toegangs sleutel opslaat met uw code, zoals u dat wel doet met toegangs sleutel autorisatie. Hoewel u de toegangs sleutel verificatie met communicatie Services-toepassingen kunt blijven gebruiken, raadt micro soft u waar mogelijk naar Azure AD te verplaatsen. Als u een beheerde identiteit wilt instellen, [maakt u een geregistreerde toepassing vanuit de Azure cli](../quickstarts/managed-identity-from-cli.md). Het eind punt en de referenties kunnen vervolgens worden gebruikt voor het verifiëren van de client bibliotheken. Zie voor beelden van de manier waarop [beheerde identiteit](../quickstarts/managed-identity.md) wordt gebruikt.

- **Tokens voor gebruikers toegang** worden gegenereerd met behulp van de identiteits-client bibliotheek en zijn gekoppeld aan gebruikers die zijn gemaakt in de identiteits-client bibliotheek. Bekijk een voor beeld van het [maken van gebruikers en het genereren van tokens](../quickstarts/access-tokens.md). Vervolgens worden gebruikers toegangs tokens gebruikt om deel nemers te verifiëren die zijn toegevoegd aan conversaties in de chat-of aanroepende SDK. Zie [Chat toevoegen aan uw app](../quickstarts/chat/get-started.md)voor meer informatie. Verificatie van tokens voor gebruikers toegang verschilt in vergelijking met toegangs sleutel en beheerde identiteits verificatie in dat wordt gebruikt voor de verificatie van een gebruiker in plaats van een beveiligde Azure-resource.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Communicatie services-resources](../quickstarts/create-communication-resource.md) 
>  maken en beheren [Een Azure Active Directory beheerde identiteits toepassing maken vanuit Azure cli](../quickstarts/managed-identity-from-cli.md) 
>  [Tokens voor gebruikers toegang maken](../quickstarts/access-tokens.md)

Raadpleeg voor meer informatie de volgende artikelen:
- [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
