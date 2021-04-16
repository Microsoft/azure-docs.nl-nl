---
title: documentatie Azure-netwerken architectuur
description: Meer informatie over de referentiearchitectuurdocumentatie die beschikbaar is voor Azure-netwerkservices.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484792"
---
# <a name="azure-networking-architecture-documentation"></a>documentatie Azure-netwerken architectuur

Dit artikel bevat informatie over architectuurhandleidingen die u kunnen helpen bij het verkennen van de verschillende netwerkservices in Azure die voor u beschikbaar zijn voor het bouwen van uw toepassingen.

## <a name="networking-overview"></a>Overzicht van netwerken

De volgende tabel bevat artikelen met een netwerkoverzicht van een virtueel datacenter en een hub en spoke-topologie in Azure.

|Titel |Beschrijving  |
|---------|---------|
|[Virtuele datacenters](/azure/architecture/vdc/networking-virtual-datacenter)   | Biedt een netwerkperspectief van een virtueel datacenter in Azure.       |
|[Hub-spoke topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Biedt een overzicht van de hub en spoke-netwerktopologie in Azure, samen met informatie over abonnementslimieten en meerdere hubs.          |

## <a name="connect-to-azure-resources"></a>Verbinding maken met Azure-resources

De volgende tabel bevat artikelen over Azure-netwerken-services die connectiviteit bieden tussen Azure-resources, connectiviteit van een on-premises netwerk naar Azure-resources en vertakkings-naar-vertakking-connectiviteit in Azure.

|Titel |Beschrijving  |
|---------|---------|
|[IP-adresruimten toevoegen aan virtuele peernetwerken](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Biedt scripts voor het toevoegen van IP-adresruimten aan virtuele netwerken met peering.        |
|[Zelfstandige servers verbinden met de Azure-netwerkadapter](/azure/architecture/hybrid/azure-network-adapter)   | Laat zien hoe u een on-premises zelfstandige server verbindt met Microsoft Azure virtuele netwerken met behulp van de Azure-netwerkadapter die u implementeert via Windows Admin Center.        |
|[Kiezen tussen virtuele netwerkpeering en VPN-gateways](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Vergelijkt twee manieren om virtuele netwerken in Azure te verbinden: peering voor virtuele netwerken en VPN-gateways.        |
|[Een on-premises netwerk verbinden met Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Vergelijkt opties voor het verbinden van een on-premises netwerk met een Azure Virtual Network (VNet). Voor elke optie is ook een meer gedetailleerde referentiearchitectuur beschikbaar.        |
|[Architectuur voor SD-WAN-connectiviteit met Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Beschrijft de verschillende connectiviteitsopties voor het verbinden van een particulier SD-WAN (Software Defined WAN) met Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Toepassingen met hoge beschikbaarheid implementeren

De volgende tabel bevat artikelen waarin wordt beschreven hoe u uw toepassingen implementeert voor hoge beschikbaarheid met behulp van een combinatie Azure-netwerken services.

|Titel |Beschrijving  |
|---------|---------|
|[N-tier-toepassing voor meerdere regio's](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Beschrijft een N-tier-toepassing met meerdere regio's die gebruikmaakt van Traffic Manager om binnenkomende aanvragen naar een primaire regio te routeeren. Als die regio niet meer beschikbaar is, wordt Traffic Manager overgezet naar de secundaire regio.      |
| [SaaS voor meerdere tenants in Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Maakt gebruik van een oplossing met meerdere tenants die een combinatie van Front Door en Application Gateway.  Front Door zorgt voor een goede load balancer van verkeer tussen regio's en Application Gateway routes en zorgt voor een interne load balancer van verkeer in de toepassing naar de verschillende services die voldoen aan de bedrijfsbehoeften van de klant.  |
| [Webtoepassing met meerdere lagen die is gebouwd voor hoge beschikbaarheid en herstel na nood ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Implementeert robuuste toepassingen met meerdere lagen die zijn gebouwd voor hoge beschikbaarheid en herstel na noodherstel. Als de primaire regio niet meer beschikbaar is, wordt Traffic Manager overgezet naar de secundaire regio.  |
|[IaaS: webtoepassing met relationele database](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Beschrijft hoe u resources verspreid over meerdere zones gebruikt om een architectuur met hoge beschikbaarheid te bieden voor het hosten van een IaaS-webtoepassing (Infrastructure as a Service) en een SQL Server database.     |
|[Locatie delen in realtime met goedkope serverloze Azure-services](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Maakt Azure Front Door voor hogere beschikbaarheid voor uw toepassingen dan implementeren in één regio. Als een regionale storing van invloed is op de primaire regio, kunt u met Front Door een failover-schakeling naar de secundaire regio uitvoeren.      |
|[Virtuele netwerkapparaten met hoge beschikbaarheid](/azure/architecture/reference-architectures/dmz/nva-ha)     | Laat zien hoe u een set virtuele netwerkapparaten (NNET's) implementeert voor hoge beschikbaarheid in Azure.        |
|[Taakverdeling voor meerdere regio's met Traffic Manager en Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Beschrijft hoe u flexibele toepassingen met meerdere lagen implementeert in meerdere Azure-regio's om beschikbaarheid en een robuuste infrastructuur voor herstel na noodherstel te realiseren.        |

## <a name="secure-your-network-resources"></a>Uw netwerkbronnen beveiligen

De volgende tabel bevat artikelen waarin wordt beschreven hoe u uw netwerkbronnen kunt beveiligen met behulp Azure-netwerken services.

|Titel |Beschrijving  |
|---------|---------|
|[Best practices voor netwerkbeveiliging](../../security/fundamentals/network-best-practices.md) |Bespreekt een verzameling best practices van Azure om uw netwerkbeveiliging te verbeteren.         |
[Gids voor de firewallarchitectuur van Azure](/azure/architecture/example-scenario/firewalls/) | Biedt een gestructureerde benadering voor het ontwerpen van firewalls met hoge beschikbare firewalls in Azure met behulp van virtuele apparaten van derden.        |
|[Veilig hybride netwerk implementeren](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Beschrijft een architectuur die een DMZ implementeert, ook wel een perimeternetwerk genoemd, tussen het on-premises netwerk en een virtueel Azure-netwerk. Al het binnenkomende en uitgaande verkeer wordt via de Azure Firewall.        |
|[Werkbelastingen beveiligen en beheren met segmentatie op netwerkniveau](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Beschrijft de drie algemene patronen die worden gebruikt voor het organiseren van workloads in Azure vanuit netwerkperspectief.   Elk van deze patronen biedt een ander type isolatie en connectiviteit.      |
|[Firewall en Application Gateway voor virtuele netwerken](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Beschrijft Azure Virtual Network-beveiligingsservices zoals Azure Firewall en Azure Application Gateway, wanneer u elke service gebruikt, en netwerkontwerpopties die beide combineren.      |

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over Azure Virtual Network.](../../virtual-network/virtual-networks-overview.md)
