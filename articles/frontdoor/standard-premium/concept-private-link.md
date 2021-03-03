---
title: Uw oorsprong beveiligen met een privé koppeling in azure front deur Standard/Premium (preview)
description: Deze pagina bevat informatie over het beveiligen van de verbinding met uw oorsprong met behulp van een persoonlijke koppeling.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: 6a1ec6e0b8862c6ad2b884b019e908e7d2a59a1e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715510"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Uw oorsprong beveiligen met een privé koppeling in azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Documenten voor de front-deur van Azure](../front-door-overview.md)weer geven.

## <a name="overview"></a>Overzicht

Met [Azure private link](../../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure PaaS Services en gehoste Azure-Services via een persoonlijk eind punt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Azure front deur Premium SKU kan verbinding maken met uw oorsprong achter Web-app en opslag account met behulp van de persoonlijke koppelings service, waardoor uw oorsprong openbaar toegankelijk is.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Architectuur van privé-eind punten voor de voor deur":::

Wanneer u een persoonlijke koppeling naar uw oorsprong in de Azure front-deur Premium-configuratie inschakelt, wordt met de voor deur een persoonlijk eind punt in uw naam op het regionale privé netwerk van de voor deur gemaakt. Dit eind punt wordt beheerd door de voor deur van Azure. U ontvangt een Azure front-endpoint-aanvraag voor een privé-eind punt voor een goedkeurings bericht op uw oorsprong. Nadat u de aanvraag hebt goedgekeurd, wordt een privé-IP-adres toegewezen van het virtuele netwerk van de voor deur, verkeer tussen de Azure front-deur en uw oorsprong door de ingestelde persoonlijke koppeling met de Azure-netwerk-backbone. Binnenkomend verkeer naar uw oorsprong is nu beveiligd wanneer het afkomstig is van uw Azure front-deur.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Persoonlijk eind punt inschakelen":::

> [!NOTE]
> Zodra u de oorsprong van een persoonlijke koppeling inschakelt en het persoonlijke eind punt conenction goedkeurt, duurt het enkele minuten voordat de verbinding tot stand is gebracht. Gedurende deze periode ontvangt de aanvraag voor de oorsprong een fout bericht voor de voor deur. Het fout bericht gaat verloren wanneer de verbinding tot stand is gebracht.

## <a name="limitations"></a>Beperkingen

Azure front-deur privé-eind punten zijn beschikbaar in de volgende regio's tijdens de open bare Preview: VS-Oost, West 2 VS en Zuid-Centraal vs.

Voor de beste latentie moet u altijd een Azure-regio kiezen die het dichtst bij uw oorsprong is als u ervoor kiest om het eind punt van de front-deur persoonlijke koppelingen in te scha kelen.

Azure front-deur-privé-eind punten worden beheerd door het platform en onder het abonnement van de Azure front-deur. Met Azure front-deur kunt u verbindingen met een privé koppeling met hetzelfde klant abonnement maken dat wordt gebruikt om het voorste deur profiel te creëren.

## <a name="next-steps"></a>Volgende stappen

* Zie [verbinding maken met een web-app met behulp van een persoonlijk eind punt](../../private-link/tutorial-private-endpoint-webapp-portal.md)als u Azure front-deur Premium wilt verbinden met uw web-app via een persoonlijke koppelings service.
* Zie [verbinding maken met een opslag account met behulp van een persoonlijk eind punt](../../private-link/tutorial-private-endpoint-storage-portal.md)als u Azure front-deur Premium wilt verbinden met uw opslag account via een privé-koppelings service.
