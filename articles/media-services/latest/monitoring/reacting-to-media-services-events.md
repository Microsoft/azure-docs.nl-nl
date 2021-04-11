---
title: Reageren op Azure Media Services gebeurtenissen
description: In dit artikel wordt beschreven hoe u Azure Event Grid kunt gebruiken om u te abonneren op Media Services-gebeurtenissen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9bed493ca37d21d0c3e5c289bb8c26607975bdc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609308"
---
# <a name="handling-event-grid-events"></a>Event Grid-gebeurtenissen verwerken

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Met Media Services gebeurtenissen kunnen toepassingen reageren op verschillende gebeurtenissen (bijvoorbeeld de taak status wijzigings gebeurtenis) met behulp van moderne serverloze architecturen. Dit gebeurt zonder dat er complexe code of dure en inefficiënte polling services nodig zijn. In plaats daarvan worden gebeurtenissen gepusht via [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar gebeurtenis-handlers zoals [Azure functions](https://azure.microsoft.com/services/functions/), [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen webhook en betaalt u alleen voor wat u gebruikt. Zie [Event grid prijzen](https://azure.microsoft.com/pricing/details/event-grid/)voor meer informatie over prijzen.

De beschik baarheid voor Media Services gebeurtenissen is gekoppeld aan Event Grid [Beschik baarheid](../../../event-grid/overview.md) en wordt in andere regio's beschikbaar gesteld als Event grid.  

## <a name="media-services-events-and-schemas"></a>Media Services gebeurtenissen en schema's

Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees. Media Services gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een Media Services gebeurtenis identificeren omdat de eigenschap Event type met ' micro soft. media. ' begint.

Zie [Media Services-gebeurtenis schema's](../media-services-event-schemas.md)voor meer informatie.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen

Toepassingen die Media Services gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:

* Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van het opslag account dat u verwacht.
* Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
* Velden negeren die u niet begrijpt.  Met deze procedure kunt u de nieuwe functies die in de toekomst kunnen worden toegevoegd, flexibeler maken.
* Gebruik het voor voegsel ' subject ' en achtervoegsel overeenkomsten om gebeurtenissen te beperken tot een bepaalde gebeurtenis.

> [!NOTE]
> Gebeurtenissen zijn onderhevig aan de Event Grid [-Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Als u gebeurtenis meldingen wilt ontvangen met behulp van Api's, raadpleegt u voor beelden van het gebruik van gebeurtenissen met [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) of [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Volgende stappen

* [Gebeurtenissen bewaken-Portal](../monitor-events-portal-how-to.md)
* [Gebeurtenissen bewaken - CLI](../job-state-events-cli-how-to.md)
