---
title: Zelfstudie - On-premises omgevingen peeren met een privécloud
description: Meer informatie over hoe u ExpressRoute Global Reach-peering maakt met een privécloud in een Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: ae92bf89a08c5fade8757e3ee596c4ed4a5e6389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494149"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Zelfstudie: On-premises omgevingen peeren met een privécloud

ExpressRoute Global Reach verbindt uw on-premises omgeving verbindt met uw Azure VMware Solution-privécloud. De ExpressRoute-Global Reach-verbinding wordt tot stand gebracht tussen de ExpressRoute-circuit in de privécloud en een bestaande ExpressRoute-verbinding met uw on-premises omgevingen. 

Het ExpressRoute-circuit dat u gebruikt bij het [configureren van netwerken voor uw VMware privécloud in azure](tutorial-configure-networking.md) , moet u autorisatie sleutels maken en gebruiken.  U hebt al een autorisatie sleutel uit het ExpressRoute-circuit gebruikt. in deze zelf studie maakt u een tweede autorisatie sleutel voor peer met uw on-premises ExpressRoute-circuit.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een tweede autorisatie sleutel voor _circuit 2_, het ExpressRoute-circuit van de privécloud.
> * Gebruik de Azure Portal of de Azure CLI in een Cloud Shell methode in het abonnement van _Circuit 1_ om on-premises-to-Private Cloud ExpressRoute Global Reach peering in te scha kelen.


## <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de documentatie over het [inschakelen van connectiviteit in verschillende Azure-abonnementen](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) voordat u de connectiviteit tussen twee ExpressRoute-circuits inschakelt met behulp van ExpressRoute Global Reach.  

## <a name="prerequisites"></a>Vereisten

- Vastgelegde connectiviteit naar en van een Azure VMware Solution-privécloud met een ExpressRoute-circuit die is gekoppeld aan een ExpressRoute-gateway in een virtueel Azure-netwerk (VNet). Dit is circuit 2 vanuit procedures voor peering.
- Een afzonderlijk, functionerend ExpressRoute-circuit dat wordt gebruikt om on-premises omgevingen te verbinden met Azure. Dit is circuit 1 vanuit het perspectief van de procedures voor peering.
- Een/29 niet-overlappend [blok met netwerkadressen](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) voor de ExpressRoute Global Reach-peering.
- Zorg ervoor dat alle gateways, met inbegrip van de service van de ExpressRoute-provider, 4-bytes autonoom systeem nummer (ASN) ondersteunen. De Azure VMware-oplossing maakt gebruik van 4-byte open bare Asn's voor het adverteren van routes.

>[!IMPORTANT]
>In de context van deze vereisten is uw on-premises ExpressRoute-circuit _circuit 1_ en het ExpressRoute-circuit van de privécloud in een ander abonnement _circuit 2_.

## <a name="create-an-expressroute-authorization-key-in-the-on-premises-circuit"></a>Een ExpressRoute-autorisatie sleutel maken in het on-premises circuit

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Persoonlijke cloud van de peer to on-premises met een autorisatie sleutel
U beschikt nu over een autorisatiesleutel voor het ExpressRoute-circuit van de privécloud en kunt het circuit dus peeren met uw on-premises ExpressRoute-circuit. De peering wordt uitgevoerd vanuit het perspectief van een on-premises ExpressRoute-circuit in de **Azure Portal** of met de **Azure CLI in een Cloud Shell**. Met beide methoden gebruikt u de resource-id en autorisatiesleutel van het ExpressRoute-circuit van uw privécloud om de peering te voltooien.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met hetzelfde abonnement als dat van het on-premises ExpressRoute-circuit.

1. Selecteer in het **overzicht** van de privécloud onder beheren de optie **connectiviteit**  >  **ExpressRoute Global Reach**  >  **toevoegen**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Scherm opname van het tabblad ExpressRoute Global Reach in de privécloud van de Azure VMware-oplossing.":::

1. Een on-premises Cloud verbinding maken. Voer een van de volgende handelingen uit en selecteer **maken**:

   - Selecteer het **ExpressRoute-circuit** in de lijst of
   - Als u de circuit-ID hebt, plakt u deze in het veld en geeft u de autorisatie sleutel op.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Voer de ExpressRoute-ID en de autorisatie sleutel in en selecteer vervolgens maken.":::   
   
   De nieuwe verbinding wordt weergegeven in de lijst on-premises cloudverbindingen.

>[!TIP]
>U kunt een verbinding verwijderen of verbreken uit de lijst door **Meer** te selecteren.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Een on-premises verbinding verbreken of verwijderen":::

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [CLI-opdrachten](../expressroute/expressroute-howto-set-global-reach-cli.md) zijn uitgebreid met specifieke details en voorbeelden om u te helpen bij de configuratie van ExpressRoute Global Reach-peering tussen on-premises omgevingen en een privécloud van Azure VMware Solution.

>[!TIP]
>Voor de boog van de Azure CLI-opdracht uitvoer kunnen deze instructies een [ `–query` argument](https://docs.microsoft.com/cli/azure/query-azure-cli) gebruiken om een JMESPath-query uit te voeren, zodat alleen de vereiste resultaten worden weer gegeven.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met hetzelfde abonnement als dat van het on-premises ExpressRoute-circuit. 

1. Open een Cloud Shell en verlaat de shell als bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Scherm opname met de Azure Portal Cloud Shell.":::

1. Maak de peering op basis van Circuit 1, waarbij de resource-ID en autorisatie sleutel van circuit 2 worden door gegeven. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Scherm opname met de opdracht en de resultaten van een geslaagde peering tussen Circuit 1 en circuit 2.":::

U kunt nu via de ExpressRoute Global Reach-peering vanaf on-premises omgevingen verbinding maken met uw privécloud.

>[!TIP]
>U kunt de peering verwijderen door de [verbinding tussen uw on-premises netwerk instructies uit te scha kelen](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


---

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de on-premises-to-Private Cloud ExpressRoute kunt inschakelen Global Reach peering. 

Ga verder met de volgende zelfstudie voor meer informatie over hoe u VMware HCX-oplossing implementeert en configureert voor uw Azure VMware Solution-privécloud.

> [!div class="nextstepaction"]
> [VMware HCX implementeren en configureren](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
