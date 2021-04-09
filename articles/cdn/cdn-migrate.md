---
title: Azure CDN Profiel van Verizon Standard migreren naar Verizon Premium
description: Meer informatie over de details van het migreren van een profiel van Verizon Standard naar Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: e58c1177fb3c4d241a3efe1759b3900abbd04ca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778392"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Een Azure CDN profiel migreren van Standard Verizon naar Premium Verizon

Wanneer u een profiel van Azure Content Delivery Network (CDN) maakt om uw eind punten te beheren, biedt Azure CDN vier verschillende producten waaruit u kunt kiezen. Zie voor informatie over de verschillende producten en de beschik bare functies [vergelijken Azure CDN product functies](cdn-features.md).

Als u een **Azure CDN standaard Profiel van Verizon** hebt gemaakt en deze gebruikt voor het beheren van uw CDN-eind punten, hebt u de optie om het te upgraden naar een **Azure CDN Premium-Profiel van Verizon** . Wanneer u een upgrade uitvoert, blijven uw CDN-eind punten en al uw gegevens behouden. 

> [!IMPORTANT]
> Zodra u een upgrade hebt uitgevoerd naar een **Azure CDN Premium-Profiel van Verizon** , kunt u het niet later weer converteren naar een **Azure CDN standaard Profiel van Verizon** .
> 

Neem contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/)als u een upgrade wilt uitvoeren **van een Azure CDN standaard Profiel van Verizon** .

## <a name="profile-comparison"></a>Profiel vergelijking
**Azure CDN Premium van Verizon** -profielen hebben de volgende belang rijke verschillen ten opzichte van **Azure CDN Standard van Verizon** -profielen:
- Voor bepaalde Azure CDN functies zoals [compressie](cdn-improve-performance.md), [cache regels](cdn-caching-rules.md)en geo- [filtering](cdn-restrict-access-by-country.md)kunt u de Azure CDN-interface niet gebruiken. u moet de Verizon-Portal gebruiken via de knop **beheren** .
- API: in tegens telling tot standaard Verizon kunt u de API niet gebruiken om de functies te beheren die toegankelijk zijn vanuit de Premium Verizon-Portal. U kunt echter de API gebruiken om andere algemene functies te beheren, zoals het maken/verwijderen van een eind punt, het opschonen/laden van assets in de cache en het inschakelen/uitschakelen van een aangepast domein.
- Prijzen: Premium Verizon heeft een andere prijs structuur voor gegevens overdracht dan Standard Verizon. Zie [Content Delivery Network prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie.

**Azure CDN Premium van Verizon** -profielen hebben de volgende extra functies:
- [Token verificatie](cdn-token-auth.md): Hiermee kunnen gebruikers een token verkrijgen en gebruiken voor het ophalen van beveiligde bronnen.
- [Regel engine](./cdn-verizon-premium-rules-engine.md): Hiermee kunt u aanpassen hoe HTTP-aanvragen worden verwerkt.
- Geavanceerde analyse Programma's:
   - [Gedetailleerde HTTP-analyse](cdn-advanced-http-reports.md)
   - [Edge Performance Analytics](cdn-edge-performance.md)
   - [Realtime analyses](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de regel engine de [referentie voor Azure CDN regels-engine](./cdn-verizon-premium-rules-engine-reference.md).