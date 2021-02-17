---
title: Zelfstudie - On-premises omgevingen peeren met een privécloud
description: Meer informatie over hoe u ExpressRoute Global Reach-peering maakt met een privécloud in een Azure VMware Solution.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558817"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Zelfstudie: On-premises omgevingen peeren met een privécloud

ExpressRoute Global Reach verbindt uw on-premises omgeving verbindt met uw Azure VMware Solution-privécloud. De ExpressRoute-Global Reach-verbinding wordt tot stand gebracht tussen de ExpressRoute-circuit in de privécloud en een bestaande ExpressRoute-verbinding met uw on-premises omgevingen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik de Azure Portal om on-premises-to-Private Cloud ExpressRoute in te scha kelen Global Reach peering.


## <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de documentatie over het [inschakelen van connectiviteit in verschillende Azure-abonnementen](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) voordat u de connectiviteit tussen twee ExpressRoute-circuits inschakelt met behulp van ExpressRoute Global Reach.  


## <a name="prerequisites"></a>Vereisten

- Een afzonderlijk, functionerend ExpressRoute-circuit dat wordt gebruikt om on-premises omgevingen te verbinden met Azure.
- Zorg ervoor dat alle gateways, met inbegrip van de service van de ExpressRoute-provider, 4-bytes autonoom systeem nummer (ASN) ondersteunen. De Azure VMware-oplossing maakt gebruik van 4-byte open bare Asn's voor het adverteren van routes.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Een ExpressRoute-autorisatie sleutel maken in het on-premises ExpressRoute-circuit

1. Selecteer op de Blade **ExpressRoute-circuits** onder instellingen de optie **autorisaties**.

2. Voer de naam in voor de autorisatie sleutel en selecteer **Opslaan**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Selecteer autorisaties en voer de naam in voor de autorisatie sleutel.":::
  
     Zodra de nieuwe sleutel is gemaakt, wordt deze weer gegeven in de lijst met autorisatie sleutels voor het circuit.
 
 4. Noteer de autorisatie sleutel en de ExpressRoute-ID. U hebt deze nodig in de volgende stap om de peering te voltooien.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Persoonlijke cloud van de peer to on-premises

1. Selecteer in de privécloud **Overzicht** onder Beheren, selecteer **Connectiviteit > ExpressRoute > Global Reach > Toevoegen**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Selecteer Connectiviteit in het menu, vervolgens het tabblad ExpressRoute Global Reach en vervolgens toevoegen.":::

2. Voer de ExpressRoute-ID in en de autorisatie sleutel die u in de vorige sectie hebt gemaakt.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Voer de ExpressRoute-ID en de autorisatie sleutel in en selecteer vervolgens maken.":::

3. Selecteer **Maken**. De nieuwe verbinding wordt weergegeven in de lijst on-premises cloudverbindingen.  

>[!TIP]
>U kunt een verbinding verwijderen of verbreken uit de lijst door **Meer** te selecteren.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Een on-premises verbinding verbreken of verwijderen":::


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de on-premises-to-Private Cloud ExpressRoute kunt inschakelen Global Reach peering. 

Ga verder met de volgende zelfstudie voor meer informatie over hoe u VMware HCX-oplossing implementeert en configureert voor uw Azure VMware Solution-privécloud.

> [!div class="nextstepaction"]
> [VMware HCX implementeren en configureren](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
