---
title: Azure-services die beschikbaarheidszones ondersteunen
description: Als u uiterst beschikbare en flexibele toepassingen in Azure wilt maken, Beschikbaarheidszones fysiek gescheiden locaties bieden die u kunt gebruiken om uw resources uit te voeren.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 4c592c2d67df1e792200cc36449a6268807bbb56
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816256"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-services die beschikbaarheidszones ondersteunen

Microsoft Azure globale infrastructuur is ontworpen en samengesteld op elke laag om de klanten de hoogste redundantie- en tolerantieniveaus te bieden. De Azure-infrastructuur bestaat uit geografische gebieden, regio's en Beschikbaarheidszones, die de radius van een storing beperken en daarom potentiële gevolgen voor klanttoepassingen en -gegevens beperken. De Azure-beschikbaarheidszones is ontwikkeld om een software- en netwerkoplossing te bieden ter bescherming tegen storingen in datacenters en om onze klanten een hogere beschikbaarheid (HA) te bieden.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters met onafhankelijke voeding, koeling en netwerken. De fysieke scheiding van Beschikbaarheidszones binnen een regio beperkt de gevolgen voor toepassingen en gegevens van zonestoringen, zoals grootschalige overstromingen, grote stormen en superstormen, en andere gebeurtenissen die de toegang tot de site kunnen verstoren, veilige ruimte, uitgebreide uptime van hulpprogramma's en de beschikbaarheid van resources. Beschikbaarheidszones en de bijbehorende datacenters zijn zodanig ontworpen dat als de ene zone wordt aangetast, de services, capaciteit en beschikbaarheid worden ondersteund door de andere Beschikbaarheidszones in de regio.

Alle Azure-beheerservices zijn ontworpen om bestand te zijn tegen storingen op regioniveau. In het spectrum van storingen hebben een of meer beschikbaarheidszone-storingen binnen een regio een kleinere fout radius in vergelijking met een storing in de hele regio. Azure kan herstellen na een fout op zoneniveau van beheerservices binnen een regio. Azure voert kritiek onderhoud één zone tegelijk binnen een regio uit om storingen te voorkomen die van invloed zijn op de resources van klanten die zijn geïmplementeerd Beschikbaarheidszones binnen een regio.


![conceptuele weergave van een Azure-regio met 3 zones](./media/az-region/azure-region-availability-zones.png)


Azure-services Beschikbaarheidszones kunnen worden onderverdeeld in drie categorieën: **zone-redundante** en **niet-regionale** services.  Workloads van klanten kunnen worden gecategoriseerd om elk van deze architectuurscenario's te gebruiken om te voldoen aan de prestaties en duurzaamheid van toepassingen.

- **Zonespecifieke services:** een resource kan worden geïmplementeerd in een specifieke, zelf geselecteerde beschikbaarheidszone om strengere latentie- of prestatievereisten te bereiken.  Tolerantie wordt zelf ontworpen door toepassingen en gegevens te repliceren naar een of meer zones in de regio.  Resources kunnen worden vastgemaakt aan een specifieke zone. Virtuele machines, beheerde schijven of standaard-IP-adressen kunnen bijvoorbeeld worden vastgemaakt aan een specifieke zone, waardoor de tolerantie wordt verhoogd door een of meer exemplaren van resources te verdelen over zones.

- **Zone-redundante services:** resources worden automatisch gerepliceerd of gedistribueerd over zones. ZRS repliceert bijvoorbeeld de gegevens in drie zones, zodat een zonefout geen invloed heeft op de ha van de gegevens.  

- **Niet-regionale services: services** zijn altijd beschikbaar vanuit Azure-geografieën en zijn bestand tegen zonebrede uitval en regiobrede uitval. 


Voor uitgebreide bedrijfscontinuïteit in Azure bouwt u uw toepassingsarchitectuur met behulp van de combinatie van Beschikbaarheidszones met Azure-regioparen. U kunt uw toepassingen en gegevens synchroon repliceren met behulp van Beschikbaarheidszones binnen een Azure-regio voor hoge beschikbaarheid en asynchroon repliceren tussen Azure-regio's voor bescherming tegen noodherstel. Lees Oplossingen bouwen voor [hoge beschikbaarheid met behulp van](/azure/architecture/high-availability/building-solutions-for-high-availability)Beschikbaarheidszones . 

