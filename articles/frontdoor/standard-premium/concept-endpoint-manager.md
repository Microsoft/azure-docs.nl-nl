---
title: 'Azure front deur: eindpunt beheerder'
description: Dit artikel bevat een overzicht van Azure front-deur eindpunt beheerder.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099221"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Wat is Azure front deur Standard/Premium (preview) Endpoint Manager?

> [!NOTE]
> * Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Documenten voor de front-deur van Azure](../front-door-overview.md)weer geven.

Endpoint Manager biedt een overzicht van de eind punten die u hebt geconfigureerd voor de Azure front-deur. Een eind punt is een logische groepering van een domein en de bijbehorende configuraties. Met eindpunt beheerder kunt u uw verzameling eind punten beheren voor ruwe (maken, lezen, bijwerken en verwijderen). U kunt de volgende elementen voor uw eind punten beheren via eindpunt beheerder:

* Domains
* Oorsprongs groepen
* Routes
* Beveiliging

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Scherm opname van eindpunt beheer zonder configuraties." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

In de lijst met eind punten kunt u zien hoeveel exemplaren van elk element worden gemaakt binnen een eind punt. De koppelings status voor elk element wordt ook weer gegeven. U kunt bijvoorbeeld meerdere domeinen en oorsprongs groepen maken en de koppeling tussen hen met verschillende routes toewijzen.

> [!IMPORTANT]
> * Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [**Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="linked-view"></a>Gekoppelde weer gave

Met de gekoppelde weer gave in de eindpunt beheerder kunt u eenvoudig de koppeling tussen de elementen van de Azure-front-deur identificeren, zoals:

* Welke domeinen zijn gekoppeld aan het huidige eind punt?
* Welke oorspronkelijke groep is gekoppeld aan welk domein?
* Welk WAF-beleid is gekoppeld aan welk domein?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Scherm opname van eindpunt beheerder met configuraties." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
