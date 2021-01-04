---
title: Aan de slag met Teams-interop op Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In deze quickstart leert u hoe u kunt deelnemen aan een Teams-vergadering met de chatclientbibliotheek in Azure Communication
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea66e4295e8228aa382aa29a46fcca8147dcbc98
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578017"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Quickstart: Voeg u chat-app toe aan een Teams-meeting

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ga aan de slag met Azure Communication Services door uw chatoplossing te verbinden met Microsoft Teams, met behulp van de JavaScript-clientbibliotheek. 

## <a name="prerequisites"></a>Vereisten 

1. Een  [Teams-implementatie](https://docs.microsoft.com/deployoffice/teams-install). 
2. Een werkende [chat-app](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Teams-interoperabiliteit inschakelen 

Wanneer Communication Services-gebruikers als gastgebruiker deelnemen aan een Teams-vergadering, hebben ze alleen toegang tot de chat van de vergadering als ze deel uitmaken van de Teams-vergaderoproep. Raadpleeg de documentatie voor [Teams-interop](../voice-video-calling/get-started-teams-interop.md) voor meer informatie over het toevoegen van Communication Services-gebruikers aan een Teams-vergaderoproep.

De interoperabiliteitsfunctie van Teams bevindt zich op dit moment in de beperkte preview. Als u deze functie wilt inschakelen voor uw Communication Services-resource, kunt u acsfeedback@microsoft.com e-mailen. Voeg het volgende bij: 
1. De abonnements-id van het Azure-abonnement dat uw Communication Services-resource bevat. 
2. De tenant-id van uw Teams. De eenvoudigste manier om dit te verkrijgen is door een koppeling naar het Team te verkrijgen en te delen. 

U moet lid zijn van de organisatie die eigenaar is van beide entiteiten om deze functie te kunnen gebruiken. 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- Bekijk ons [voorbeeld van een chathero](../../samples/chat-hero-sample.md)
- Meer informatie over [de werking van chat](../../concepts/chat/concepts.md)
