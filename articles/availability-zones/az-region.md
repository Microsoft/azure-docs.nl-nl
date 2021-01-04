---
title: Azure-Services die ondersteuning bieden voor Beschikbaarheidszones
description: Als u Maxi maal beschik bare en flexibele toepassingen in azure wilt maken, Beschikbaarheidszones u fysiek afzonderlijke locaties bieden die u kunt gebruiken om uw resources uit te voeren.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723032"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-Services die ondersteuning bieden voor Beschikbaarheidszones

Beschikbaarheidszones is een Maxi maal beschik bare aanbieding die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Zie [regio's en Beschikbaarheidszones in azure](az-overview.md)voor een lijst met bestaande en aanstaande regio's die Beschikbaarheidszones ondersteunen.  

In deze sectie vindt u de Azure-Services die Beschikbaarheidszones ondersteunen. 

Services die beschikbaar zijn in elke regio, samen met het geplande schema voor Beschik baarheid, kunnen worden gevonden in [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/).

Alle Azure Management-Services zijn ontworpen om te worden flexibeler dan storingen op regio niveau. In het spectrum van fouten, hebben een of meer fouten in de beschikbaarheids zone binnen een regio een kleinere fout RADIUS vergeleken met een volledige regio fout. Azure kan een storing op zone niveau van beheer services binnen een regio herstellen. Azure voert een kritieke onderhouds bewerking uit binnen een regio, om te voor komen dat er fouten optreden die van invloed zijn op klant resources die zijn geïmplementeerd op Beschikbaarheidszones binnen een regio.


![conceptuele weer gave van een Azure-regio met drie zones](./media/az-region/azure-region-availability-zones.png)


Azure-Services die Beschikbaarheidszones ondersteunen, kunnen worden onderverdeeld in drie categorieën: **zonegebonden**, **zone-redundante** en **niet-regionale** Services. Werk belastingen van klanten kunnen worden gecategoriseerd om gebruik te maken van een van deze architectuur scenario's om te voldoen aan de prestaties en duurzaamheid van toepassingen.

- **Zonegebonden Services** : een resource kan worden geïmplementeerd in een specifieke, zelfgeselecteerde beschikbaarheids zone voor meer strikte latentie of prestatie vereisten.  Tolerantie is zelf ontworpen door toepassingen en gegevens te repliceren naar een of meer zones binnen de regio.  Resources kunnen worden vastgemaakt aan een specifieke zone. Virtuele machines, Managed disks of standaard-IP-adressen kunnen bijvoorbeeld worden vastgemaakt aan een specifieke zone, waardoor de flexibiliteit kan worden verg root door een of meer exemplaren van resources over meerdere zones te verdelen.

- **Zone-redundante Services** : Azure-platform repliceert de resource en gegevens over zones.  Micro soft beheert de levering van hoge Beschik baarheid, omdat Azure automatisch exemplaren van de regio repliceert en distribueert.  ZRS bijvoorbeeld repliceert de gegevens over drie zones, zodat een zone storing geen invloed heeft op de HA van de gegevens. 

- **Niet-regionale Services** : services die geen afhankelijkheid hebben van een specifieke Azure-regio, waardoor ze flexibel zijn voor de zone-brede storingen en regionale uitval.


Als u een uitgebreide bedrijfs continuïteit wilt bereiken op Azure, bouwt u uw toepassings architectuur met de combi natie van Beschikbaarheidszones met Azure-regio paren. U kunt uw toepassingen en gegevens synchroon repliceren met Beschikbaarheidszones binnen een Azure-regio voor hoge Beschik baarheid en asynchroon repliceren in azure-regio's voor nood herstel beveiliging. Lees voor meer informatie over het [bouwen van oplossingen voor hoge Beschik baarheid met behulp van Beschikbaarheidszones](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Azure-Services die Beschikbaarheidszones ondersteunen

- Virtuele machines van de vorige generatie worden hieronder niet weer gegeven. Zie voor meer informatie [vorige generaties virtuele machine grootten](../virtual-machines/sizes-previous-gen.md).

- Sommige services zijn niet regionaal, Zie [regio's en Beschikbaarheidszones in azure](az-overview.md) voor meer informatie. Deze services hebben geen afhankelijkheid van een specifieke Azure-regio, waardoor ze robuust zijn voor de zone-ruime onderbrekingen en de regionale storingen.  De lijst met niet-regionale Services vindt u op [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Noord- en Zuid-Amerika

| **Producten** | **US - centraal** | **VS - oost** | **US - oost 2** | **West US 2** | **Canada - midden** |
|--|--|--|--|--|--|
| **Compute** |  |  |  |  |  |
| [App Service omgevingen (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Containers** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium files-opslag](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Netwerken** |  |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standaard-IP-adres](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databases** |  |  |  |  |  |
| [Azure Cache voor Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL-flexibele server](../mysql/flexible-server/concepts-high-availability.md) | BxDxH | BxDxH | :heavy_check_mark: | :heavy_check_mark: | BxDxH |
| [Azure Database for PostgreSQL - Flexible Server](../postgresql/flexible-server/overview.md) | BxDxH | BxDxH | :heavy_check_mark: | :heavy_check_mark: | BxDxH |
| [Azure SQL Database (Algemeen laag)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | BxDxH | : heavy_check_mark: (preview-versie) | : heavy_check_mark: (preview-versie) | : heavy_check_mark: (preview-versie) | BxDxH |
| [Azure SQL Database (Premium-& Bedrijfskritiek-lagen)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integratie** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Beheer en governance** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | BxDxH | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | BxDxH |
| **Beveiliging** |  |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Producten** | **Frankrijk - centraal** | **Europa - noord** | **Verenigd Koninkrijk Zuid** | **Europa -west** |
|--|--|--|--|--|
| **Compute** |  |  |  |  |
| [App Service omgevingen (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium files-opslag](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Netwerken** |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standaard-IP-adres](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databases** |  |  |  |  |
| [Azure Cache voor Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL-flexibele server](../mysql/flexible-server/concepts-high-availability.md) | BxDxH | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL - Flexible Server](../postgresql/flexible-server/overview.md) | BxDxH | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Algemeen laag)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | BxDxH | : heavy_check_mark: (preview-versie) | BxDxH | : heavy_check_mark: (preview-versie) |
| [Azure SQL Database (Premium-& Bedrijfskritiek-lagen)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integratie** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Beheer en governance** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | BxDxH | :heavy_check_mark: |
| **Beveiliging** |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Azië en Stille Oceaan



| **Producten** | **Japan East** | **Azië - zuidoost** | **Australië - oost** |
|--|--|--|--|
| **Compute** |  |  |  |
| [App Service omgevingen (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Premium files-opslag](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Netwerken** |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express-route](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standaard-IP-adres](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databases** |  |  |  |
| [Azure Cache voor Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL-flexibele server](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL - Flexible Server](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Algemeen laag)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | BxDxH | : heavy_check_mark: (preview-versie) | : heavy_check_mark: (preview-versie) |
| [Azure SQL Database (Premium-& Bedrijfskritiek-lagen)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analyse** |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integratie** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Beheer en governance** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | BxDxH | BxDxH |
| **Beveiliging** |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Aanstaande Beschikbaarheidszones 

Azure biedt Beschikbaarheidszones ondersteuning in de volgende regio's:
- VS (overheid) - Virginia
- Zuid-Afrika - noord
- South Central US
- Duitsland - west-centraal

De lijst met bestaande en aanstaande regio's die ondersteuning bieden voor Beschikbaarheidszones, vindt u [hier](https://azure.microsoft.com/global-infrastructure/geographies/).    

Neem contact op met uw micro soft-verkoop-of klant vertegenwoordiger voor meer informatie over de ondersteuning van Beschikbaarheidszones in deze regio's.


## <a name="pricing-for-vms-in-availability-zones"></a>Prijzen voor Vm's in Beschikbaarheidszones

Er zijn geen extra kosten verbonden aan het implementeren van virtuele machines in een beschikbaarheids zone. Bekijk de [pagina met bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.


## <a name="get-started-with-availability-zones"></a>Aan de slag met Beschikbaarheidszones

- [Een virtuele machine maken](../virtual-machines/windows/create-portal-availability-zone.md)
- [Een beheerde schijf toevoegen met Power shell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Een door een zone redundante schaalset voor virtuele machines maken](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Taak verdeling van virtuele machines in zones met behulp van een Standard Load Balancer met een zone-redundante front-end](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Taak verdeling van Vm's binnen een zone met behulp van een Standard Load Balancer met een zonegebonden-front-end](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Zone-redundante opslag](../storage/common/storage-redundancy.md)
- [SQL Database laag voor algemeen gebruik](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geo-noodherstel Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geo-noodherstel Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Een zone-redundante virtuele netwerkgateway maken](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zone redundante regio voor Azure Cosmos DB toevoegen](../cosmos-db/high-availability.md#availability-zone-support)
- [Aan de slag met Azure cache voor redis Beschikbaarheidszones](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Een Azure Active Directory Domain Services-instantie maken](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Een AKS-cluster (Azure Kubernetes service) maken dat gebruikmaakt van Beschikbaarheidszones](../aks/availability-zones.md)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Regio's en beschikbaarheidszones in Azure](az-overview.md)
