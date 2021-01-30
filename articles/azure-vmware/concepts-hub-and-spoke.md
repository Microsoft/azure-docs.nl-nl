---
title: 'Concept: een implementatie van een Azure VMware-oplossing integreren in een hub-en spoke-architectuur'
description: Meer informatie over het integreren van een implementatie van een Azure VMware-oplossing in een hub-en spoke-architectuur in Azure.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062742"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Azure VMware-oplossing integreren in een hub-en spoke-architectuur

Dit artikel bevat aanbevelingen voor het integreren van een implementatie van een Azure VMware-oplossing in een bestaande of een nieuwe [hub-en spoke-architectuur](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) in Azure. 


In het hub-en-spoke-scenario wordt uitgegaan van een hybride cloud omgeving met workloads op:

* Systeem eigen Azure met IaaS-of PaaS-Services
* Azure VMware Solution 
* on-premises vSphere

## <a name="architecture"></a>Architectuur

De *hub* is een Azure-Virtual Network die fungeert als een centraal punt van connectiviteit met uw on-premises en Azure VMware-oplossing privécloud. De *spokes* zijn virtuele netwerken die zijn gekoppeld aan de hub om intervirtuele netwerk communicatie in te scha kelen.

Verkeer tussen het on-premises Data Center, de privécloud van Azure VMware-oplossing en de hub loopt via Azure ExpressRoute-verbindingen. Spoke Virtual Networks bevatten doorgaans IaaS werk belastingen, maar kunnen PaaS-services zoals [app service Environment](../app-service/environment/intro.md)hebben, die directe integratie met Virtual Network hebben, of andere PaaS-services waarvoor [Azure private link](../private-link/index.yml) is ingeschakeld.

>[!IMPORTANT]
>U kunt een bestaande ExpressRoute-gateway gebruiken om verbinding te maken met Azure VMware Solution zolang u de limiet van vier ExpressRoute-circuits per virtueel netwerk niet overschrijdt.  Om toegang te krijgen tot Azure VMware Solution on-premises via ExpressRoute, moet u beschikken over ExpressRoute Global Reach, omdat de ExpressRoute-gateway geen transitieve routering biedt tussen de verbonden circuits.

In het diagram ziet u een voor beeld van een hub-en spoke-implementatie in azure die is verbonden met on-premises en Azure VMware-oplossing via ExpressRoute Global Reach.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Implementatie van Azure VMware Solution hub en spoke-integratie" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

De architectuur heeft de volgende hoofd onderdelen:

- **On-premises site:** On-premises data centers van de klant die zijn verbonden met Azure via een ExpressRoute-verbinding.

- **Privécloud van Azure VMware-oplossing:** Azure VMware-oplossing SDDC gevormd door een of meer vSphere-clusters, die elk een maximum van 16 hosts hebben.

- **ExpressRoute-gateway:** Hiermee schakelt u de communicatie tussen de privécloud-oplossing van Azure VMware, gedeelde services op het virtuele hub-netwerk en workloads die worden uitgevoerd op spoke virtuele netwerken.

- **ExpressRoute Global Reach:** Hiermee schakelt u de connectiviteit tussen on-premises en Azure VMware-oplossing privécloud. De connectiviteit tussen de Azure VMware-oplossing en de Azure-infra structuur is alleen ExpressRoute Global Reach. U kunt geen optie selecteren buiten het snelle pad ExpressRoute.  ExpressRoute direct wordt niet ondersteund.


- **Overwegingen voor S2S-VPN:** Voor implementaties van Azure VMware-oplossingen productie wordt Azure S2S VPN niet ondersteund vanwege netwerk vereisten voor VMware-HCX. U kunt het echter gebruiken voor een haalbaarheids implementatie.


- **Virtueel netwerk hub:** Fungeert als het centrale punt van connectiviteit naar uw on-premises netwerk en Azure VMware-oplossing privécloud.

- **Spoke-virtueel netwerk**

    - **IaaS Spaak:** Een IaaS-spoke fungeert als host voor Azure IaaS-workloads, waaronder VM-beschikbaarheids sets en schaal sets voor virtuele machines en de bijbehorende netwerk onderdelen.

    - **PaaS Spaak:** Een PaaS Spaak fungeert als host voor Azure PaaS Services met privé-adres Sering dankzij het [persoonlijke eind punt](../private-link/private-endpoint-overview.md) en de [persoonlijke koppeling](../private-link/private-link-overview.md).

