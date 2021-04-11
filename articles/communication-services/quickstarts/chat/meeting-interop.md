---
title: Aan de slag met Teams-interop op Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In deze Quick Start leert u hoe u kunt deel nemen aan een team vergadering met de Azure Communication chat SDK
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ba3a589c5d0f09f24950bd3fee8edc7f4dcd4601
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169059"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Quickstart: Voeg u chat-app toe aan een Teams-meeting

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> Vul [dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)in om [teams Tenant interoperabiliteit](../../concepts/teams-interop.md)in of uit te scha kelen.

Ga aan de slag met Azure Communication Services door uw chat-oplossing te verbinden met micro soft teams met behulp van de Java script SDK. 

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