## <a name="azure-services-supporting-availability-zones"></a>Azure-services die ondersteuning Beschikbaarheidszones

 - De virtuele machines van de oudere generatie worden niet weergegeven. Zie Vorige generaties van [virtuele-machinegrootten voor meer informatie.](../virtual-machines/sizes-previous-gen.md)
 - Zoals vermeld in de [regio's en Beschikbaarheidszones in Azure](az-overview.md), zijn sommige services niet-regionaal. Deze services zijn niet afhankelijk van een specifieke Azure-regio, omdat ze bestand zijn tegen zonebrede uitval en regiobrede uitval.  De lijst met niet-regionale services vindt u op [Producten beschikbaar per regio.](https://azure.microsoft.com/global-infrastructure/services/)


## <a name="azure-regions-with-availability-zones"></a>Azure-regio's met Beschikbaarheidszones
 

| Noord- en Zuid-Amerika           | Europa               | Afrika              | Azië en Stille Oceaan   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brazilië - zuid       | Frankrijk - centraal       | Zuid-Afrika - noord* | Australië - oost |
| Canada - midden     | Duitsland - west-centraal |                     | India - centraal* |
| Central US         | Europa - noord         |                     | Japan - oost     |
| VS - oost            | Verenigd Koninkrijk Zuid             |                     | Korea - centraal* |
| VS - oost 2          | Europa -west          |                     | Azië - zuidoost |
| VS - zuid-centraal |                      |                     |                |
| VS (overheid) - Virginia    |                      |                     |                |
| VS - west 2        |                      |                     |                |
| VS - west 3*       |                      |                     |                |

\* Voor meer informatie over Beschikbaarheidszones en beschikbare services in deze regio's, neem dan contact op met uw Microsoft-verkoop- of klantmedewerker. Zie Azure-geografische gebieden voor Beschikbaarheidszones volgende [regio's die ondersteuning bieden voor de regio's.](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)


## <a name="azure-services-supporting-availability-zones"></a>Azure-services die ondersteuning Beschikbaarheidszones

- Oudere virtuele machines van de generatie worden hieronder niet vermeld. Zie vorige generaties van [virtuele-machinegrootten voor meer informatie.](../virtual-machines/sizes-previous-gen.md)

- Sommige services zijn niet-regionaal. Zie [Regio's en Beschikbaarheidszones in Azure voor](az-overview.md) meer informatie. Deze services zijn niet afhankelijk van een specifieke Azure-regio, waardoor ze bestand zijn tegen zonebrede uitval en regiobrede uitval.  De lijst met niet-regionale services vindt u op [Producten beschikbaar per regio.](https://azure.microsoft.com/global-infrastructure/services/)


### <a name="zone-resilient-services"></a>Zone Resilient Services 

:globe_with_meridians: niet-regionale services : services zijn altijd beschikbaar vanuit Azure-geografieën en zijn bestand tegen zonebrede uitval en regiobrede uitval.

:large_blue_diamond: bestand tegen zonebrede uitval 

**Basisservices**

|     Producten                                                    | Flexibiliteit             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (V2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [Openbaar IP-adres van Azure](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     Azure SQL Database ([Algemeen laag](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))                 | :large_blue_diamond:  |
|     Azure SQL Database([Premium & Bedrijfskritiek Tier](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | :large_blue_diamond:  |
|     [Disk Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [Opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     Opslag:   [hot/cool Blob Storage lagen](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     Opslag:   [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Virtual Machines-schaalsets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Virtual Machines: [Av2-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Bs-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [DSv2-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [DSv3-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Virtual Machines: [Dv2-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Dv3-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [ESv3-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [Ev3-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [F-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [FS-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**Algemene services**


|     Producten                                                    | Flexibiliteit             |
|-----------------------------------------------------------------|:----------------------------:|
|     [App Service-omgevingen](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [Azure API Management](https://docs.microsoft.com/azure/api-management/zone-redundancy)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Azure Cache voor Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Azure Database for MySQL – [Flexible Server](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL – [Flexible Server](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [Virtuele machine](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Azure Web Application Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Network Watcher: [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Premium Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     Opslag: [Azure Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     Virtual Machines: [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Virtual Machines: [Ddsv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Ddv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dsv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Dv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Edsv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Edv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Esv4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Ev4-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Fsv2-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [M-serie](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [Virtuele WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [Punt-naar-site-VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     Virtual WAN: [Site-naar-site-VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | :large_blue_diamond:  |


**Gespecialiseerde services**

|     Producten                                                    | Flexibiliteit             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services: Anomaly Detector                        | :large_blue_diamond:  |
|     Cognitive Services: Form Recognizer                         | :large_blue_diamond:  |
|     Opslag: Ultra Disk                                         | :large_blue_diamond:  |


**Niet-regionaal**

|     Producten                                                    | Flexibiliteit             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Azure Bot Service                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender for IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Klanten-lockbox voor Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft Azure-portal                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security Center                                           | :globe_with_meridians: |
|     Traffic Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Prijzen voor VM's in Beschikbaarheidszones

Azure-beschikbaarheidszones zijn beschikbaar met uw Azure-abonnement. Meer informatie hier - [Pagina met bandbreedteprijzen.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Aan de slag met Beschikbaarheidszones

- [Een virtuele machine maken](../virtual-machines/windows/create-portal-availability-zone.md)
- [Een beheerde schijf toevoegen met Behulp van PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Een zone-redundante virtuele-machineschaalset maken](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [VM's verdelen over zones met behulp van Standard Load Balancer met een zone-redundante front-end](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Load balancer VM's binnen een zone met behulp van Standard Load Balancer zone met een zone-front-end](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zone-redundante opslag](../storage/common/storage-redundancy.md)
- [SQL Database algemeen gebruik](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geo-noodherstel Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geo-noodherstel Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Een zone-redundante virtuele netwerkgateway maken](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zone-redundante regio voor Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Aan de slag Azure Cache voor Redis Beschikbaarheidszones](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Een Azure Active Directory Domain Services-instantie maken](../active-directory-domain-services/tutorial-create-instance.md)
- [Een AKS Azure Kubernetes Service cluster (AKS) maken dat gebruikmaakt van Beschikbaarheidszones](../aks/availability-zones.md)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Regio's en beschikbaarheidszones in Azure](az-overview.md)
