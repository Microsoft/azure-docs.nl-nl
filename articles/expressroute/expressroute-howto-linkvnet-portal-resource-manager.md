---
title: 'Zelfstudie: Een VNet koppelen aan een ExpressRoute-circuit - Azure Portal'
description: Deze zelfstudie laat zien hoe u een verbinding maakt om een virtueel netwerk te koppelen aan een ExpressRoute-circuit met behulp van Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998767"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Zelfstudie: Een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

In deze zelfstudie leert u hoe u een verbinding maakt om een virtueel netwerk te koppelen aan een ExpressRoute-circuit met behulp van Azure Portal. De virtuele netwerken die u met uw Azure ExpressRoute-circuit verbindt, kunnen zich in hetzelfde abonnement bevinden of deel uitmaken van een ander abonnement.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een virtueel netwerk verbinden met een circuit in hetzelfde abonnement.
> - Een virtueel netwerk verbinden met een circuit in een ander abonnement.
> - De koppeling tussen het virtuele netwerk en het ExpressRoute-circuit verwijderen.

## <a name="prerequisites"></a>Vereisten

* Bekijk de [vereisten](expressroute-prerequisites.md), de [routeringsvereisten](expressroute-routing.md) en de [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben.
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider.
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Raadpleeg het artikel [Peering voor een ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md) voor instructies voor peeren en routeren.
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat BGP-peering tussen uw netwerk en Microsoft wordt ingesteld voor end-to-end-connectiviteit.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig hebt ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute te maken](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van het GatewayType 'ExpressRoute', niet VPN.

* U kunt tot 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt.

* Eén VNet kan aan maximaal vier ExpressRoute-circuits worden gekoppeld. Gebruik de volgende procedure om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, in verschillende abonnementen of in een combinatie van beide bevinden.

* Als u de ExpressRoute Premium-invoegtoepassing inschakelt, kunt u virtuele netwerken koppelen die zich buiten de geopolitieke regio van het ExpressRoute-circuit bevinden. De Premium-invoegtoepassing biedt u ook de mogelijkheid om meer dan 10 virtuele netwerken te verbinden met uw ExpressRoute-circuit, afhankelijk van de gekozen bandbreedte. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoegtoepassing.

* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Een VNet verbinden met een circuit - zelfde abonnement

> [!NOTE]
> BGP-configuratiegegevens worden niet weergegeven als de provider van laag 3 uw peerings heeft geconfigureerd. Als uw circuit een ingerichte status heeft, zou u verbindingen moeten kunnen maken.
>

### <a name="to-create-a-connection"></a>Een verbinding maken

1. Zorg ervoor dat uw ExpressRoute-circuit en persoonlijke Azure-peering juist zijn geconfigureerd. Volg de instructies in [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md) en [Peering voor een ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md). Uw ExpressRoute-circuit zou eruit moeten zien zoals in de volgende afbeelding:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Schermopname van ExpressRoute-circuit":::

1. U kunt nu beginnen met het inrichten van een verbinding om uw virtuele netwerkgateway te koppelen aan uw ExpressRoute-circuit. Selecteer **Verbinding** > **Toevoegen** om de pagina **Verbinding toevoegen** te openen.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Schermopname van verbinding toevoegen":::

1. Voer een naam voor de verbinding in en selecteer **Volgende: Instellingen >**

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Pagina Verbinding maken - Basisinformatie":::.

1. Selecteer de gateway die bij het virtuele netwerk hoort dat u aan het circuit wilt koppelen, en selecteer vervolgens **Beoordelen en maken**. Selecteer **Maken** nadat de validatie is voltooid.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Pagina Verbindingsinstellingen maken":::

1. Nadat de verbinding is geconfigureerd, wordt de informatie voor de verbinding weergegeven in het verbindingsobject.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Schermopname van verbindingsobject":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Een VNet verbinden met een circuit - ander abonnement

U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. De volgende afbeelding is een schematische weergave van hoe delen tussen meerdere abonnementen werkt voor ExpressRoute-circuits.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Connectiviteit tussen abonnementen":::

Elk van de kleinere clouds in de grote cloud staan voor abonnementen die tot verschillende afdelingen binnen een organisatie behoren. Elk van de afdelingen in de organisatie gebruikt een eigen abonnement voor het implementeren van hun services, maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken terug naar uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen gebruikmaken van het ExpressRoute-circuit.

  > [!NOTE]
  > Kosten voor connectiviteit- en bandbreedte voor het toegewezen circuit zijn in rekening gebracht bij de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde bandbreedte.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Beheer: informatie over circuiteigenaars en -gebruikers

De 'circuiteigenaar' is een geautoriseerde hoofdgebruiker van de ExpressRoute-circuitresource. De circuiteigenaar kan autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaren van virtuele netwerkgateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie heeft tot gevolg dat alle koppelingsverbindingen uit het abonnement waarvan de toegangsrechten zijn ingetrokken, worden verwijderd.

### <a name="circuit-owner-operations"></a>Bewerkingen door circuiteigenaars

**Een autorisatie voor een verbinding maken**

De circuiteigenaar maakt een autorisatie, waarmee een autorisatiesleutel wordt gemaakt die door een circuitgebruiker moet worden gebruikt om de virtuele netwerkgateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

> [!NOTE]
> Voor elke verbinding is een afzonderlijke autorisatie vereist.
>

1. Selecteer op de pagina ExpressRoute de optie **Autorisaties** en typ vervolgens een **naam** voor de autorisatie en selecteer **Opslaan**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Autorisaties":::

2. Zodra de configuratie is opgeslagen, kopieert u de **resource-id** en de **autorisatiesleutel**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Autorisatiesleutel":::

**Een autorisatie voor een verbinding verwijderen**

U kunt een verbinding verwijderen door het pictogram **Verwijderen** te selecteren voor de autorisatiesleutel voor uw verbinding.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Autorisatiesleutel verwijderen":::

Als u de verbinding wilt verwijderen maar de autorisatiesleutel wilt bewaren, kunt u de verbinding verwijderen van de verbindingspagina van het circuit.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Verbinding verwijderen die circuit bezit":::

### <a name="circuit-user-operations"></a>Bewerkingen door circuitgebruikers

De circuitgebruiker heeft de resource-id en een autorisatiesleutel nodig van de circuiteigenaar.

**Een autorisatie voor een verbinding inwisselen**

1. Selecteer de knop **+ Een resource maken**. Zoek naar **Verbinding** en selecteer **Maken**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Nieuwe resources maken":::

1. Zorg ervoor dat het *Verbindingstype* is ingesteld op **ExpressRoute**. Selecteer de *Resourcegroep* en *Locatie*, en selecteer vervolgens **OK** op de pagina Basisinformatie.

    > [!NOTE]
    > De locatie *moet* overeenkomen met de locatie van de virtuele-netwerkgateway waarvoor u de verbinding maakt.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Pagina Basisinformatie":::

1. Selecteer op de pagina **Instellingen** de *virtuele netwerkgateway* en schakel het selectievakje **Autorisatie inwisselen** in. Voer de *autorisatiesleutel* en de *URI van het peer-circuit* in en type een naam voor de verbinding. Selecteer **OK**. 
 
    > [!NOTE]
    > De *URI van het peer-circuit* is de resource-id van het ExpressRoute-circuit (u kunt deze vinden onder het deelvenster Instelling eigenschappen van het ExpressRoute-circuit).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Pagina Instellingen":::

1. Lees de informatie op de pagina **Samenvatting** en selecteer **OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Overzichtspagina":::

## <a name="clean-up-resources"></a>Resources opschonen

U kunt een verbinding verwijderen en uw VNet loskoppelen van een ExpressRoute-circuit door op de pagina voor uw verbinding het pictogram **Verwijderen** te selecteren.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Verbinding verwijderen":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een virtueel netwerk verbindt met een circuit in hetzelfde abonnement en in een ander abonnement. Zie 

> [!div class="nextstepaction"]
> [Info over virtuele ExpressRoute-netwerkgateways](expressroute-about-virtual-network-gateways.md) voor meer informatie over de virtuele ExpressRoute-netwerkgateway
