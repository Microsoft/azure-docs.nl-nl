---
title: 'Azure-ExpressRoute: Global Reach configureren met behulp van de Azure Portal'
description: Dit artikel helpt u bij het koppelen van ExpressRoute-circuits aan elkaar om een particulier netwerk te maken tussen uw on-premises netwerken en Global Reach in te scha kelen met behulp van de Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539346"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>ExpressRoute configureren Global Reach met behulp van de Azure Portal

Dit artikel helpt u bij het configureren van ExpressRoute Global Reach met behulp van Power shell. Zie [ExpressRouteRoute Global Reach](expressroute-global-reach.md)voor meer informatie.

 ## <a name="before-you-begin"></a>Voordat u begint

Bevestig de volgende criteria voordat u begint met de configuratie:

* U begrijpt [werk stromen](expressroute-workflows.md)voor het inrichten van ExpressRoute-circuits.
* Uw ExpressRoute-circuits bevinden zich in een Provisioning-status.
* Persoonlijke Azure-peering is geconfigureerd op uw ExpressRoute-circuits.
* Als u Power shell lokaal wilt uitvoeren, controleert u of de meest recente versie van Azure PowerShell op uw computer is geïnstalleerd.

## <a name="identify-circuits"></a>Circuits identificeren

1. Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

2. Identificeer de ExpressRoute-circuits die u wilt gebruiken. U kunt Global Reach ExpressRoute inschakelen tussen de persoonlijke peering van twee ExpressRoute-circuits, zolang deze zich in de ondersteunde landen/regio's bevinden. De circuits moeten op verschillende peering-locaties worden gemaakt. 

   * Als uw abonnement eigenaar is van beide circuits, kunt u een van beide circuits kiezen om de configuratie uit te voeren in de volgende secties.
   * Als de twee circuits zich in verschillende Azure-abonnementen bevinden, hebt u autorisatie nodig van een Azure-abonnement. Vervolgens geeft u de autorisatie sleutel door wanneer u de configuratie opdracht uitvoert in het andere Azure-abonnement.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Lijst met ExpressRoute-circuits":::

## <a name="enable-connectivity"></a>Connectiviteit inschakelen

Connectiviteit tussen uw on-premises netwerken inschakelen. Er zijn afzonderlijke sets instructies voor circuits die zich in hetzelfde Azure-abonnement bevinden en circuits die verschillende abonnementen zijn.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-circuits in hetzelfde Azure-abonnement

1. Selecteer de configuratie van de **persoonlijke Azure** -peering. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Overzicht van ExpressRoute-peering":::

1. Schakel het selectie vakje **Global Reach inschakelen** in en selecteer vervolgens **Global Reach toevoegen** om de pagina *Global Reach configuratie toevoegen* te openen.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Wereld wijd bereik van privé-peering inschakelen":::

1. Geef op de pagina *Global Reach configuratie toevoegen* een naam op voor deze configuratie. Selecteer het *ExpressRoute-circuit* waarmee u dit circuit wilt verbinden en voer een **/29 IPv4** in voor het *Global Reach subnet*. We gebruiken IP-adressen in dit subnet om verbinding te maken tussen de twee ExpressRoute-circuits. Gebruik niet de adressen in dit subnet in uw virtuele Azure-netwerken of in uw on-premises netwerk. Selecteer **toevoegen** om het circuit toe te voegen aan de persoonlijke peering-configuratie.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Configuratie pagina Global Reach":::

1. Selecteer **Opslaan** om de Global Reach configuratie te volt ooien. Wanneer de bewerking is voltooid, hebt u verbinding tussen uw twee on-premises netwerken via beide ExpressRoute-circuits.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="De persoonlijke peering-configuratie opslaan":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-circuits in verschillende Azure-abonnementen

Als de twee circuits zich niet in hetzelfde Azure-abonnement bevinden, hebt u autorisatie nodig. In de volgende configuratie wordt autorisatie gegenereerd van het abonnement van circuit 2. De autorisatie sleutel wordt vervolgens door gegeven aan Circuit 1.

1. Genereer een autorisatie sleutel.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Autorisatie sleutel genereren"::: 

   Noteer de circuit Resource-ID van circuit 2 en de autorisatie sleutel.

1. Selecteer de configuratie van de **persoonlijke Azure** -peering. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Overzicht van Circuit 1-peering":::

1. Schakel het selectie vakje **Global Reach inschakelen** in en selecteer vervolgens **Global Reach toevoegen** om de pagina *Global Reach configuratie toevoegen* te openen.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Wereld wijd bereik van Circuit 1 inschakelen":::

1. Geef op de pagina *Global Reach configuratie toevoegen* een naam op voor deze configuratie. Schakel het selectie vakje **autorisatie inwisselen** in. Voer de **autorisatie sleutel** in en de **ExpressRoute-circuit-id** die u in stap 1 hebt gegenereerd en opgehaald. Geef vervolgens een **/29 IPv4** op voor het *Global Reach subnet*. We gebruiken IP-adressen in dit subnet om verbinding te maken tussen de twee ExpressRoute-circuits. Gebruik niet de adressen in dit subnet in uw virtuele Azure-netwerken of in uw on-premises netwerk. Selecteer **toevoegen** om het circuit toe te voegen aan de persoonlijke peering-configuratie.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Global Reach toevoegen met autorisatie sleutel":::

1. Selecteer **Opslaan** om de Global Reach configuratie te volt ooien. Wanneer de bewerking is voltooid, hebt u verbinding tussen uw twee on-premises netwerken via beide ExpressRoute-circuits.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="De configuratie van de persoonlijke peering op Circuit 1 opslaan":::

## <a name="verify-the-configuration"></a>De configuratie controleren

Controleer de configuratie van de Global Reach door *privé-peering* te selecteren onder de configuratie van het ExpressRoute-circuit. Wanneer de configuratie correct is geconfigureerd, moet deze er als volgt uitzien:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Global Reach configuratie controleren":::

## <a name="disable-connectivity"></a>Connectiviteit uitschakelen

U hebt twee opties wanneer u Global Reach uitschakelt. Als u de verbinding tussen alle circuits wilt uitschakelen, schakelt u **Global Reach inschakelen** uit om de verbinding tussen alle circuits uit te scha kelen. Als u de verbinding tussen een afzonderlijk circuit wilt uitschakelen, selecteert u de knop verwijderen naast de naam van de *Global Reach* om de verbinding tussen de twee te verwijderen. Selecteer vervolgens **Opslaan** om de bewerking te volt ooien.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Global Reach configuratie uitschakelen":::

Nadat de bewerking is voltooid, hebt u geen verbinding meer tussen uw on-premises netwerk via uw ExpressRoute-circuits.

## <a name="next-steps"></a>Volgende stappen
1. [Meer informatie over ExpressRoute Global Reach](expressroute-global-reach.md)
2. [ExpressRoute-connectiviteit controleren](expressroute-troubleshooting-expressroute-overview.md)
3. [Een ExpressRoute-circuit koppelen aan een virtueel Azure-netwerk](expressroute-howto-linkvnet-arm.md)
