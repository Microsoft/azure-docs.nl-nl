---
title: Over het upgraden van circuit bandbreedte | Azure-ExpressRoute
description: In dit artikel leert u de aanbevolen procedures voor het upgraden van de band breedte van het ExpressRoute-circuit
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89397947"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Over het upgraden van ExpressRoute-circuit bandbreedte

ExpressRoute maakt specifieke en particuliere connectiviteit mogelijk met het wereld wijde netwerk van micro soft. Connectiviteit wordt vereenvoudigd door het netwerk van een ExpressRoute-partner of een rechtstreekse verbinding met de MSEE-apparaten (micro soft Enter prise Edge). Zodra de fysieke verbinding is geconfigureerd en getest, kunt u laag-2-en laag-3-connectiviteit inschakelen door een ExpressRoute-circuit te maken en peering te configureren.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Band breedte van het circuit upgraden

Als u de band breedte van het circuit wilt upgraden, moet de ExpressRoute direct-of ExpressRoute-partner [voldoende beschik bare band breedte](#considerations) hebben om de upgrade te kunnen volt ooien.

Als er capaciteit beschikbaar is, kunt u het circuit bijwerken met behulp van de volgende methoden:

* [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure-CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Overwegingen bij capaciteitsbepaling

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Onvoldoende ExpressRoute-partner bandbreedte

Als de ExpressRoute-partner onvoldoende capaciteit heeft, moet u een nieuw circuit maken dat is geconfigureerd voor de gewenste band breedte. Als u de connectiviteit wilt behouden, moet u het oude circuit pas verwijderen als het nieuwe circuit is ingericht, peering is geconfigureerd en (met betrekking tot privé-peering) het verbindings object voor de gateway van het virtuele ExpressRoute-netwerk is ingericht.

Als uw ExpressRoute-partner niet voldoende beschik bare capaciteit heeft, moet u extra capaciteit aanvragen op de gewenste locatie van de peering. Zodra de nieuwe capaciteit is ingericht, kunt u de stappen in de artikelen in de sectie [band breedte van het upgrade circuit](#upgrade) gebruiken om een nieuw circuit te maken, de verbinding te configureren en het oude circuit te verwijderen.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Onvoldoende ExpressRoute directe band breedte

Als de ExpressRoute direct voldoende capaciteit heeft, kunt u circuits verwijderen die zijn gekoppeld aan de ExpressRoute directe resource die niet meer nodig zijn of een nieuwe ExpressRoute direct-resource maken. Raadpleeg [How to configure ExpressRoute direct](how-to-expressroute-direct-portal.md)(Engelstalig) voor hulp bij het beheren van de ExpressRoute direct-resource.

## <a name="next-steps"></a>Volgende stappen

* [Een circuit maken en wijzigen](expressroute-howto-circuit-portal-resource-manager.md)
* [Een peeringconfiguratie maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
