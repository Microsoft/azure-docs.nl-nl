---
title: 'Azure-ExpressRoute: verbinding maken met Microsoft Cloud met behulp van Global Reach'
description: Meer informatie over hoe Azure ExpressRoute Global Reach ExpressRoute-circuits kunt koppelen om een privé netwerk tussen uw on-premises netwerken te maken.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 50679e11697a4227af69b8568c5f3cd23fe26cbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662794"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute is een privé-en robuuste manier om uw on-premises netwerken te verbinden met de Microsoft Cloud. U hebt toegang tot veel Cloud Services van micro soft, zoals Azure en Microsoft 365 van uw persoonlijke data center of uw bedrijfs netwerk. Zo kunt u bijvoorbeeld een filiaal in San Francisco hebben met een ExpressRoute-circuit in Silicon dal en een ander filiaal in Londen met een ExpressRoute-circuit in dezelfde plaats. Beide filialen hebben snelle connectiviteit met Azure-resources in VS West en UK-zuid. De filialen kunnen echter geen verbinding maken en gegevens rechtstreeks met elkaar verzenden. Met andere woorden, 10.0.1.0/24 kan gegevens verzenden naar 10.0.3.0/24-en 10.0.4.0/24-netwerk, maar niet op 10.0.2.0/24-netwerk.

![Diagram met de circuits die niet samen zijn gekoppeld met Express route Global Reach.][1]

Met **ExpressRoute Global Reach** kunt u ExpressRoute-circuits samen koppelen om een particulier netwerk tussen uw on-premises netwerken te maken. In het bovenstaande voor beeld, met de toevoeging van ExpressRoute Global Reach, kan uw San Francisco Office (10.0.1.0/24) gegevens rechtstreeks uitwisselen met uw kantoor in Londen (10.0.2.0/24) via de bestaande ExpressRoute-circuits en via het wereld wijde netwerk van micro soft. 

![Diagram met de circuits die samen met Express route Global Reach worden gekoppeld.][2]

## <a name="use-case"></a>Gebruiksvoorbeeld
ExpressRoute Global Reach is ontworpen voor een aanvulling op de WAN-implementatie van uw service provider en verbindt uw filialen over de hele wereld. Als uw service provider bijvoorbeeld voornamelijk in de Verenigde Staten werkt en al uw branches in de V.S. is gekoppeld, maar de service provider niet werkt in Japan en Hong Kong, met ExpressRoute Global Reach u met een lokale service provider kunt werken en micro soft uw vertakkingen in de VS verbindt met ExpressRoute en ons wereld wijde netwerk.

![Diagram waarin een use-case voor Express route Global Reach wordt weer gegeven.][3]

## <a name="availability"></a>Beschikbaarheid 
ExpressRoute Global Reach wordt ondersteund op de volgende locaties. 

> [!NOTE] 
> Om ExpressRoute Global Reach te scha kelen tussen [verschillende geopolitieke regio's](expressroute-locations-providers.md#locations), moeten uw circuits een **Premium-SKU** zijn.

* Australië
* Canada
* Frankrijk
* Duitsland
* Hongkong SAR
* Ierland
* Japan
* Korea
* Nederland
* Nieuw-Zeeland
* Noorwegen
* Singapore
* Zuid-Afrika (alleen Johannes Burg)
* Zweden
* Zwitserland
* Verenigd Koninkrijk
* Verenigde Staten

## <a name="next-steps"></a>Volgende stappen
- Bekijk de [Veelgestelde vragen over Global Reach](expressroute-faqs.md#globalreach).
- Meer informatie over het [inschakelen van Global Reach](expressroute-howto-set-global-reach.md).
- Meer informatie over het [koppelen van een ExpressRoute-circuit aan uw virtuele netwerk](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram zonder globaal bereik"
[2]: ./media/expressroute-global-reach/2.png "diagram met wereld wijd bereik"
[3]: ./media/expressroute-global-reach/3.png "gebruik van wereld wijd bereik"
