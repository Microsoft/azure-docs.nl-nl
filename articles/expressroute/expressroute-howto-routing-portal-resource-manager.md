---
title: 'Zelfstudie: Peering configureren voor een ExpressRoute-circuit - Azure-portal'
description: In deze zelfstudie wordt uitgelegd hoe u persoonlijke peering en Microsoft-peering voor ExpressRoute kunt maken en inrichten met behulp van de Azure-portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: 3e106bc313d6189b2fae8cf6a4dd7ce862294b8f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019235"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Zelfstudie: Peering voor een ExpressRoute-circuit maken en wijzigen met de Azure-portal

In deze zelfstudie leert u hoe u de routeringsconfiguratie voor een Azure Resource Manager ExpressRoute-circuit maakt en beheert met behulp van de Azure-portal. U kunt ook de status van de peering voor een ExpressRoute-circuit controleren en peerings bijwerken of verwijderen en de inrichting ervan ongedaan maken. Als u een andere methode voor uw circuit wilt gebruiken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Openbare peering](about-public-peering.md)
> * [Video - Persoonlijke peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

U kunt persoonlijke peering en Microsoft-peering configureren voor een ExpressRoute-circuit (openbare Azure-peering is afgeschaft voor nieuwe circuits). Peerings kunnen in elke gewenste volgorde worden geconfigureerd. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Zie [ExpressRoute-routeringsdomeinen](expressroute-circuit-peerings.md) voor meer informatie over routeringsdomeinen en peerings. Zie [Openbare peering van ExpressRoute](about-public-peering.md) voor meer informatie over openbare peering.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Microsoft-peering voor een circuit configureren, bijwerken en verwijderen
> - Persoonlijke Azure-peering voor een circuit configureren, bijwerken en verwijderen

## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat u de volgende pagina's hebt gecontroleerd voordat u met de configuratie begint:
    * [Vereisten](expressroute-prerequisites.md) 
    * [Routeringsvereisten](expressroute-routing.md)
    * [Werkstromen](expressroute-workflows.md)
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet ingericht en ingeschakeld zijn om peering(s) te kunnen configureren. 
* Als u een gedeelde sleutel/MD5-hash wilt gebruiken, moet u ervoor zorgen dat u de sleutel aan beide zijden van de tunnel gebruikt. De limiet is 25 alfanumerieke tekens. Speciale tekens worden niet ondersteund. 

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde laag-3-services aanbiedt (meestal een IPVPN, zoals MPLS), wordt de routering door de connectiviteitsprovider geconfigureerd en beheerd. 

> [!IMPORTANT]
> Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw serviceprovider voordat u BGP-peerings configureert.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Deze sectie helpt u bij het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor Microsoft-peering voor een ExpressRoute-circuit.

> [!IMPORTANT]
> Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle servicevoorvoegsels die worden geadverteerd via de Microsoft-peering, zelfs als er geen routefilters zijn gedefinieerd. Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voorvoegsels die worden geadverteerd totdat een routefilter aan het circuit is gekoppeld. Zie [Een routefilter configureren voor Microsoft-peering](how-to-routefilter-powershell.md) voor meer informatie.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Configureer het ExpressRoute-circuit. Controleer bij de **Providerstatus** of het circuit volledig is ingericht door de connectiviteitsprovider voordat u verdergaat.

   Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om Microsoft-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter niet routering voor u beheert, gaat u na het maken van het circuit verder met de volgende stappen.

   **Circuit - Providerstatus: Niet ingericht**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Schermopname met de overzichtspagina voor het ExpressRoute-democircuit met een rood vak dat markeert dat de Providerstatus is ingesteld op Niet ingericht":::

   **Circuit - Providerstatus: Ingericht**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Schermopname met de overzichtspagina voor het ExpressRoute-democircuit met een rood vak dat markeert dat de Providerstatus is ingesteld op Ingericht":::

2. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verdergaat.

   * Een paar /30-subnetten die eigendom zijn van u en die zijn geregistreerd in een RIR/IRR. Dit moeten geldige openbare IPv4-voorvoegsels zijn. Eén /30-subnet wordt gebruikt voor de primaire koppeling en het andere wordt gebruikt voor de secundaire koppeling. Vanuit deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien Microsoft de tweede bruikbare IP voor de eigen router gebruikt.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Zowel primaire als secundaire links moeten dezelfde VLAN-id gebruiken.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: U verstrekt een lijst van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. U kunt een met komma's gescheiden lijst verzenden als u een set voorvoegsels wilt verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.
   * **Optioneel -** Klant-ASN: Als u voorvoegsels adverteert die niet zijn geregistreerd op het AS-nummer van de peering, kunt u het AS-nummer opgeven waarbij ze zijn geregistreerd.
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * **Optioneel -** Een MD5-hash, als u er een wilt gebruiken.
3. U kunt de peering selecteren die u wilt configureren, zoals in het volgende voorbeeld wordt weergegeven. Selecteer de rij voor Microsoft-peering.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="De rij voor Microsoft-peering selecteren":::

4. Configureer Microsoft-peering. **Sla de configuratie op** nadat u alle parameters hebt opgegeven. In de volgende afbeelding ziet u een voorbeeldconfiguratie:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Microsoft-peering configureren validatie nodig":::

> [!IMPORTANT]
> Microsoft controleert of de opgegeven geadverteerde openbare voorvoegsels en peer-ASN (of klant-ASN) aan u zijn toegewezen in het IRR (Internet Routing Registry). Als u de openbare voorvoegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routeringsregister, wordt de automatische validatie niet voltooid en is handmatige validatie vereist. Als de automatische validatie mislukt, wordt het bericht Validatie vereist weergegeven. 
>
> Als u het bericht 'Validatie vereist' ziet, verzamelt u de documenten met de openbare voorvoegsels die zijn toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voorvoegsels in het routeringsregister en verzendt u deze documenten voor handmatige validatie door een ondersteuningsticket te openen. 
>

   Als het circuit moet worden gevalideerd, moet u een ondersteuningsticket openen om aan ons ondersteuningsteam aan te tonen dat u eigenaar bent van de voorvoegsels. U kunt rechtstreeks vanuit de portal een ondersteuningsticket openen, zoals in het volgende voorbeeld wordt weergegeven:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Validatie vereist - ondersteuningsticket":::

5. Wanneer de configuratie is geaccepteerd, wordt iets soortgelijks als in de volgende afbeelding weergegeven:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Peeringstatus: Geconfigureerd":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering weergeven

U kunt de eigenschappen van Microsoft-peering weergeven door de rij voor de peering te selecteren.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Eigenschappen van Microsoft-peering weergeven":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U kunt de rij selecteren voor de peering die u wilt wijzigen, de eigenschappen van de peering aanpassen en vervolgens uw wijzigingen opslaan.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Rij van peering selecteren":::

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

Deze sectie helpt u bij het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een persoonlijke Azure-peering voor een ExpressRoute-circuit.

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Configureer het ExpressRoute-circuit. Zorg dat het circuit volledig is ingericht door de connectiviteitsprovider voordat u verder gaat. 

   Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om persoonlijke Azure-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter niet routering voor u beheert, gaat u na het maken van het circuit verder met de volgende stappen.

   **Circuit - Providerstatus: Niet ingericht**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Schermopname met de overzichtspagina voor het ExpressRoute-democircuit met een rood vak dat markeert dat de Providerstatus is ingesteld op Niet ingericht":::

   **Circuit - Providerstatus: Ingericht**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Schermopname met de overzichtspagina voor het ExpressRoute-democircuit met een rood vak dat markeert dat de Providerstatus is ingesteld op Ingericht":::

2. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een paar /30-subnetten die eigendom zijn van u en die zijn geregistreerd in een RIR/IRR. Eén /30-subnet wordt gebruikt voor de primaire koppeling en het andere wordt gebruikt voor de secundaire koppeling. Vanuit deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien Microsoft de tweede bruikbare IP voor de eigen router gebruikt. Er zijn drie opties voor dit paar subnetten:
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Zowel primaire als secundaire links moeten dezelfde VLAN-id gebruiken.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer gebruiken voor deze peering, behalve voor 65515 tot en met 65520.
   * U moet de routes van uw on-premises Edge-router naar Azure via BGP adverteren wanneer u persoonlijke peering configureert.
   * **Optioneel -** Een MD5-hash, als u er een wilt gebruiken.
3. Selecteer de rij voor persoonlijke Azure-peering, zoals wordt weergegeven in het volgende voorbeeld:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="De rij voor persoonlijke peering selecteren":::

4. Configureer persoonlijke Azure-peering. **Sla de configuratie op** nadat u alle parameters hebt opgegeven.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Persoonlijke peering configureren":::

5. Wanneer de configuratie is geaccepteerd, wordt iets soortgelijks als in het volgende voorbeeld weergegeven:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Opgeslagen persoonlijke peering":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U kunt de eigenschappen van persoonlijke Azure-peering weergeven door de peering te selecteren.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Eigenschappen van persoonlijke peering weergeven":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>De configuratie van persoonlijke Azure-peering bijwerken

U kunt de rij voor peering selecteren en de eigenschappen van de peering wijzigen. Sla na het bijwerken uw wijzigingen op.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Persoonlijke peering bijwerken":::

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U kunt uw Microsoft-peeringconfiguratie verwijderen door met de rechtermuisknop op de peering te klikken en **Verwijderen** te selecteren, zoals in de volgende afbeelding:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Microsoft-peering verwijderen":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt uw persoonlijke peeringconfiguratie verwijderen door met de rechtermuisknop op de peering te klikken en **Verwijderen** te selecteren, zoals in de volgende afbeelding:

> [!WARNING]
> Zorg ervoor dat alle virtuele netwerken en ExpressRoute Global Reach-verbindingen zijn verwijderd voordat u deze bewerking uitvoert. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Persoonlijke peering verwijderen":::

## <a name="next-steps"></a>Volgende stappen

Nadat u persoonlijke Azure-peering hebt geconfigureerd, kunt u een ExpressRoute-gateway maken om een virtueel netwerk aan het circuit te koppelen. 

> [!div class="nextstepaction"]
> [Een virtuele netwerkgateway configureren voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
