---
title: Wat is Azure Private Link?
description: Overzicht van Azure Private Link-functies, -architectuur en -implementatie. Meer informatie over hoe Azure privé-eindpunten en de Azure Private Link-service werken en hoe u deze kunt gebruiken.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 6a85bfe7b3390b32fc220000b0c710b5a4e35067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496487"
---
# <a name="what-is-azure-private-link"></a>Wat is Azure Private Link? 
Met Azure Private Link hebt u via een [privé-eindpunt](private-endpoint-overview.md) in uw virtuele netwerk toegang tot Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en in Azure gehoste services van klanten of partners.

Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst. U hoeft uw service niet langer bloot te stellen aan het openbare internet. U kunt een eigen [Private Link-service](private-link-service-overview.md) maken in uw virtuele netwerk en deze aanbieden bij klanten. Het instellen en gebruiken van Azure Private Link is consistent voor Azure PaaS-services, services die eigendom zijn van klanten en gedeelde partnerservices.

> [!IMPORTANT]
> Azure Private Link is nu algemeen beschikbaar. Zowel privé-eindpunten als de Private Link-service (service achter standaard load balancer) zijn algemeen beschikbaar. Met verschillende Azure PaaS wordt de Azure Private Link volgens verschillende planningen uitgevoerd. Zie de [Beschik baarheid van persoonlijke koppelingen](availability.md) voor een nauw keurige status van Azure PaaS op een privé-koppeling. Zie [Privé-eindpunt](private-endpoint-overview.md#limitations) en [Private Link-service](private-link-service-overview.md#limitations) voor bekende beperkingen. 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure Private Link-centrum in Azure Portal" border="false":::

## <a name="key-benefits"></a>Belangrijkste voordelen
Azure Private Link biedt de volgende voordelen:  
- **Privétoegang tot services op het Azure-platform**: Verbind uw virtuele netwerk met services in Azure zonder een openbaar IP-adres bij de bron of bestemming. Serviceproviders kunnen hun services weergeven in hun eigen virtuele netwerk en consumenten hebben toegang tot deze services in hun lokale virtuele netwerk. Het Private Link-platform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. 
 
- **On-premises en peered netwerken**: Toegang tot services die worden uitgevoerd in Azure vanuit on-premises via ExpressRoute-privépeering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eindpunten. U hoeft geen ExpressRoute Microsoft-peering in te stellen of door het internet te gaan om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevenslekken**: Een privé-eindpunt wordt toegewezen aan een instantie van een PaaS-resource in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen risico's van gegevenslekken. 
 
- **Globaal bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met services achter een Private Link in regio B.  
 
- **Breid uit naar uw eigen services**: Schakel dezelfde ervaring en functionaliteit in om uw service privé te maken voor consumenten in Azure. Als u uw service achter een standaard Azure Load Balancer plaatst, kunt u deze inschakelen voor Private Link. De gebruiker kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een privé-eindpunt in zijn eigen virtuele netwerk. U kunt de verbindingsaanvragen beheren met een goedkeuringsaanroepstroom. Azure Private Link werkt voor consumenten en services die deel uitmaken van verschillende Azure Active Directory-tenants. 

## <a name="availability"></a>Beschikbaarheid 

Zie [Beschik baarheid van persoonlijke Azure-koppelingen](availability.md)voor informatie over Azure-Services die persoonlijke koppelingen ondersteunen.

Voor recente updates kijkt u op de pagina [Azure Private Link Updates](https://azure.microsoft.com/updates/?product=private-link) (Updates voor Azure Private Link).

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Azure Private Link kan worden geïntegreerd met Azure Monitor. Met deze combinatie kunt u:

 - Logboeken in een opslagaccount archiveren.
 - Gebeurtenissen naar uw Event Hub streamen.
 - Logboekregistratie van Azure Monitor vastleggen.

U krijgt toegang tot de volgende informatie op Azure Monitor: 
- **Privé-eindpunt**: 
    - Gegevens verwerkt door het privé-eindpunt (IN/UIT)
 
- **Private Link-service**:
    - Gegevens verwerkt door de Private Link-service (IN/UIT)
    - Beschikbaarheid NAT-poort  
 
## <a name="pricing"></a>Prijzen   
Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over prijzen.
 
## <a name="faqs"></a>Veelgestelde vragen  
Zie [Veelgestelde vragen over Azure Private Link](private-link-faq.md) voor veelgestelde vragen.
 
## <a name="limits"></a>Limieten  
Zie [limieten van Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits) voor limieten.

## <a name="service-level-agreement"></a>Service Level Agreement
Zie [SLA voor Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/) voor SLA.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Een privé-eindpunt maken met behulp van de Azure-portal](create-private-endpoint-portal.md)
- [Snelstart: Een Private Link-service maken met behulp van de Azure-portal](create-private-link-service-portal.md)
