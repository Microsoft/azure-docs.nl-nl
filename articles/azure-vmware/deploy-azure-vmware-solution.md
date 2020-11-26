---
title: Azure VMware Solution implementeren en configureren
description: Meer informatie over hoe u gegevens verzameld in de planningsfase kunt gebruiken voor het implementeren van de Azure VMware Solution-privécloud.
ms.topic: tutorial
ms.author: tredavis
ms.date: 11/09/2020
ms.openlocfilehash: 47fc894fa1d6204a1bc2c912da7fcbb2394627fa
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484216"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware Solution implementeren en configureren

In dit artikel gebruikt u de informatie in het onderdeel [planning](production-ready-deployment-steps.md) om Azure VMware Solution te implementeren. Als u de informatie nog niet hebt gedefinieerd, gaat u terug naar het gedeelte [planning](production-ready-deployment-steps.md) alvorens verder te gaan.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Azure VMware Solution implementeren

Gebruik de informatie die u hebt verzameld in het artikel [De implementatie van Azure VMware Solution plannen](production-ready-deployment-steps.md):

>[!NOTE]
>Als u Azure VMware Solution wilt implementeren, moet u minimum het niveau inzender hebben in het abonnement.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>Bekijk voor een volledig overzicht van deze stap de video [Azure VMware Solution Deployment](https://www.youtube.com/embed/gng7JjxgayI).

## <a name="create-the-jump-box"></a>De jumpbox maken

>[!IMPORTANT]
>Als u de optie **Virtueel netwerk** leeg hebt gelaten tijdens de eerste inrichtingsstap op het scherm **Een Privécloud maken**, voltooi dan de zelfstudie [Netwerken configureren voor uw VMware-privécloud](tutorial-configure-networking.md) **voordat** u verdergaat met dit onderdeel.  

Na de implementatie van Azure VMware Solution maakt u de jumpbox van het virtueel netwerk die verbinding maakt met vCenter en NSX. Wanneer u ExpressRoute-circuits en ExpressRoute Global Reach hebt geconfigureerd, is de jumpbox niet meer nodig.  Maar het is handig om vCenter en NSX te bereiken in uw Azure VMware Solution.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="De Azure VMware Solution-jumpbox maken" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Als u een virtuele machine (VM) wilt maken in het virtuele netwerk dat u [hebt geïdentificeerd of gemaakt tijdens het implementatieproces](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution), volg dan deze instructies: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Maak verbinding met een virtueel netwerk met ExpressRoute

Als u tijdens de implementatiestap geen virtueel netwerk hebt gedefinieerd en u de ExpressRoute van Azure VMware Solution wilt verbinden met een bestaande ExpressRoute-gateway, volg dan de onderstaande stappen.

Als u al een virtueel netwerk hebt gedefinieerd op het implementatiescherm in Azure, ga dan verder met het volgende onderdeel.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Geadverteerde netwerkroutes controleren

De jumpbox bevindt zich in het virtuele netwerk waar Azure VMware Solution verbinding maakt via het ExpressRoute-circuit.  Ga in Azure naar de netwerkinterface van de jumpbox en [bekijk de effectieve routes](../virtual-network/manage-route-table.md#view-effective-routes).

In de lijst met effectieve routes ziet u de netwerken die zijn gemaakt als onderdeel van de Azure VMware Solution-implementatie. U ziet meerdere netwerken die zijn afgeleid van het [`/22`netwerk dat u gedefinieerd hebt](production-ready-deployment-steps.md#ip-address-segment) tijdens de [implementatiestap](#deploy-azure-vmware-solution) eerder in dit artikel.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Controleer de netwerkroutes die zijn geadverteerd van Azure VMware Solution naar Azure Virtual Network" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

In dit voorbeeld werd het 10.74.72.0/22-netwerk ingevoerd tijdens de implementatie van de /24-netwerken.  Als u iets dergelijks ziet, kunt u verbinding maken met vCenter in Azure VMware Solution.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Verbinding maken en aanmelden bij vCenter en NSX-T

Meld u aan bij de jumpbox die u in de vorige stap hebt aangemaakt. Zodra u bent aangemeld, opent u een webbrowser en gaat u naar en meldt u zich aan bij de beheerconsole van vCenter en NSX-T.  

U kunt de IP-adressen en referenties van de beheerconsole van vCenter en NSX controleren in het Azure-portaal.  Selecteer uw privécloud en selecteer vervolgens in de weergave **Overzicht** de optie **Identiteit > Standaard**. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Een netwerksegment maken in Azure VMware Solution

U gebruikt NSX-T om nieuwe netwerksegmenten te maken in uw Azure VMware Solution-omgeving.  U hebt de netwerk(en) gedefinieerd die u wilt maken het [onderdeel planning](production-ready-deployment-steps.md).  Als u deze nog niet hebt gedefinieerd, gaat u terug naar het gedeelte [planning](production-ready-deployment-steps.md) alvorens verder te gaan.

>[!IMPORTANT]
>Zorg ervoor dat het adresblok dat u hebt gedefinieerd voor het CIDR-netwerk niet overlapt met iets in uw Azure-of on-premises omgevingen.  

Volg de zelfstudie [Een NSX-T-netwerksegment maken in Azure VMware Solution](tutorial-nsx-t-network-segment.md) om een NSX-T-netwerksegment te maken in Azure VMware Solution.

## <a name="verify-advertised-nsx-t-segment"></a>Geadverteerd NSX-T-segment controleren

Ga terug naar de stap[Geadverteerde netwerkroutes controleren](#verify-network-routes-advertised) U ziet een of meer route(s) in de lijst met de netwerksegment(en) die u in de vorige stap hebt gemaakt.  

Voor virtuele machines wijst u de segmenten toe die u hebt gemaakt in de stap [Een netwerksegment maken in Azure VMware Solution](#create-a-network-segment-on-azure-vmware-solution).  

Omdat DNS vereist is, identificeert u de DNS-server die u wilt gebruiken.  

- Als ExpressRoute Global Reach hebt geconfigureerd, gebruik dan de DNS-server die u on-premises gebruikt.  
- Als u een DNS-server in Azure hebt, gebruikt die dan.  
- Als u geen van beide hebt, gebruik dan de DNS-server die uw jumpbox gebruikt.

>[!NOTE]
>Deze stap dient om de DNS-server te identificeren. Er vinden geen configuraties plaats tijdens deze stap.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>(Optioneel) DHCP-services voorzien aan een NSX-T-netwerksegment

Als u DHCP wilt gebruiken op uw NSX-T-segment(en), ga dan verder met dit onderdeel. Zoniet, ga dan naar het onderdeel [Een virtuele machine toevoegen aan het NSX-T-netwerksegment](#add-a-vm-on-the-nsx-t-network-segment).  

Nu u het NSX-T-netwerksegment hebt gemaakt, kunt u op twee manieren DHCP in Azure VMware Solution maken en beheren:

* Als u NSX-T gebruikt om uw DHCP-server te hosten, moet u [een DHCP-server maken](manage-dhcp.md#create-a-dhcp-server) en [deze doorsturen naar die server](manage-dhcp.md#create-dhcp-relay-service). 
* Als u een externe DHCP-server van derden in uw netwerk gebruikt, moet u [een DHCP-doorgifteservice maken](manage-dhcp.md#create-dhcp-relay-service).  Voor deze optie [voert u enkel de relay-configuratie uit,](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Een virtuele machine toevoegen aan het NSX-T-netwerksegment

Implementeer in het vCenter van uw Azure VMware Solution een virtuele machine en gebruik deze om de verbinding te testen van uw Azure VMware Solution-netwerk(en) met:

- Het internet
- Virtuele netwerken van Azure
- On-premises.  

Implementeer de virtuele machine op dezelfde manier als in een vSphere-omgeving.  Koppel de virtuele machine aan een van de netwerksegment(en) die u eerder hebt gemaakt in NSX-T.  

>[!NOTE]
>Als u een DHCP-server instelt, levert deze de netwerkconfiguratie van de virtuele machine (vergeet niet om het bereik in te stellen).  Als u statisch gaat configureren, doe dat dan op de gebruikelijke manier.

## <a name="test-the-nsx-t-segment-connectivity"></a>Verbinding van NSX-T-segment testen

Meld u aan bij de virtuele machine die u in de vorige stap hebt gemaakt en controleer de verbinding.

1. Ping een IP-adres op het internet.
2. Ga naar een internetsite via een webbrowser.
3. Ping de jumpbox die zich op het Azure Virtual Network bevindt.

>[!IMPORTANT]
>Op dit moment is de Azure VMware Solution actief en hebt u verbinding gemaakt van en naar Azure Virtual Network en het internet.

## <a name="next-steps"></a>Volgende stappen

In het volgende onderdeel verbindt u Azure VMware Solution met uw on-premises netwerk via ExpressRoute.
> [!div class="nextstepaction"]
> [Azure VMware Solution verbinden met uw on-premises omgeving](azure-vmware-solution-on-premises.md)
