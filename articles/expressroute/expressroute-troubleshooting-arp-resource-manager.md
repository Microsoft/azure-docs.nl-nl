---
title: 'Azure ExpressRoute: ARP-tabellen-problemen oplossen'
description: Op deze pagina vindt u instructies voor het ophalen van ARP-tabellen (Address Resolution Protocol) voor een ExpressRoute-circuit
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561576"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>ARP-tabellen ophalen in het Resource Manager-implementatie model
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [Power shell-klassiek](expressroute-troubleshooting-arp-classic.md)
> 
> 

Dit artikel begeleidt u stapsgewijs door de stappen voor het leren van de ARP-tabellen voor uw ExpressRoute-circuit.

> [!IMPORTANT]
> Dit document is bedoeld om u te helpen bij het vaststellen en oplossen van eenvoudige problemen. Het is niet bedoeld als vervanging van micro soft-ondersteuning. U moet een ondersteunings ticket openen met [micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u het probleem niet kunt oplossen aan de hand van de onderstaande richt lijnen.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP-en ARP-tabellen (Address Resolution Protocol)
Address Resolution Protocol (ARP) is een Layer 2-protocol dat is gedefinieerd in [RFC 826](https://tools.ietf.org/html/rfc826). ARP wordt gebruikt om het Ethernet-adres (MAC-adres) toe te wijzen aan een IP-adres.

De ARP-tabel bevat de volgende informatie voor de primaire en secundaire interfaces voor elke peering-type:

1. Toewijzing van het IP-adres van de on-premises router interface aan het MAC-adres
2. Toewijzing van IP-adres van de ExpressRoute-router interface aan het MAC-adres
3. Leeftijd van de toewijzing

ARP-tabellen kunnen helpen bij het valideren van laag 2-configuratie en het oplossen van problemen met elementaire laag 2-connectiviteit. 

Voor beeld van ARP-tabel: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


De volgende sectie bevat informatie over hoe u de ARP-tabellen kunt weer geven die door de ExpressRoute-rand routers worden gezien. 

## <a name="prerequisites-for-learning-arp-tables"></a>Vereisten voor het leren van ARP-tabellen
Zorg ervoor dat de onderstaande gegevens waar zijn voordat u verder gaat:

* Een geldig ExpressRoute-circuit dat is geconfigureerd met ten minste één peering. Het circuit moet volledig worden geconfigureerd door de connectiviteits provider. U of uw connectiviteits provider moet ten minste Azure private, Azure Public of micro soft-peering op dit circuit hebben geconfigureerd.
* IP-adresbereiken die worden gebruikt voor het configureren van de peerings. Bekijk de voor beelden van IP-adres toewijzingen op de [pagina ExpressRoute-routerings vereisten](expressroute-routing.md) voor meer informatie over hoe IP-adressen worden toegewezen aan interfaces. U kunt informatie over de peering-configuratie verkrijgen door de [configuratie pagina voor ExpressRoute-peering](expressroute-howto-routing-arm.md)te bekijken.
* Informatie van uw netwerk team/connectiviteits provider op de MAC-adressen van interfaces die worden gebruikt met deze IP-adressen.
* U moet de meest recente Power shell-module voor Azure (versie 1,50 of hoger) hebben.

> [!NOTE]
> Als laag 3 wordt opgegeven door de service provider en de ARP-tabellen in de onderstaande Portal/uitvoer leeg zijn, moet u de circuit configuratie vernieuwen met de knop Vernieuwen in de portal. Met deze bewerking wordt de juiste routerings configuratie op uw circuit toegepast. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>ARP-tabellen ophalen voor uw ExpressRoute-circuit
In deze sectie vindt u instructies voor het weer geven van de ARP-tabellen per peering met behulp van Power shell. U of uw connectiviteits provider moet de peering hebben geconfigureerd voordat u verder gaat. Elk circuit heeft twee paden (primair en secundair). U kunt de ARP-tabel voor elk pad afzonderlijk controleren.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabellen voor persoonlijke Azure-peering
De volgende cmdlet biedt de ARP-tabellen voor persoonlijke Azure-peering

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Voorbeeld uitvoer wordt hieronder weer gegeven voor een van de paden

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabellen voor open bare Azure-peering
Met de volgende cmdlet worden de ARP-tabellen voor open bare Azure-peering geboden

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Voorbeeld uitvoer wordt hieronder weer gegeven voor een van de paden

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabellen voor micro soft-peering
Met de volgende cmdlet worden de ARP-tabellen voor micro soft-peering geboden

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Voorbeeld uitvoer wordt hieronder weer gegeven voor een van de paden

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Deze informatie gebruiken
De ARP-tabel van een peering kan worden gebruikt om de validatie van laag 2-configuratie en-connectiviteit te bepalen. Deze sectie bevat een overzicht van de wijze waarop ARP-tabellen eruitzien onder verschillende scenario's.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tabel wanneer een circuit actief is (verwachte status)
* De ARP-tabel bevat een vermelding voor de on-premises zijde met een geldig IP-adres en MAC-adres. Dit geldt ook voor de micro soft-kant. 
* Het laatste octet van het on-premises IP-adres zal altijd een oneven getal zijn.
* Het laatste octet van het micro soft IP-adres zal altijd een even getal zijn.
* Hetzelfde MAC-adres wordt weer gegeven op de micro soft-zijde voor alle drie peerings (primair/secundair). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tabel wanneer de on-premises/connectiviteits provider problemen ondervindt
Als er een probleem met de on-premises of connectiviteits provider optreedt, wordt in de ARP-tabel een van de twee dingen weer gegeven. U ziet dat het on-premises MAC-adres onvolledig is of alleen de micro soft-vermelding in de ARP-tabel wordt weer gegeven.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
of
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Open een ondersteunings aanvraag met uw connectiviteits provider om deze problemen op te lossen. Als de ARP-tabel geen IP-adressen heeft van de interfaces die zijn toegewezen aan MAC-adressen, controleert u de volgende informatie:
> 
> 1. Als het eerste IP-adres van het/30-subnet dat is toegewezen voor de koppeling tussen de MSEE-PR en MSEE, wordt gebruikt op de interface van MSEE-PR. Azure maakt altijd gebruik van het tweede IP-adres voor Msee's.
> 2. Controleer of de VLAN-labels van de klant (C-tag) en de service (S-tag) overeenkomen met het MSEE-PR-en MSEE-paar.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tabel wanneer aan de kant van micro soft problemen ondervindt
* Er wordt geen ARP-tabel weer gegeven voor een peering als er problemen zijn met de zijde van micro soft. 
* Open een ondersteunings ticket met [ondersteuning van micro soft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Geef op dat er een probleem is met laag 2-connectiviteit. 

## <a name="next-steps"></a>Volgende stappen
* Valideer laag 3-configuraties voor uw ExpressRoute-circuit.
  * Route samenvatting ophalen om de status van BGP-sessies te bepalen.
  * Route tabel ophalen om te bepalen welke voor voegsels worden geadverteerd over ExpressRoute.
* Gegevens overdracht valideren door bytes in/uit te controleren.
* Open een ondersteunings ticket met [micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u nog steeds problemen ondervindt.

