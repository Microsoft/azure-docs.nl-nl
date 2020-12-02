---
title: Migratie van klassieke en Resource Manager VPN Gateway | Microsoft Docs
description: Op deze pagina vindt u een overzicht van de VPN Gateway klassieke naar Resource Manager-migratie.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488201"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Migratie van klassieke en Resource Manager VPN Gateway
VPN-gateways kunnen nu worden gemigreerd van het klassieke naar het Resource Manager-implementatie model. U kunt meer lezen over Azure Resource Manager [functies en voor delen](../azure-resource-manager/management/overview.md). In dit artikel wordt beschreven hoe u migreert van klassieke implementaties naar het nieuwere model op basis van Resource Manager. 

VPN-gateways worden gemigreerd als onderdeel van de VNet-migratie van klassiek naar Resource Manager. Deze migratie vindt één VNet per keer plaats. Er is geen extra vereiste voor de migratie van hulpprogram ma's of vereisten. Migratie stappen zijn identiek aan bestaande VNet-migratie en worden beschreven op de [pagina IaaS resources-migratie](../virtual-machines/migration-classic-resource-manager-ps.md). Er is geen uitval tijd tijdens de migratie en daarom blijven bestaande workloads functioneren zonder verlies van on-premises connectiviteit tijdens de migratie. Het open bare IP-adres dat is gekoppeld aan de VPN-gateway, verandert tijdens het migratie proces niet. Dit betekent dat u de on-premises router niet opnieuw hoeft te configureren nadat de migratie is voltooid.  

Het model in Resource Manager wijkt af van het klassieke model en bestaat uit virtuele netwerk gateways, lokale netwerk gateways en verbindings bronnen. Deze vertegenwoordigen de VPN-gateway zelf, de lokale site die de on-premises adres ruimte en connectiviteit tussen de twee heeft. Zodra de migratie is voltooid, zijn de gateways niet meer beschikbaar in het klassieke model en kunnen alle beheer bewerkingen op virtuele netwerk gateways, lokale netwerk gateways en verbindings objecten worden uitgevoerd met het Resource Manager-model.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
De meest voorkomende scenario's voor VPN-connectiviteit worden besproken door de migratie van het klassieke model naar Resource Manager. De ondersteunde scenario's zijn:

* Site connectiviteit aanwijzen
* Site-naar-site-verbinding met VPN Gateway verbonden met on-premises locatie
* VNet-naar-VNet-connectiviteit tussen twee VNets met VPN-gateways
* Meerdere VNets zijn verbonden met hetzelfde on-premises locatie
* Connectiviteit op meerdere locaties
* Geforceerde tunneling is ingeschakeld VNets

Scenario's die niet worden ondersteund, zijn onder andere-  

* VNet met zowel de ExpressRoute-gateway als de VPN Gateway wordt momenteel niet ondersteund.
* Transit scenario's waarbij VM-extensies zijn verbonden met on-premises servers. De beperkingen voor transit VPN-verbindingen worden hieronder beschreven.

> [!NOTE]
> CIDR-validatie in het Resource Manager-model is strikter dan het in het klassieke model. Voordat u migreert, moet u ervoor zorgen dat de klassieke adresbereiken voldoen aan de geldige CIDR-indeling voordat u begint met de migratie. CIDR kan worden gevalideerd met behulp van algemene CIDR-validatie functies. VNet-of lokale sites met ongeldige CIDR-bereiken die worden gemigreerd, resulteren in een fout status.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migratie van VNet-naar-VNet-connectiviteit
VNet-naar-VNet-connectiviteit in het klassiek is bereikt door een lokale site weergave van het verbonden VNet te maken. Klanten moesten twee lokale sites maken die de twee VNets vertegenwoordigen die samen moeten worden verbonden. Deze zijn vervolgens verbonden met de bijbehorende VNets met behulp van IPsec-tunnel om verbinding te maken tussen de twee VNets. Dit model heeft beheer baarheids problemen omdat wijzigingen in het adres bereik in één VNet ook moeten worden behouden in de bijbehorende lokale site weergave. In het Resource Manager-model is deze tijdelijke oplossing niet meer nodig. De verbinding tussen de twee VNets kan rechtstreeks worden bereikt met het verbindings type ' Vnet2Vnet ' in de verbindings bron. 

![Scherm opname van VNet-naar-VNet-migratie.](./media/vpn-gateway-migration/migration1.png)

Tijdens de VNet-migratie zien we dat de verbonden entiteit met de huidige VNet VPN-gateway een ander VNet is en ervoor zorgt dat de migratie van beide VNets is voltooid, maar u hebt niet langer twee lokale sites die het andere VNet vertegenwoordigen. Het klassieke model van twee VPN-gateways, twee lokale sites en twee verbindingen ertussen worden omgezet naar het Resource Manager-model met twee VPN-gateways en twee verbindingen van het type Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>VPN-verbinding tussen Transit
U kunt VPN-gateways configureren in een topologie, zodat de on-premises connectiviteit voor een VNet wordt bereikt door verbinding te maken met een ander VNet dat rechtstreeks is verbonden met on-premises. Dit is een transit VPN-verbinding waarbij exemplaren in het eerste VNet via door voer naar de VPN-gateway worden verbonden met on-premises bronnen, die rechtstreeks zijn verbonden met on-premises. Voor het uitvoeren van deze configuratie in het klassieke implementatie model moet u een lokale site maken met geaggregeerde voor voegsels die zowel de verbonden VNet-als on-premises adres ruimte vertegenwoordigen. Deze representatieve lokale site is vervolgens verbonden met het VNet om een doorvoer verbinding te bewerkstelligen. Dit klassieke model heeft ook vergelijk bare uitdagingen voor beheer, omdat elke wijziging in het on-premises adres bereik ook moet worden onderhouden op de lokale site die de aggregatie van VNet en on-premises vertegenwoordigt. De introductie van BGP-ondersteuning in Resource Manager ondersteunt gateways vereenvoudigt de beheer baarheid omdat de verbonden gateways routes van on-premises kunnen ontdekken zonder hand matige aanpassing van voor voegsels.

![Scherm opname van het scenario voor transit routering.](./media/vpn-gateway-migration/migration2.png)

Omdat we VNet transformeren naar VNet-connectiviteit zonder dat hiervoor lokale sites zijn vereist, verliest het Transit scenario on-premises connectiviteit voor VNet dat indirect is verbonden met on-premises. Het verlies van de connectiviteit kan op de volgende twee manieren worden verholpen, nadat de migratie is voltooid- 

* Schakel BGP in op VPN-gateways die met elkaar zijn verbonden en on-premises. Als BGP wordt ingeschakeld, wordt de connectiviteit hersteld zonder enige andere configuratie wijziging omdat routes worden geleerd en geadverteerd tussen VNet-gateways. Houd er rekening mee dat BGP Option alleen beschikbaar is voor Standard-en hogere Sku's.
* Stel een expliciete verbinding in van het betreffende VNet naar de lokale netwerk gateway die de on-premises locatie vertegenwoordigt. Hiervoor moet ook de configuratie van de on-premises router worden gewijzigd om de IPsec-tunnel te maken en te configureren.

## <a name="next-steps"></a>Volgende stappen
Nadat u hebt geprofiteerd van de ondersteuning van VPN-gateway migratie, gaat u naar door het [platform ondersteunde migratie van IaaS-resources van klassiek naar Resource Manager](../virtual-machines/migration-classic-resource-manager-ps.md) om aan de slag te gaan.