- **Azure firewall:** Fungeert als het centrale stuk om het verkeer tussen de spokes en de Azure VMware-oplossing te segmenteren.

- **Application Gateway:** Beschrijft en beveiligt web-apps die worden uitgevoerd op Azure IaaS/PaaS of virtuele machines met Azure VMware-oplossingen. Het is geïntegreerd met andere services, zoals API Management.

## <a name="network-and-security-considerations"></a>Netwerk-en beveiligings overwegingen

Met ExpressRoute-verbindingen kunnen verkeer tussen on-premises, Azure VMware-oplossingen en de Azure-netwerk infrastructuur worden geplaatst. De Azure VMware-oplossing maakt gebruik van [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) voor het implementeren van deze verbinding.

Omdat een ExpressRoute-gateway geen transitieve route ring biedt tussen de verbonden circuits, moet on-premises connectiviteit ook ExpressRoute Global Reach gebruiken om te communiceren tussen de on-premises vSphere-omgeving en de Azure VMware-oplossing. 

* **Verkeers stroom van on-premises naar Azure VMware-oplossing**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="Verkeers stroom van on-premises naar Azure VMware-oplossing" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Azure VMware-oplossing voor hub VNET-verkeers stroom**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware-oplossing voor het hub-verkeer van virtuele netwerken" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Raadpleeg de [product documentatie voor Azure VMware-oplossingen](./concepts-networking.md)voor meer informatie over de netwerk-en connectiviteits concepten van Azure VMware-oplossingen.

### <a name="traffic-segmentation"></a>Segmentatie van verkeer

[Azure firewall](../firewall/index.yml) is het centrale onderdeel van de hub-en spoke-topologie, geïmplementeerd op het virtuele hub-netwerk. Gebruik Azure Firewall of een ander door Azure ondersteund virtueel netwerk apparaat om verkeers regels vast te leggen en de communicatie tussen de verschillende spokes en de workloads van Azure VMware-oplossingen te segmenteren.

Maak route tabellen om het verkeer naar Azure Firewall te sturen.  Maak voor de spoke-virtuele netwerken een route waarmee de standaard route wordt ingesteld op de interne interface van Azure Firewall. Op deze manier kan de firewall, wanneer een werk belasting in de Virtual Network de adres ruimte van de Azure VMware-oplossing moet bereiken, deze evalueren en de bijbehorende verkeers regel Toep assen om deze toe te staan of te weigeren.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Route tabellen maken voor het omleiden van verkeer naar Azure Firewall" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> Een route met het adres voorvoegsel 0.0.0.0/0 voor de **GatewaySubnet** -instelling wordt niet ondersteund.

Routes instellen voor specifieke netwerken op de bijbehorende route tabel. Bijvoorbeeld: routes voor het bereiken van Azure VMware-oplossings beheer en werk belastingen IP-voor voegsels van de spoke-workloads en de andere manier.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Routes instellen voor specifieke netwerken op de bijbehorende route tabel" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

Een tweede niveau van Traffic segmentatie met behulp van de netwerk beveiligings groepen binnen de spokes en de hub om een meer gedetailleerd verkeers beleid te maken.

> [!NOTE]
> **Verkeer van on-premises naar Azure VMware-oplossing:** Verkeer tussen on-premises workloads, ofwel vSphere of anderen, wordt ingeschakeld door Global Reach, maar het verkeer gaat niet via Azure Firewall op de hub. In dit scenario moet u segmentatie mechanismen voor verkeer implementeren, hetzij on-premises als in azure VMware-oplossing.

### <a name="application-gateway"></a>Application Gateway

Azure-toepassing gateway v1 en v2 zijn getest met web apps die worden uitgevoerd op virtuele machines uit de Azure VMware-oplossing als back-end-groep. Application Gateway is momenteel de enige methode die wordt ondersteund om web-apps die worden uitgevoerd op virtuele machines van Azure VMware-oplossingen, beschikbaar te maken op internet. Ook kunnen de apps veilig worden blootgesteld aan interne gebruikers.

