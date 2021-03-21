---
title: 'Zelfstudie: Azure-ExpressRoute - Een gateway toevoegen aan een VNet (Azure-portal)'
description: In deze zelfstudie worden de stappen beschreven voor het toevoegen van een virtuele-netwerkgateway aan een VNet voor ExpressRoute met behulp van de Azure-portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 6c6969fdf413c4eb5e7bbcf046fc397834d6c0a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038914"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Zelfstudie: Een virtuele-netwerkgateway configureren voor ExpressRoute met behulp van de Azure-portal
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiek - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

In deze zelfstudie vindt u stapsgewijze instructies voor het toevoegen van een virtuele-netwerkgateway voor een al bestaand VNet. In dit artikel vindt u stapsgewijze instructies voor het toevoegen, vergroten/verkleinen en verwijderen van een gateway voor een virtueel netwerk (VNet) voor een al bestaand VNet. De stappen voor deze configuratie zijn specifiek voor VNets die zijn gemaakt met behulp van het Resource Manager-implementatiemodel dat wordt gebruikt in een ExpressRoute-configuratie. Zie het artikel over [virtuele-netwerkgateways voor ExpressRoute](expressroute-about-virtual-network-gateways.md) voor meer informatie over virtuele-netwerkgateways en configuratie-instellingen voor gateways voor ExpressRoute. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een gatewaysubnet maken.
> - De virtuele-netwerkgateway maken.

## <a name="prerequisites"></a>Vereisten

In de stappen voor deze taak gebruiken we een VNet dat is gebaseerd op de waarden in de volgende configuratiereferentielijst. We gebruiken deze lijst in onze voorbeeldstappen. U kunt de lijst kopiëren om deze als referentie te gebruiken, waarbij u de waarden vervangt door uw eigen waarden.

**Configuratiereferentielijst**

* Naam van virtueel netwerk = "TestVNet"
* Adresruimte van virtueel netwerk = 192.168.0.0/16
* Naam van subnet = "FrontEnd" 
    * Adresruimte van subnet = "192.168.1.0/24"
* Resourcegroep = "TestRG"
* Locatie = "US - oost"
* Naam van gatewaysubnet: "GatewaySubnet". U moet een gatewaysubnet altijd de naam *GatewaySubnet* geven.
    * Adresruimte van gatewaysubnet = "192.168.200.0/26"
* Naam van gateway = "ERGW"
* Naam van openbare IP van gateway = "MyERGWVIP"
* Type gateway = "ExpressRoute". Dit type is vereist voor een ExpressRoute-configuratie.

U kunt een [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) van deze stappen bekijken voordat u begint met de configuratie.

> [!IMPORTANT]
> IPv6-ondersteuning voor persoonlijke peering is momenteel beschikbaar als **open bare preview**. Als u uw virtuele netwerk wilt verbinden met een ExpressRoute-circuit met op IPv6 gebaseerde privé-peering geconfigureerd, zorgt u ervoor dat uw virtuele netwerk dual stack is en volgt u de richt lijnen voor [IPv6 voor Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).
> 
> 

## <a name="create-the-gateway-subnet"></a>Her gatewaysubnet maken

1. Navigeer in de [portal](https://portal.azure.com) naar het virtuele netwerk van Resource Manager waarvoor u een gateway wilt maken.
1. Ga naar de sectie **Instellingen** van uw VNet en selecteer **Subnetten** om de instellingen van het subnet uit te vouwen.
1. Selecteer in de instellingen onder **Subnetten** de optie **+ Gatewaysubnet** om een gatewaysubnet toe te voegen. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Het gatewaysubnet toevoegen":::

1. Als **naam** voor het subnet wordt automatisch de waarde GatewaySubnet ingevuld. Deze waarde is vereist. Zonder deze waarde wordt het subnet niet in Azure als het gatewaysubnet herkend. Pas de automatisch ingevulde waarden voor **Adresbereik** aan overeenkomstig uw configuratievereisten. We raden u aan een gatewaysubnet te maken met een adresbereik van /27 of groter (/26, /25, enzovoort).

    Als u een virtueel netwerk met dubbele stack gebruikt en op IPv6 gebaseerde privé-peering via ExpressRoute wilt gebruiken, klikt u op **ip6 adres ruimte toevoegen** en waarden voor het **IPv6-adres bereik** invoeren.

    Selecteer vervolgens **OK** om de waarden op te slaan en het gatewaysubnet te maken.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Het subnet toevoegen":::

## <a name="create-the-virtual-network-gateway"></a>De gateway van het virtuele netwerk maken

1. Klik in de portal aan de linkerkant op **Een resource maken** en typ 'virtuele netwerkgateway' in het zoekvak. Zoek **Virtuele netwerkgateway** in de zoekresultaten en selecteer de vermelding. Selecteer op de pagina **Virtuele netwerkgateway** de optie **Maken**.
1. Voer op de pagina **Virtuele netwerkgateway maken** de volgende instellingen in of selecteer ze:

    | Instelling | Waarde |
    | --------| ----- |
    | Abonnement | Controleer of het juiste abonnement is geselecteerd. |
    | Resourcegroep | De resourcegroep wordt automatisch geselecteerd wanneer u het virtuele netwerk selecteert. | 
    | Naam | Geef een naam op voor de gateway. Dit is iets anders dan een naam opgeven voor het gatewaysubnet. Hier gaat het om de naam van het gatewayobject dat u maakt.|
    | Regio | Wijzig het veld **Regio** om naar de locatie van het virtuele netwerk te verwijzen. Als de locatie niet verwijst naar de regio waarin het virtuele netwerk zich bevindt, wordt het virtuele netwerk niet weergegeven in de vervolgkeuzelijst Virtueel netwerk. |
    | Gatewaytype | Selecteer **ExpressRoute**|
    | SKU | Selecteer de gateway-SKU in de vervolgkeuzelijst. |
    | Virtueel netwerk | Selecteer *TestVNet*. |
    | Openbaar IP-adres | Selecteer **Nieuw maken**.|
    | Naam openbaar IP-adres | Geef een naam op voor het openbare IP-adres. |

    > [!IMPORTANT]
    > Als u van plan bent om op IPv6 gebaseerde privé-peering te gebruiken via ExpressRoute, moet u ervoor zorgen dat u een AZ-SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) voor **SKU** selecteert.
    > 
    > 

1. Selecteer **Beoordelen en maken** en daarna **Maken** om de gateway te maken. De instellingen worden gevalideerd en de gateway wordt geïmplementeerd. Het maken van een gateway voor een virtueel netwerk kan tot 45 minuten duren.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Velden op de pagina voor het maken van een gateway voor een virtueel netwerk":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de ExpressRoute-gateway niet meer nodig hebt, gaat u naar de gateway in de resourcegroep met het virtuele netwerk en selecteert u **Verwijderen**. Zorg ervoor dat de gateway geen verbindingen met een circuit heeft.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Virtuele-netwerkgateway verwijderen":::

## <a name="next-steps"></a>Volgende stappen
Als u de VNet-gateway hebt gemaakt, kunt u uw VNet koppelen aan een ExpressRoute-circuit. 

> [!div class="nextstepaction"]
> [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
