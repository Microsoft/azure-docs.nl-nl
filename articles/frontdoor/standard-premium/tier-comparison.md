---
title: Vergelijking van Azure front deur Standard/Premium-SKU
description: In dit artikel vindt u een overzicht van de Azure front-deur Standard-en Premium-SKU'S en functie verschillen ertussen.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181802"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Overzicht van de Azure front deur Standard/Premium SKU (preview-versie)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Azure front deur wordt aangeboden voor drie verschillende Sku's, [Azure front deur](../front-door-overview.md), Azure front deur Standard (preview) en Azure front deur Premium (preview). Azure front deur Standard/Premium Sku's combineert de mogelijkheden van Azure front deur, Azure CDN standaard van micro soft, Azure WAF in één veilig cloud platform met intelligente bedreigingen.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [**Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

* De **Azure front-deur standaard-SKU** is:

    * Inhouds levering geoptimaliseerd
    * Bieden van zowel statische als dynamische inhouds versnelling
    * Globale taak verdeling
    * SSL-offload
    * Domein-en certificaat beheer
    * Verbeterde verkeers analyse 
    * Basis beveiligings mogelijkheden

* **Azure front-deur Premium SKU** bouwt voort op de mogelijkheden van de standaard-SKU en voegt het volgende toe:

    * Uitgebreide beveiligings mogelijkheden in WAF
    * BOT-beveiliging
    * Ondersteuning voor persoonlijke koppelingen
    * Integratie met micro soft Threat Intelligence en Security Analytics. 

![Diagram van het vergelijken van de voor deur-Sku's.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Vergelijking van functies

| Functie |      Standard      |  Premium |
|----------|:-------------:|------:|
| Aangepaste domeinen | Ja | Ja |
| SSL-offload | Ja | Ja |
| Caching |  Ja  | Ja |
| Compressie | Ja | Ja   |
| Globale taak verdeling | Ja  | Ja |
| Layer 7-route ring | Ja | Ja |
| URL opnieuw genereren | Ja | Ja |
| Regelengine | Ja | Ja |
| Privé oorsprong (privé-koppeling) | Nee | Ja |
| WAF | Alleen aangepaste regels | Ja |
| Bot-beveiliging | Nee | Ja |
| Uitgebreide metrische gegevens en diagnostische gegevens | Ja | Ja |
| Verkeers rapport | Ja | Ja |
| Beveiligings rapport | Nee | Ja | 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur](create-front-door-portal.md)
