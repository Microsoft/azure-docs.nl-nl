---
title: De implementatie van Azure VMware Solution plannen
description: In dit artikel vindt u een overzicht van de implementatiewerkstroom voor Azure VMware Solution.  Het uiteindelijke resultaat is een omgeving die gereed is om virtuele machines te maken en te migreren.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 08a15e6f8cad4068415cec3353544829f2218fb0
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888977"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>De implementatie van Azure VMware Solution plannen

In dit artikel wordt het planningsproces getoond om gegevens die gebruikt worden tijdens de implementatie te identificeren en te verzamelen. Zorg dat u bij het plannen van uw implementatie alle verzamelde informatie documenteert om er tijdens de implementatie snel naar te kunnen verwijzen.

De processen van deze quickstart zorgen voor een omgeving die gereed is om virtuele machines (VM's) te maken en te migreren. 

>[!IMPORTANT]
>Voordat u uw Azure VMware Solution-resource maakt, is het raadzaam het artikel [Een Azure VMware Solution-resouce inschakelen](enable-azure-vmware-solution.md) te volgen om een ondersteuningsticket in te dienen om uw hosts te laten toewijzen. Zodra het ondersteuningsteam uw aanvraag heeft ontvangen, duurt het maximaal vijf werkdagen om uw aanvraag te bevestigen en uw hosts toe te wijzen. Als u een bestaande privécloud van Azure VMware Solution hebt en u meer hosts wilt toewijzen, dan volgt u hetzelfde proces. 


## <a name="subscription"></a>Abonnement

Kies het abonnement dat u wilt gebruiken om Azure VMware Solution te implementeren.  U kunt een nieuw abonnement maken of een bestaand gebruiken.

>[!NOTE]
>Het abonnement moet gekoppeld zijn aan een Microsoft Enterprise Agreement.

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

## <a name="number-of-hosts"></a>Aantal hosts

Definieer het aantal hosts dat u wilt implementeren in de Azure VMware Solution-privécloud.  Het minimum aantal knooppunten is drie, en het maximum is 16 per cluster.  Raadpleeg voor meer informatie de documentatie van [Azure VMware Solution-privéclouds en -clusters](concepts-private-clouds-clusters.md#clusters).

U kunt de cluster later altijd uitbreiden als u verder moet gaan dan het oorspronkelijke implementatie-aantal.

## <a name="vcenter-admin-password"></a>Beheerderswachtwoord van vCenter
Stel het beheerderswachtwoord van vCenter in.  Tijdens de implementatie maakt u een beheerderswachtwoord voor vCenter. Het wacht woord is voor het cloudadmin@vsphere.local-beheerdersaccount tijdens de vCenter-build. U gebruikt het om u aan te melden bij vCenter.

## <a name="nsx-t-admin-password"></a>NSX-T-beheerderswachtwoord
Definieer het NSX-T-beheerderswachtwoord.  Tijdens de implementatie maakt u een NSX-T-beheerderswachtwoord. Het wachtwoord wordt toegewezen aan de gebruiker met beheerdersrechten in het NSX-account tijdens de NSX-build. U gebruikt het om u aan te melden bij NSX-T Manager.

## <a name="ip-address-segment"></a>Segment IP-adres

De eerste stap bij het plannen van de implementatie is de planning van de IP-segmentatie.  De Azure VMware Solution neemt een /22-netwerk op dat u opgeeft. Vervolgens deelt het dat op in kleinere segmenten, en gebruikt het die IP-segmenten voor vCenter, VMware, HCX, NSX-T en vMotion.

Azure VMware Solution maakt verbinding met uw Microsoft Azure Virtual Network via een intern ExpressRoute-circuit. In de meeste gevallen maakt het verbinding met uw gegevenscentrum via ExpressRoute Global Reach. 

Azure VMware Solution, uw bestaande Azure-omgeving en uw on-premises omgeving wisselen (meestal) allemaal routes uit. Dat wil zeggen dat het adresblok voor het /22 CIDR-netwerkadres dat u in deze stap definieert, niet mag overlappen met iets wat u al on-premises of in Azure hebt.

**Voorbeeld:** 10.0.0.0/22

Zie de [Controlelijst voor netwerkplanning](tutorial-network-checklist.md#routing-and-subnet-considerations) voor meer informatie.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificeren - IP-adressegment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-adressegment voor workloads van virtuele machines

Identificeer een IP-segment om uw eerste netwerk (NSX-segment) in uw privécloud te maken.  U moet met andere woorden een netwerksegment maken op Azure VMware Solution, zodat u virtuele machines kunt implementeren in Azure VMware Solution.   

Zelfs als u alleen van plan bent om L2-netwerken uit te breiden, maakt u een netwerksegment waarmee de omgeving kan worden gevalideerd.

Houd er rekening mee dat alle gemaakte IP-segmenten uniek moeten zijn binnen uw configuratie in Azure en on-premises.  

**Voorbeeld:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificeren - IP-adressegment voor workloads van virtuele machines" border="false":::     

## <a name="optional-extend-networks"></a>(Optioneel) Netwerken uitbreiden

U kunt netwerksegmenten van on-premises uitbreiden naar Azure VMware Solution. Als u dit doet, moet u deze netwerken nu identificeren.  

Denk hierbij aan het volgende:

- Als u van plan bent om netwerken van on-premises uit te breiden, moeten die netwerken verbinding maken met een [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) in uw on-premises VMware-omgeving.  
- Als het netwerk/de netwerken die u wilt uitbreiden zich op een [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) bevinden, dan kunnen ze niet worden uitgebreid.

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure Virtual Network om Azure VMware Solution toe te voegen

Om toegang te krijgen tot uw Azure VMware Solution-privécloud, moet het ExpressRoute-circuit, dat wordt geleverd met Azure VMware Solution, gekoppeld worden aan een Azure Virtual Network.  Tijdens de implementatie kunt u een nieuw virtueel netwerk definiëren of er een bestaande selecteren.

Het ExpressRoute-circuit van Azure VMware Solution maakt verbinding met een ExpressRoute-gateway in het Azure Virtual Network dat u in deze stap definieert.  

>[!IMPORTANT]
>U kunt een bestaande ExpressRoute-gateway gebruiken om verbinding te maken met Azure VMware Solution zolang u de limiet van vier ExpressRoute-circuits per virtueel netwerk niet overschrijdt.  Om toegang te krijgen tot Azure VMware Solution on-premises via ExpressRoute, moet u beschikken over ExpressRoute Global Reach, omdat de ExpressRoute-gateway geen transitieve routering biedt tussen de verbonden circuits.  

Als u het ExpressRoute-circuit van Azure VMware Solution wilt verbinden met een bestaande ExpressRoute-gateway, dan kunt u dit doen na de implementatie.  

Samengevat: u wilt Azure VMware Solution verbinden met een bestaande ExpressRoute-gateway?  

* **Ja** = Identificeer het virtueel netwerk dat tijdens de implementatie niet wordt gebruikt.
* **Nee** = Selecteer een bestaand virtueel netwerk of maak er een nieuw tijdens de implementatie.

In beide gevallen kunt u documenteren wat u in deze stap wilt doen.

>[!NOTE]
>Dit virtueel netwerk is zichtbaar voor uw on-premises omgeving en Azure VMware Solution. Zorg er dus voor dat het IP-segment dat u in dit virtueel netwerk gebruikt en subnetten niet overlappen.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identiteit - Azure Virtual Network om Azure VMware Solution toe te voegen" border="false":::

## <a name="vmware-hcx-network-segments"></a>VMware HCX-netwerksegmenten

VMware HCX is een technologie die deel uitmaakt van Azure VMware Solution. De voornaamste use cases voor VMware HCX zijn migraties van workloads en herstel na noodgevallen. Als u van plan bent een van die twee te doen, dan kunt u het netwerken best nu al plannen.   Als dat niet het geval is, kunt u dit overslaan en naar de volgende stap gaan.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u de benodigde informatie hebt verzameld en gedocumenteerd, gaat u verder naar de volgende sectie om uw Azure VMware Solution-privécloud te maken.

> [!div class="nextstepaction"]
> [Azure VMware Solution implementeren](deploy-azure-vmware-solution.md)
