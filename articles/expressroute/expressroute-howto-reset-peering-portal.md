---
title: 'Azure ExpressRoute: circuit peerings opnieuw instellen met behulp van de Azure Portal'
description: Meer informatie over het uitschakelen en inschakelen van peerings van een Azure ExpressRoute-circuit met behulp van de Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680277"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>ExpressRoute-circuit-peerings opnieuw instellen met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u peerings van een Azure ExpressRoute-circuit kunt in-en uitschakelen met behulp van de Azure Portal. Wanneer u een peering uitschakelt, wordt de Border Gateway Protocol (BGP)-sessie voor zowel de primaire als de secundaire verbinding van het ExpressRoute-circuit afgesloten. Wanneer u een peering inschakelt, wordt de BGP-sessie op zowel de primaire als de secundaire verbinding van het ExpressRoute-circuit hersteld.

> [!Note]
> De eerste keer dat u de peerings op uw ExpressRoute-circuit configureert, worden de peerings standaard ingeschakeld.

Het opnieuw instellen van uw ExpressRoute-peerings kan handig zijn in de volgende scenario's:

* U test het ontwerp en de implementatie van herstel na nood gevallen. Stel dat u twee ExpressRoute-circuits hebt. U kunt de peerings van één circuit uitschakelen en uw netwerk verkeer afdwingen om het andere circuit te gebruiken.

* U wilt de detectie van bidirectionele door sturing inschakelen (BFD) op persoonlijke Azure-peering of micro soft-peering. Als uw ExpressRoute-circuit vóór 1 augustus 2018, op persoonlijke Azure-peering of vóór 10 januari 2020, op micro soft-peering is gemaakt, is BFD niet standaard ingeschakeld. Stel de peering opnieuw in om BFD in te scha kelen.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga in een browser naar de [Azure Portal](https://portal.azure.com)en meld u vervolgens aan met uw Azure-account.

## <a name="reset-a-peering"></a>Een peering opnieuw instellen

U kunt de micro soft-peering en de persoonlijke Azure-peering op een ExpressRoute-circuit onafhankelijk van elkaar resetten.

1. Kies het circuit dat u wilt wijzigen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Scherm opname van het kiezen van een circuit in de ExpressRoute-circuit lijst.":::

1. Kies de peering-configuratie die u opnieuw wilt instellen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Scherm opname van het kiezen van een peering in het overzicht van het ExpressRoute-circuit.":::

1. Schakel het selectie vakje **peering inschakelen** uit en selecteer **Opslaan** om de peering-configuratie uit te scha kelen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Scherm opname die laat zien hoe het selectie vakje peering inschakelen wordt uitgeschakeld.":::

1. Schakel het selectie vakje **peering inschakelen** in en selecteer **Opslaan** om de peering-configuratie opnieuw in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor het oplossen van problemen met ExpressRoute:

* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerk prestaties oplossen](expressroute-troubleshooting-network-performance.md)