Zie het artikel over de oplossings specificaties van Azure VMware op [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md)voor meer informatie.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Tweede niveau van verkeers segmentatie met behulp van de netwerk beveiligings groepen" border="false":::


### <a name="jump-box-and-azure-bastion"></a>Jump box en Azure Bastion

Toegang tot de Azure VMware-oplossings omgeving met een Jump box, een Windows 10-of Windows Server-VM die is geïmplementeerd in het subnet van de gedeelde service binnen het virtuele hub-netwerk.

>[!IMPORTANT]
>Azure Bastion is de aanbevolen service om verbinding te maken met het Jump box om te voor komen dat de Azure VMware-oplossing aan Internet wordt blootgesteld. U kunt Azure Bastion niet gebruiken om verbinding te maken met virtuele machines van Azure VMware-oplossingen omdat ze geen Azure IaaS-objecten zijn.  

Implementeer [Microsoft Azure Bastion](../bastion/index.yml) -service in het virtuele netwerk van de hub als beveiligings best practice. Azure Bastion biedt naadloze RDP-en SSH-toegang tot Vm's die zijn geïmplementeerd op Azure zonder dat open bare IP-adressen op deze resources hoeven te worden ingericht. Nadat u de Azure Bastion-service hebt ingericht, kunt u vanuit de Azure Portal toegang krijgen tot de geselecteerde VM. Nadat de verbinding tot stand is gebracht, wordt er een nieuw tabblad geopend, met daarin het bureau blad van de Jump box en van dat bureau blad, hebt u toegang tot het beheer vlak van de privécloud-oplossing van Azure VMware.

> [!IMPORTANT]
> Geef geen openbaar IP-adres toe aan de VM met het Jump box of stel de poort 3389/TCP beschikbaar op het open bare Internet. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Virtueel Azure Bastion hub-netwerk" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Overwegingen voor Azure DNS oplossingen

Voor Azure DNS oplossing zijn er twee opties beschikbaar:

-   Gebruik de domein controllers die zijn geïmplementeerd op de hub (beschreven in [identiteits overwegingen](#identity-considerations)) als naam servers.

-   Een Azure DNS privé zone implementeren en configureren.

U kunt het beste beide combi neren om een betrouw bare naam omzetting te bieden voor de Azure VMware-oplossing, on-premises en Azure.

Als algemene ontwerp aanbeveling gebruikt u de bestaande Azure DNS-infra structuur (in dit geval Active Directory-geïntegreerde DNS) die is geïmplementeerd op ten minste twee virtuele Azure-machines die zijn geïmplementeerd in het VM-netwerk en die in de spoke-virtuele netwerken zijn geconfigureerd voor het gebruik van deze Azure DNS servers in de DNS-instellingen.

U kunt Azure Privé-DNS gebruiken, waarbij de Azure Privé-DNS-zone aan het virtuele netwerk is gekoppeld.  De DNS-servers worden gebruikt als hybride resolvers met voorwaardelijk door sturen naar on-premises of Azure VMware-oplossing met behulp van de Azure Privé-DNS-infra structuur van de klant. 

Als u de levens cyclus van de DNS-records voor de Vm's die zijn geïmplementeerd in de spoke-virtuele netwerken automatisch wilt beheren, schakelt u automatische registratie in. Wanneer deze functie is ingeschakeld, is het maximum aantal privé-DNS-zones slechts één. Als deze is uitgeschakeld, is het maximum aantal 1000.

On-premises en Azure VMware-oplossingen kunnen worden geconfigureerd met voorwaardelijke doorstuur servers voor het oplossen van Vm's in azure voor de Azure Privé-DNS-zone.

## <a name="identity-considerations"></a>Identiteits overwegingen

Voor identiteits doeleinden is het de beste benadering om ten minste één domein controller op de hub te implementeren. Gebruik twee gedeelde service subnetten in zone-gedistribueerde mode of een beschik bare VM-set. Zie [Azure Architecture Center](/azure/architecture/reference-architectures/identity/adds-extend-domain)voor meer informatie over het uitbreiden van uw on-premises Active Directory (AD)-domein naar Azure.

Implementeer daarnaast een andere domein controller op de Azure VMware-oplossing zijde om als identiteits-en DNS-bron binnen de vSphere omgeving te fungeren.

Als aanbevolen best practice kunt u het [AD-domein integreren met Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
