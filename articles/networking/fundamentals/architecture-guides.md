---
title: Documentatie voor Azure-netwerk architectuur
description: Meer informatie over de documentatie over referentie architectuur die beschikbaar is voor Azure-netwerk services.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080239"
---
# <a name="azure-networking-architecture-documentation"></a>Documentatie voor Azure-netwerk architectuur

Dit artikel bevat informatie over architectuur handleidingen die u kunnen helpen bij het verkennen van de verschillende netwerk services in azure die voor u beschikbaar zijn voor het bouwen van uw toepassingen.

## <a name="networking-overview"></a>Overzicht van netwerken

De volgende tabel bevat artikelen die een netwerk overzicht bieden van een virtueel Data Center en een hub-en-spoke-topologie in Azure.

|Titel |Beschrijving  |
|---------|---------|
|[Virtuele datacenters](/azure/architecture/vdc/networking-virtual-datacenter)   | Biedt een netwerk perspectief van een virtueel Data Center in Azure.       |
|[Hub-spoke topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Biedt een overzicht van de hub-en spoke-netwerk topologie in azure, samen met informatie over de limieten voor abonnementen en meerdere hubs.          |

## <a name="connect-to-azure-resources"></a>Verbinding maken met Azure-resources

De volgende tabel bevat artikelen over Azure-netwerk services die verbinding bieden tussen Azure-resources, connectiviteit van een on-premises netwerk naar Azure-resources en vertakking naar vertakkings connectiviteit in Azure.

|Titel |Beschrijving  |
|---------|---------|
|[IP-adres ruimten toevoegen aan peered virtuele netwerken](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Bevat scripts die u helpen IP-adres ruimten toe te voegen aan peered virtuele netwerken.        |
|[Zelfstandige servers verbinden met de Azure-netwerkadapter](/azure/architecture/hybrid/azure-network-adapter)   | Laat zien hoe u een on-premises zelfstandige server verbindt met Microsoft Azure virtuele netwerken met behulp van de Azure-netwerk adapter die u via Windows-beheer centrum implementeert.        |
|[Kiezen tussen virtuele netwerkpeering en VPN-gateways](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Vergelijkt twee manieren om virtuele netwerken in azure te verbinden: virtuele netwerk peering en VPN-gateways.        |
|[Een on-premises netwerk verbinden met Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Vergelijkt de opties voor het verbinden van een on-premises netwerk met een Azure Virtual Network (VNet). Voor elke optie is ook een meer gedetailleerde referentiearchitectuur beschikbaar.        |
|[Architectuur van de SD-WAN-verbinding met Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Beschrijft de verschillende connectiviteits opties voor het interconnecteren van een door private software gedefinieerde WAN (SD-WAN) met Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Maxi maal beschik bare toepassingen implementeren

De volgende tabel bevat artikelen die beschrijven hoe u uw toepassingen kunt implementeren voor maximale Beschik baarheid met behulp van een combi natie van Azure-netwerk services.

|Titel |Beschrijving  |
|---------|---------|
|[N-tier-toepassing met meerdere regio's](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Beschrijft een N-tier-toepassing met meerdere regio's die gebruikmaakt van Traffic Manager voor het routeren van binnenkomende aanvragen naar een primaire regio. als deze regio niet beschikbaar is, wordt Traffic Manager een failover naar de secundaire regio uitgevoerd.      |
| [SaaS voor meerdere tenants in Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Maakt gebruik van een multi tenant-oplossing die een combi natie van front-deur en Application Gateway bevat.  Met de voor deur kunt u verkeer verdelen over regio's en Application Gateway routes en verkeer in de toepassing intern verdelen over de verschillende services die voldoen aan de behoeften van de client.  |
| [Webtoepassing met meerdere lagen die is gebouwd voor hoge Beschik baarheid en herstel na nood gevallen ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Implementeert robuuste toepassingen met meerdere lagen die zijn gebouwd voor hoge Beschik baarheid en herstel na nood gevallen. Als de primaire regio niet beschikbaar is, wordt Traffic Manager een failover naar de secundaire regio uitgevoerd.  |
|[IaaS: webtoepassing met relationele data base](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Hierin wordt beschreven hoe u bronnen verspreid over meerdere zones kunt gebruiken om een architectuur met hoge Beschik baarheid te bieden voor het hosten van een IaaS-webtoepassing (Infrastructure as a Service) en SQL Server Data Base.     |
|[Locatie delen in realtime met goedkope serverloze Azure-services](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Maakt gebruik van de voor deur van Azure voor een hogere Beschik baarheid van uw toepassingen dan in één regio worden geïmplementeerd. Als een regionale storing van invloed is op de primaire regio, kunt u met Front Door een failover-schakeling naar de secundaire regio uitvoeren.      |
|[Virtuele netwerkapparaten met hoge beschikbaarheid](/azure/architecture/reference-architectures/dmz/nva-ha)     | Laat zien hoe u een set virtuele netwerk apparaten (Nva's) implementeert voor hoge Beschik baarheid in Azure.        |

## <a name="secure-your-network-resources"></a>Uw netwerk bronnen beveiligen

De volgende tabel bevat artikelen waarin wordt beschreven hoe u netwerk bronnen kunt beveiligen met Azure-netwerk services.

|Titel |Beschrijving  |
|---------|---------|
|[Best practices voor netwerkbeveiliging](../../security/fundamentals/network-best-practices.md) |In dit artikel wordt een verzameling van aanbevolen procedures voor Azure beschreven om uw netwerk beveiliging te verbeteren.         |
[Gids voor de firewallarchitectuur van Azure](/azure/architecture/example-scenario/firewalls/) | Biedt een gestructureerde benadering voor het ontwerpen van Maxi maal beschik bare firewalls in azure met behulp van virtuele apparaten van derden.        |
|[Veilig hybride netwerk implementeren](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Beschrijft een architectuur die een DMZ implementeert, ook wel een perimeter netwerk genoemd, tussen het on-premises netwerk en een virtueel Azure-netwerk. Alle inkomend en uitgaand verkeer passeren via Azure Firewall.        |
|[Werkbelastingen beveiligen en beheren met segmentatie op netwerkniveau](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Hierin worden de drie algemene patronen beschreven die worden gebruikt voor het organiseren van werk belastingen in azure vanuit een netwerk perspectief.   Elk van deze patronen biedt een ander type isolatie en connectiviteit.      |
|[Firewall en Application Gateway voor virtuele netwerken](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Hierin worden de Azure Virtual Network Security Services, zoals Azure Firewall en Azure-toepassing gateway, beschreven, wanneer u elke service gebruikt, en netwerk ontwerp opties die beide combi neren.      |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).
