---
title: Aan de slag met Teams-interop op Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In deze quickstart leert u hoe u kunt deelnemen aan een Teams-vergadering met de chatclientbibliotheek in Azure Communication
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 59e259df5d9e564e11dbebd416569727c3857632
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932302"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Quickstart: Voeg u chat-app toe aan een Teams-meeting

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ga aan de slag met Azure Communication Services door uw chatoplossing te verbinden met Microsoft Teams, met behulp van de JavaScript-clientbibliotheek. 

## <a name="prerequisites"></a>Vereisten 

1. Een  [Teams-implementatie](/deployoffice/teams-install). 
2. Een werkende [chat-app](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Teams-interoperabiliteit inschakelen 

Wanneer Communication Services-gebruikers als gastgebruiker deelnemen aan een Teams-vergadering, hebben ze alleen toegang tot de chat van de vergadering als ze deel uitmaken van de Teams-vergaderoproep. Raadpleeg de documentatie voor [Teams-interop](../voice-video-calling/get-started-teams-interop.md) voor meer informatie over het toevoegen van Communication Services-gebruikers aan een Teams-vergaderoproep.

U moet lid zijn van de organisatie die eigenaar is van beide entiteiten om deze functie te kunnen gebruiken.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- Bekijk ons [voorbeeld van een chathero](../../samples/chat-hero-sample.md)
- Meer informatie over [de werking van chat](../../concepts/chat/concepts.md)