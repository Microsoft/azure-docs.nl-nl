---
title: 'Zelfstudie: netwerken configureren voor uw VMware-privécloud in Azure'
description: Meer informatie over het maken en configureren van de netwerken die nodig zijn voor het implementeren van uw privécloud in Azure
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: c609d191191cd9de111e83fe62761b149405cccc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494349"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Zelfstudie: Netwerken configureren voor uw VMware-privécloud in Azure

Voor een Azure VMware Solution-privécloud is een virtueel Azure-netwerk vereist. Azure VMware Solution biedt geen ondersteuning voor uw on-premises vCenter, dus zijn er aanvullende stappen nodig voor de integratie met uw on-premises omgeving. Het instellen van een ExpressRoute-circuit en een virtuele netwerk gateway is ook vereist.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * De gateway van een virtueel netwerk maken
> * Uw ExpressRoute-circuit met de gateway verbinden
> * De URL's voor vCenter en NSX Manager zoeken

## <a name="prerequisites"></a>Vereisten 
Een virtueel netwerk waarin u een [Azure VMware Solution-privécloud](tutorial-create-private-cloud.md) hebt gemaakt. 

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar de resourcegroep die u hebt gemaakt in de [zelfstudie over het maken van een privécloud](tutorial-create-private-cloud.md) en selecteer **+Toevoegen** om een nieuwe resource te definiëren. 

1. In het tekstvak **Marketplace doorzoeken** typt u **virtueel netwerk**. Zoek de resource voor het virtuele netwerk en selecteer deze.

1. Selecteer op de pagina **Virtueel netwerk** **Maken** om een virtueel netwerk voor uw privécloud in te stellen.

1. Voer op de pagina **Virtueel netwerk maken** de gegevens voor uw virtuele netwerk in.

1. Op het tabblad **basis beginselen** voert u een naam in voor het virtuele netwerk, selecteert u de gewenste regio en selecteert u **volgende: IP-adressen**.

1. Voer op het tabblad **IP-adressen**, onder **IPv4-adresruimte**, de adresruimte in die u in de vorige zelfstudie hebt gemaakt.

   > [!IMPORTANT]
   > U moet een adresruimte gebruiken die **niet** overlapt met de adresruimte die u hebt gebruikt bij het maken van de privécloud in de voorgaande zelfstudie.

1. Selecteer **+ Subnet toevoegen** en geef op de pagina **Subnet toevoegen** het subnet een naam en het juiste adresbereik. Als u klaar bent, selecteert u **Toevoegen**.

1. Selecteer **Controleren + maken**.

   Selecteer :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Controleren en maken" border="true":::.

1. Controleer de gegevens en selecteer **Maken**. Zodra de implementatie is voltooid, ziet u het virtuele netwerk in de resourcegroep.

## <a name="create-a-virtual-network-gateway"></a>De gateway van een virtueel netwerk maken

Nu u in de vorige sectie een virtueel netwerk hebt gemaakt, gaat u een gateway voor een virtueel netwerk maken.

1. Selecteer in de resourcegroep **+ Toevoegen** om een nieuwe resource toe te voegen.

1. Typ in het tekstvak **Marketplace doorzoeken** **gateway voor virtueel netwerk**. Zoek de resource voor het virtuele netwerk en selecteer deze.

1. Selecteer op de pagina **Gateway voor virtueel netwerk** de optie **Maken**.

1. Geef op het tabblad Basisinformatie van de pagina **Gateway voor virtueel netwerk maken** waarden op voor de velden en selecteer daarna **Controleren en maken**. 

   | Veld | Waarde |
   | --- | --- |
   | **Abonnement** | Vooraf ingevulde waarde met het abonnement waartoe de resourcegroep behoort. |
   | **Resourcegroep** | Vooraf ingevulde waarde voor de huidige resourcegroep. De waarde hoort de resourcegroep te zijn die u in een vorige test hebt gemaakt. |
   | **Naam** | Voer een unieke naam in voor de gateway voor het virtuele netwerk. |
   | **Regio** | Selecteer de geografische locatie van de gateway voor het virtuele netwerk. |
   | **Gatewaytype** | Selecteer **ExpressRoute**. |
   | **SKU** | Laat de standaardwaarde staan: **standaard**. |
   | **Virtueel netwerk** | Selecteer het virtuele netwerk dat u eerder hebt gemaakt. Als u het virtuele netwerk niet ziet, zorg er dan voor dat de regio van de gateway overeenkomt met de regio van het virtuele netwerk. |
   | **Adresbereik gatewaysubnet** | Deze waarde wordt ingevuld wanneer u het virtuele netwerk selecteert. Wijzig de standaardwaarde niet. |
   | **Openbaar IP-adres** | Selecteer **Nieuw maken**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Geef waarden op voor de velden en selecteer Controleren en maken." border="true":::

1. Controleer of de gegevens juist zijn en selecteer **Maken** om de implementatie van de gateway voor uw virtuele netwerk te starten. 
1. Zodra de implementatie is voltooid, gaat u naar de volgende sectie om uw ExpressRoute-verbinding te verbinden met de gateway van het virtuele netwerk dat uw Azure VMware Solution-privécloud bevat.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute verbinden met de gateway voor het virtuele netwerk

Nu u een gateway voor het virtueel netwerk hebt geïmplementeerd, voegt u een verbinding toe tussen de gateway en uw Azure VMware Solution-privécloud.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>De URL's voor vCenter en NSX Manager zoeken

Als u zich wilt aanmelden bij vCenter en NSX Manager, hebt u de Url's nodig voor de vCenter-webclient en de NSX-T-beheer site. 

Ga naar uw Azure VMware Solution-privécloud en selecteer onder **Beheren** de optie **Identiteit**. Hier vindt u de vereiste informatie.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Scherm afbeelding van de vCenter-en NSX-T-referenties en web client-Url's." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een virtueel netwerk maken
> * De gateway van een virtueel netwerk maken
> * Uw ExpressRoute-circuit met de gateway verbinden
> * De URL's voor vCenter en NSX Manager zoeken

Ga door naar de volgende zelfstudie voor meer informatie over het maken van de NSX-T-netwerksegmenten die worden gebruikt voor VM's in vCenter.

> [!div class="nextstepaction"]
> [Een NSX-T-netwerksegment maken](tutorial-nsx-t-network-segment.md)