---
title: Overzicht van IPv6 voor Azure Virtual Network
titlesuffix: Azure Virtual Network
description: IPv6-beschrijving van IPv6-eind punten en gegevens paden in een virtueel Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bb7769298940095900da25e61e13612b92bad00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419096"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Wat is IPv6 voor Azure Virtual Network?

Met IPv6 voor Azure Virtual Network (VNet) kunt u toepassingen in azure hosten met IPv6-en IPv4-connectiviteit, zowel binnen een virtueel netwerk als vanaf het internet. Als gevolg van de uitputting van open bare IPv4-adressen, zijn nieuwe netwerken voor Mobility en Internet of Things (IoT) vaak gebaseerd op IPv6. Zelfs lange en mobiele netwerken worden naar IPv6 getransformeerd. Alleen IPv4-Services kunnen zichzelf tegen een echt nadeel vinden in zowel de bestaande als opkomende markten. Dankzij de dual-stack IPv4/IPv6-connectiviteit kunnen door Azure gehoste services dit technologie-hiaat door lopen met wereld wijd beschik bare, dubbele gestapelde services waarmee u eenvoudig verbinding kunt maken met zowel de bestaande IPv4-als deze nieuwe IPv6-apparaten en-netwerken.

Met de oorspronkelijke IPv6-connectiviteit van Azure kunt u eenvoudig een dual stack (IPv4/IPv6) Internet connectiviteit bieden voor toepassingen die worden gehost in Azure. Hiermee kan een eenvoudige implementatie van Vm's met IPv6-connectiviteit met gelijke taak verdeling voor zowel binnenkomende als uitgaande verbindingen worden gestart. Deze functie is nog steeds beschikbaar en u vindt [hier](../load-balancer/load-balancer-ipv6-overview.md)meer informatie.
IPv6 voor Azure Virtual Network is veel meer aanbevolen, waardoor volledige IPv6-oplossings architecturen in azure kunnen worden geïmplementeerd.


In het volgende diagram ziet u een eenvoudige implementatie van een dubbele stack (IPv4/IPv6) in Azure:

![Implementatie diagram van IPv6-netwerk](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Voordelen

Voor delen van IPv6 voor Azure VNET:

- Het bereik van uw door Azure gehoste toepassingen kan worden uitgebreid naar de groeiende mobiele en Internet of Things markten.
- Dubbele, gestapelde IPv4/IPv6-Vm's bieden maximale flexibiliteit voor service-implementatie. Eén service-exemplaar kan verbinding maken met IPv4-en IPv6-compatibele internetclients.
- Bouwt voort op lange, stabiele Azure VM-to-Internet IPv6-connectiviteit.
- Standaard beveiligd omdat de IPv6-verbinding met het internet alleen wordt ingesteld wanneer u deze expliciet aanvraagt in uw implementatie.

## <a name="capabilities"></a>Functies

IPv6 voor Azure VNet bevat de volgende mogelijkheden:

- Azure-klanten kunnen hun eigen IPv6-adres ruimte voor het virtuele netwerk definiëren om te voldoen aan de behoeften van hun toepassingen, klanten of naadloos integreren in hun on-premises IP-ruimte.
- Met dual stack (IPv4 en IPv6) virtuele netwerken met dual stack-subnetten kunnen toepassingen verbinding maken met zowel IPv4-als IPv6-resources in hun virtuele netwerk of Internet.
    > [!IMPORTANT]
    > De subnetten voor IPv6 moeten exact/64 groot zijn.  Dit zorgt voor toekomstige compatibiliteit. hiervoor moet u ervoor kiezen om de route ring van het subnet naar een on-premises netwerk mogelijk te maken, omdat sommige routers alleen IPv6-routes kunnen accepteren/64.  
- Beveilig uw resources met IPv6-regels voor netwerk beveiligings groepen.
    - En de DDoS-beveiliging (Distributed Denial of service) van het Azure-platform worden uitgebreid naar Internet gerichte open bare IP-adressen
- Pas de route ring van IPv6-verkeer in het virtuele netwerk aan met User-Defined routes, met name bij het gebruik van virtuele netwerk apparaten om uw toepassing uit te breiden.
- Linux en Windows Virtual Machines kunnen allemaal gebruikmaken van IPv6 voor Azure VNET
- [Open bare standaard IPv6-Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) ondersteuning voor het maken van robuuste, schaal bare toepassingen, waaronder:
    - Optionele IPv6-status test om te bepalen welke back-end-pool instanties status zijn en dus nieuwe stromen kunnen ontvangen.
    - Optionele regels voor uitgaande verbindingen die volledige declaratieve controle over uitgaande connectiviteit bieden om deze mogelijkheid te schalen en af te stemmen op uw specifieke behoeften.
    - Optionele meerdere front-end configuraties waarmee één load balancer meerdere open bare IPv6-IP-adressen kan gebruiken. hetzelfde frontend-protocol en dezelfde poort kunnen worden gebruikt voor alle frontend-adressen.
    - Optionele IPv6-poorten kunnen opnieuw worden gebruikt voor back-end-instanties met behulp van de *zwevende IP* -functie van regels voor taak verdeling 
    - Opmerking: taak verdeling voert geen protocol omzetting uit (geen NAT64). 
    - Opmerking: IPv6 kan alleen worden verdeeld over de primaire netwerk interface (NIC) op Azure-Vm's. 
- [Standaard-IPv6-ondersteuning voor interne Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) voor het maken van robuuste toepassingen met meerdere lagen in azure VNETs.   
- Standaard IPv6-ondersteuning voor open bare Load Balancer voor compatibiliteit met oudere implementaties
- [Gereserveerde IPv6-adressen en](ipv6-public-ip-address-prefix.md) adresbereiken bieden stabiele, voorspel bare IPv6-adressen die het filteren van uw door Azure gehoste toepassingen voor uw bedrijf en uw klanten vereenvoudigen.
- Openbaar IP op exemplaarniveau biedt IPv6-Internet connectiviteit rechtstreeks aan afzonderlijke Vm's.
- [IPv6 toevoegen aan bestaande alleen-IPv4-implementaties](ipv6-add-to-existing-vnet-powershell.md): met deze functie kunt u eenvoudig IPv6-connectiviteit toevoegen aan bestaande IPv4-implementaties zonder dat u opnieuw implementaties hoeft te maken.  Het IPv4-netwerk verkeer wordt niet beïnvloed tijdens dit proces, zodat u, afhankelijk van uw toepassing en besturings systeem, mogelijk IPv6 kunt toevoegen, zelfs bij Live Services.    
- Laat Internet-clients naadloos toegang krijgen tot uw dual stack-toepassing met behulp van het Protocol van de keuze met Azure DNS ondersteuning voor IPv6-records (AAAA). 
- Maak dual stack-toepassingen die automatisch worden geschaald naar uw belasting met schaal sets voor virtuele machines met IPv6.
- [Virtual Network (VNET) peering](virtual-network-peering-overview.md) -zowel binnen-regionaal als wereld wijde peering: Hiermee kunt u naadloos verbinding maken met dual stack VNETs. de IPv4-en IPv6-eind punten op vm's in de peered netwerken kunnen met elkaar communiceren. U kunt zelfs een dubbele stack met alleen IPv4-VNETs als u uw implementaties naar een dubbele stack overstapt. 
- Probleem oplossing en diagnostische gegevens over IPv6 zijn beschikbaar met load balancer metrische gegevens, waarschuwingen en Network Watcher functies, zoals pakket opname, NSG, verbindings problemen oplossen en verbindings bewaking.   

## <a name="scope"></a>Bereik
IPv6 voor Azure VNET is een basis functieset waarmee klanten dual stack-toepassingen (IPv4 + IPv6) in azure kunnen hosten.  We zijn van plan om IPv6-ondersteuning toe te voegen aan meer Azure-netwerk functies gedurende een bepaalde periode en uiteindelijk om twee stack versies van Azure PaaS services aan te bieden, maar ondertussen moeten alle Azure PaaS-services toegankelijk zijn via de IPv4-eind punten op dual stack Virtual Machines.   

## <a name="limitations"></a>Beperkingen
De huidige virtuele netwerk versie van IPv6 voor Azure heeft de volgende beperkingen:
- IPv6 voor Azure Virtual Network is beschikbaar in alle wereld wijde regio's van de Verenigde Staten van Azure en met behulp van alle implementatie methoden.  
- ExpressRoute-gateways kunnen worden gebruikt voor IPv4-verkeer in een VNET waarvoor IPv6 is ingeschakeld.  Ondersteuning voor IPv6-verkeer vindt u op ons schema.   
- VPN-gateways kunnen niet worden gebruikt in een VNET waarbij IPv6 is ingeschakeld, hetzij rechtstreeks of gelijkwaardig met ' UseRemoteGateway '.
- Het Azure-platform (AKS, enzovoort) biedt geen ondersteuning voor IPv6-communicatie voor containers. 
- Alleen IPv6-Virtual Machines of Virtual Machines schaal sets worden niet ondersteund. elke NIC moet ten minste één IPv4 IP-configuratie bevatten. 
- Bij het toevoegen van IPv6 aan bestaande IPv4-implementaties kunnen IPv6-bereiken niet worden toegevoegd aan een VNET met bestaande bronnen navigatie koppelingen.  
- Forward DNS voor IPv6 wordt vandaag ondersteund voor de open bare Azure-DNS, maar omgekeerde DNS wordt nog niet ondersteund.   

## <a name="pricing"></a>Prijzen

IPv6 Azure-resources en-band breedte worden in rekening gebracht tegen dezelfde tarieven als IPv4. Er zijn geen extra of andere kosten voor IPv6. U kunt details vinden over de prijzen voor [open bare IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses/), [netwerk bandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/)of [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren van een IPv6-toepassing met dubbele stack met Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Meer informatie over het [implementeren van een IPv6-toepassing met dubbele stacks met behulp van Azure cli](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Meer informatie over het [implementeren van een IPv6-toepassing met dubbele stacks met behulp van Resource Manager-sjablonen (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
