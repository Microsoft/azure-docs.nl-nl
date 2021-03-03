---
title: Verifiëren bij Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de verschillende manieren waarop een app of service kan worden geverifieerd bij communicatie Services.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1267fc53bd6dcbae504b01610267059545353dc5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655901"
---
# <a name="authenticate-to-azure-communication-services"></a>Verifiëren bij Azure Communication Services

Elke client interactie met Azure Communication Services moet worden geverifieerd. In een typische architectuur, Zie [client-en server architectuur](./client-and-server-architecture.md), *toegangs sleutels* of *beheerde identiteit* worden gebruikt in de service voor vertrouwde gebruikers toegang voor het maken van gebruikers en tokens voor uitgifte. Het *token voor gebruikers toegang* dat is uitgegeven door de service voor vertrouwde gebruikers toegang wordt gebruikt voor client toepassingen om toegang te krijgen tot andere communicatie Services, bijvoorbeeld chat of Call service.

De SMS-service van Azure Communication Services accepteert ook *toegangs sleutels* of *beheerde identiteit* voor authenticatie. Dit gebeurt meestal in een service toepassing die wordt uitgevoerd in een vertrouwde service omgeving.

Elke autorisatie optie wordt hieronder beschreven:

- **Toegang tot sleutel** verificatie voor SMS-en identiteits bewerkingen. Toegangs sleutel verificatie is geschikt voor service toepassingen die worden uitgevoerd in een vertrouwde service omgeving. U vindt de toegangs sleutel in de Azure Communication Services-portal. Voor verificatie met een toegangs sleutel gebruikt een service toepassing de toegangs sleutel als referentie voor het initialiseren van overeenkomende SMS-of Identity client-bibliotheken. Zie [toegangs tokens maken en beheren](../quickstarts/access-tokens.md). Omdat de toegangs sleutel deel uitmaakt van de connection string van uw resource, raadpleegt u [bronnen voor communicatie Services maken en beheren](../quickstarts/create-communication-resource.md), de verificatie met Connection String is gelijk aan de verificatie met de toegangs sleutel.
- **Beheerde identiteits** verificatie voor SMS-en identiteits bewerkingen. Beheerde identiteit, Zie [beheerde identiteit](../quickstarts/managed-identity.md), is geschikt voor service toepassingen die worden uitgevoerd in een vertrouwde service omgeving. Voor verificatie met een beheerde identiteit maakt een service toepassing een referentie met de id en een geheim van de beheerde identiteit en initialiseert u vervolgens de bijbehorende SMS-of Identity client-bibliotheken. Zie [toegangs tokens maken en beheren](../quickstarts/access-tokens.md).
- Verificatie van **gebruikers toegangs token** voor chat en aanroepen. Met tokens voor gebruikers toegang kunnen uw client toepassingen verifiëren tegen Azure Communication chat en services aanroepen. Deze tokens worden gegenereerd in een ' vertrouwde gebruikers toegangs service ' die u maakt. Ze worden vervolgens door gegeven aan client apparaten die het token gebruiken voor het initialiseren van de chat en het aanroepen van client bibliotheken. Zie voor meer informatie [Chat toevoegen aan uw app](../quickstarts/chat/get-started.md) .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Communicatie services-resources](../quickstarts/create-communication-resource.md) 
>  maken en beheren [Een Azure Active Directory beheerde identiteits toepassing maken vanuit Azure cli](../quickstarts/managed-identity-from-cli.md) 
>  [Tokens voor gebruikers toegang maken](../quickstarts/access-tokens.md)

Raadpleeg voor meer informatie de volgende artikelen:
- [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
