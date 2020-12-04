---
title: 'Azure ExpressRoute: een circuit peering opnieuw instellen met behulp van de Azure Portal'
description: Meer informatie over het uitschakelen en inschakelen van peerings van een Azure ExpressRoute-circuit met behulp van de Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582302"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>ExpressRoute-circuit-peerings opnieuw instellen gebruik de Azure Portal

In dit artikel wordt beschreven hoe u peerings van een ExpressRoute-circuit kunt uitschakelen en inschakelen met behulp van de Azure Portal. Wanneer u een peering uitschakelt, wordt de BGP-sessie voor zowel de primaire als de secundaire verbinding van het ExpressRoute-circuit afgesloten. U verliest de connectiviteit via deze peering naar micro soft. Wanneer u een peering inschakelt, wordt de BGP-sessie op zowel de primaire als de secundaire verbinding van het ExpressRoute-circuit weer gebracht. U kunt de verbinding met deze peering met micro soft herstellen. U kunt micro soft-peering en persoonlijke Azure-peering op een ExpressRoute-circuit onafhankelijk van elkaar in-en uitschakelen. De eerste keer dat u de peerings op uw ExpressRoute-circuit configureert, worden de peerings standaard ingeschakeld.

Er zijn een paar scenario's waarin het handig is om uw ExpressRoute-peerings te herstellen.
* Het ontwerp en de implementatie van herstel na nood gevallen testen. U hebt bijvoorbeeld twee ExpressRoute-circuits. U kunt de peerings van één circuit uitschakelen en uw netwerk verkeer afdwingen naar het andere circuit.
* Schakel de detectie van bidirectionele door sturing (BFD) in op persoonlijke Azure-peering of micro soft-peering van uw ExpressRoute-circuit. BFD wordt standaard ingeschakeld op persoonlijke Azure-peering als uw ExpressRoute-circuit wordt gemaakt na augustus 1 2018 en micro soft-peering. Als uw ExpressRoute-circuit na januari 10 2020 wordt gemaakt. Als uw circuit eerder is gemaakt, is BFD niet ingeschakeld. U kunt BFD inschakelen door de peering uit te scha kelen en opnieuw in te scha kelen. 

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

## <a name="reset-a-peering"></a>Een peering opnieuw instellen

1. Selecteer het circuit waarvan u wilt dat peering configuratie wijzigingen maakt.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute-circuit lijst":::

1. Selecteer de peering-configuratie die u wilt in-of uitschakelen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Overzicht van ExpressRoute-circuits":::

1. Schakel het selectie vakje **peering inschakelen** uit en selecteer **Opslaan** om de peering-configuratie uit te scha kelen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Privé-peering uitschakelen":::

1. U kunt de peering opnieuw inschakelen door **peering inschakelen** in te scha kelen en **Opslaan** te selecteren.

## <a name="next-steps"></a>Volgende stappen
Als u hulp nodig hebt bij het oplossen van een probleem met ExpressRoute, raadpleegt u de volgende artikelen:
* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerk prestaties oplossen](expressroute-troubleshooting-network-performance.md)
