---
title: De implementatie van Azure VMware Solution plannen
description: In dit artikel vindt u een overzicht van de implementatiewerkstroom voor Azure VMware Solution.  Het uiteindelijke resultaat is een omgeving die gereed is om virtuele machines te maken en te migreren.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107241"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>De implementatie van Azure VMware Solution plannen

In dit artikel wordt het plannings proces beschreven voor het identificeren en verzamelen van de informatie die u tijdens de implementatie gaat gebruiken. Zorg dat u bij het plannen van uw implementatie alle verzamelde informatie documenteert om er tijdens de implementatie snel naar te kunnen verwijzen.

Met de stappen die worden beschreven in deze Quick start krijgt u een omgeving die gereed is voor productie voor het maken van virtuele machines (Vm's) en migratie. 

Als u de gegevens wilt bijhouden die u gaat verzamelen, kunt u de [controle lijst voor HCX planning](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/)ophalen.

> [!IMPORTANT]
> Het is belang rijk dat u een quotum voor een host opvraagt wanneer u de Azure VMware-oplossings resource hebt voor bereid. U kunt nu een quotum voor een host aanvragen, dus wanneer het plannings proces is voltooid, bent u klaar om de privécloud van de Azure VMware-oplossing te implementeren. Nadat het ondersteunings team uw aanvraag voor een quotum voor een host heeft ontvangen, duurt het Maxi maal vijf werk dagen om uw aanvraag te bevestigen en uw hosts toe te wijzen. Als u een bestaande privécloud van Azure VMware-oplossing hebt en meer hosts wilt toewijzen, voltooit u hetzelfde proces. Zie de volgende koppelingen voor meer informatie, afhankelijk van het type abonnement dat u hebt:
> - [EA-klanten](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [CSP-klanten](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>Abonnement

Kies het abonnement dat u wilt gebruiken om Azure VMware Solution te implementeren.  U kunt een nieuw abonnement maken of een bestaand gebruiken.

>[!NOTE]
>Het abonnement moet zijn gekoppeld aan een Microsoft Enterprise Agreement of een Cloud Solution Provider Azure-abonnement. Zie [Een Azure VMware Solution-resource inschakelen](enable-azure-vmware-solution.md) voor meer informatie.

## <a name="resource-group"></a>Resourcegroep

Kies de resourcegroep die u wilt gebruiken voor uw Azure VMware Solution.  Over het algemeen wordt een resourcegroep specifiek voor Azure VMware Solution gemaakt, maar u kunt een bestaande resourcegroep gebruiken.

## <a name="region"></a>Regio

Bepaal de regio waarin u de Azure VMware Solution wilt implementeren.  Zie de[Gids met Azure-producten beschikbaar per regio](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware) voor meer informatie.

## <a name="resource-name"></a>Resourcenaam

Definieer de naam van de resource die u tijdens de implementatie wilt gebruiken.  De resourcenaam is een gemakkelijke en beschrijvende naam voor uw Azure VMware Solution-privécloud.

>[!IMPORTANT]
>De naam mag maximaal 40 tekens lang zijn. Als de naam deze limiet overschrijdt, kunt u geen openbare IP-adressen maken voor gebruik met de privécloud. 

## <a name="size-hosts"></a>Omvang van hosts

Bepaal de omvang van de hosts die u wilt gebruiken om Azure VMware Solution te implementeren.  Raadpleeg voor een volledige lijst de documentatie van [Azure VMware Solution-privéclouds en -clusters](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-clusters-and-hosts"></a>Aantal clusters en hosts

De eerste implementatie van de Azure VMware-oplossing die u doet, bestaat uit een privécloud met één cluster. Voor uw implementatie moet u het aantal hosts definiëren dat u op het eerste cluster wilt implementeren.

>[!NOTE]
>Het minimum aantal hosts per cluster is drie, en de maximum waarde is 16. Het maximum aantal clusters per privécloud is vier. 

Raadpleeg voor meer informatie de documentatie van [Azure VMware Solution-privéclouds en -clusters](concepts-private-clouds-clusters.md#clusters).

>[!TIP]
>U kunt het cluster altijd uitbreiden en later extra clusters toevoegen als u verder wilt dan het eerste implementatie nummer.

## <a name="ip-address-segment-for-private-cloud-management"></a>IP-adres segment voor privé-Cloud beheer

De eerste stap bij het plannen van de implementatie is de planning van de IP-segmentatie. Voor de Azure VMware-oplossing is een/22 CIDR-netwerk vereist. Deze adres ruimte is gehaald in kleinere netwerk segmenten (subnetten) en wordt gebruikt voor Azure VMware-oplossingen voor oplossings beheer, waaronder vCenter, VMware HCX, NSX-T en vMotion-functionaliteit. In de volgende visualisatie wordt het gebruik van dit segment gemarkeerd.

Dit/22 CIDR-netwerk adres blok mag niet overlappen met een bestaand netwerk segment dat u on-premises of in azure al hebt.

**Voorbeeld:** 10.0.0.0/22

Voor een gedetailleerde analyse van de manier waarop het/22 CIDR-netwerk is onderverdeeld per particuliere cloud [netwerk planning controle lijst](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificeren - IP-adressegment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-adressegment voor workloads van virtuele machines

Net als bij elke VMware-omgeving moeten de virtuele machines verbinding maken met een netwerk segment. In azure VMware-oplossing zijn er twee soorten segmenten: L2-uitgebreide segmenten (later besproken) en NSX-T-netwerk segmenten. Naarmate de productie-implementatie van de Azure VMware-oplossing uitbreidt, is er vaak een combi natie van L2-uitgebreide segmenten van on-premises en lokale NSX-T-netwerk segmenten. Als u de eerste implementatie wilt plannen, identificeert u in de Azure VMware-oplossing één netwerk segment (IP-netwerk). Dit netwerk mag niet overlappen met netwerk segmenten op locatie of in de rest van Azure en mag niet binnen het eerder gedefinieerde/22-netwerk segment vallen.

Dit netwerk segment wordt hoofd zakelijk gebruikt voor test doeleinden tijdens de eerste implementatie.

>[!NOTE]
>Dit netwerk of deze netwerken zijn niet nodig tijdens de implementatie. Ze worden gemaakt als een stap na de implementatie.
  
**Voorbeeld:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificeren - IP-adressegment voor workloads van virtuele machines" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Azure Virtual Network koppelen aan de Azure VMware-oplossing

Om verbinding te kunnen maken met de Azure VMware-oplossing, wordt een ExpressRoute gemaakt op basis van de privécloud van Azure VMware-oplossing voor een virtuele ExpressRoute-netwerk gateway.

U kunt een *bestaande* of *nieuwe* ExpressRoute virtuele netwerk gateway gebruiken.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identiteit - Azure Virtual Network om Azure VMware Solution toe te voegen" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Een bestaande virtuele ExpressRoute-netwerk gateway gebruiken

Als u van plan bent een *bestaande* virtuele ExpressRoute-netwerk gateway te gebruiken, wordt de Azure VMware Solution ExpressRoute-circuit ingesteld als een stap na de implementatie. Laat in dit geval het veld **Virtual Network** leeg.

Als algemene aanbeveling is het niet toegestaan om een bestaande ExpressRoute-gateway van een virtueel netwerk te gebruiken. Voor plannings doeleinden noteert u welke ExpressRoute virtuele netwerk gateway u gaat gebruiken en gaat u verder met de [volgende stap](#vmware-hcx-network-segments).

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Een nieuwe virtuele ExpressRoute-netwerk gateway maken

Wanneer u een *nieuwe* virtuele ExpressRoute-netwerk gateway maakt, kunt u een bestaande Azure-Virtual Network gebruiken of een nieuwe maken.  

- Voor een bestaand virtueel Azure-netwerk:
   1. Een virtueel Azure-netwerk identificeren waarbij er geen bestaande virtuele netwerk gateways voor ExpressRoute zijn.
   2. Voordat u de implementatie implementeert, maakt u een [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) in azure Virtual Network.

- Voor een nieuwe Azure-Virtual Network en een virtuele netwerk gateway maakt u die tijdens de implementatie door de **nieuwe koppeling maken** te selecteren in de lijst **Virtual Network** .  Het is belang rijk om de adres ruimte en subnetten vooraf van de implementatie te definiëren, zodat u deze gegevens kunt invoeren wanneer u de implementaties tappen voltooit.

In de volgende afbeelding ziet u het scherm **een Privécloud maken** met het **Virtual Network** veld gemarkeerd.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Scherm afbeelding van het implementatie scherm van de Azure VMware-oplossing met Virtual Network veld gemarkeerd.":::

> [!NOTE]
> Elk virtueel netwerk dat wordt gebruikt of gemaakt, kan worden gezien door uw on-premises omgeving en Azure VMware-oplossing. Zorg er dus voor dat het IP-segment dat u in dit virtuele netwerk gebruikt en dat subnetten elkaar niet overlappen.

## <a name="vmware-hcx-network-segments"></a>VMware HCX-netwerksegmenten

VMware HCX is een technologie die is gebundeld met de Azure VMware-oplossing. De voornaamste use cases voor VMware HCX zijn migraties van workloads en herstel na noodgevallen. Als u van plan bent een van die twee te doen, dan kunt u het netwerken best nu al plannen. Als dat niet het geval is, kunt u dit overslaan en naar de volgende stap gaan.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>Beschrijving Uw netwerken uitbreiden

U kunt netwerk segmenten uitbreiden van on-premises naar Azure VMware-oplossing. Als u netwerk segmenten uitbreidt, moet u deze netwerken nu identificeren.  

Hier volgen enkele factoren waarmee u rekening moet houden:

- Als u van plan bent om netwerken van on-premises uit te breiden, moeten die netwerken verbinding maken met een [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) in uw on-premises VMware-omgeving.  
- Netwerken die zich op een [vSphere-standaard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) bevinden, kunnen niet worden uitgebreid.

>[!NOTE]
>Deze netwerken worden uitgebreid als laatste stap in de configuratie, niet tijdens de implementatie.
>
## <a name="next-steps"></a>Volgende stappen
Nu u de benodigde informatie hebt verzameld en gedocumenteerd, gaat u verder naar de volgende sectie om uw Azure VMware Solution-privécloud te maken.

> [!div class="nextstepaction"]
> [Azure VMware Solution implementeren](deploy-azure-vmware-solution.md)
> 
