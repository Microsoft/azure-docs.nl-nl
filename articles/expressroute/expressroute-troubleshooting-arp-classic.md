---
title: 'Azure ExpressRoute: ARP-tabellen-problemen oplossen: klassiek'
description: Op deze pagina vindt u instructies voor het ophalen van de ARP-tabellen voor een ExpressRoute-circuit-klassiek implementatie model.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394836"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>ARP-tabellen ophalen in het klassieke implementatie model
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [Power shell-klassiek](expressroute-troubleshooting-arp-classic.md)
> 
> 

Dit artikel begeleidt u stapsgewijs door de stappen voor het ophalen van ARP-tabellen (Address Resolution Protocol) voor uw Azure ExpressRoute-circuit.

> [!IMPORTANT]
> Dit document is bedoeld om u te helpen bij het vaststellen en oplossen van eenvoudige problemen. Het is niet bedoeld als vervanging van micro soft-ondersteuning. Als u het probleem niet kunt oplossen met behulp van de volgende richt lijnen, opent u een ondersteunings aanvraag met [Microsoft Azure Help en ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP-en ARP-tabellen (Address Resolution Protocol)
ARP is een Layer 2-protocol dat is gedefinieerd in [RFC 826](https://tools.ietf.org/html/rfc826). ARP wordt gebruikt om een Ethernet-adres (MAC-adres) toe te wijzen aan een IP-adres.

Een ARP-tabel biedt een toewijzing van het IPv4-adres en MAC-adres voor een bepaalde peering. De ARP-tabel voor een ExpressRoute-circuit peering biedt de volgende informatie voor elke interface (primair en secundair):

1. Toewijzing van een on-premises router Interface IP-adres aan een MAC-adres
2. Toewijzing van een IP-adres van de ExpressRoute-router interface aan een MAC-adres
3. De leeftijd van de toewijzing

ARP-tabellen kunnen helpen bij het valideren van de configuratie van laag 2 en met het oplossen van problemen met de eenvoudige laag 2-connectiviteit.

Hier volgt een voor beeld van een ARP-tabel:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


De volgende sectie bevat informatie over het weer geven van de ARP-tabellen die worden gezien door de ExpressRoute-rand routers.

## <a name="prerequisites-for-using-arp-tables"></a>Vereisten voor het gebruik van ARP-tabellen
Zorg ervoor dat u het volgende hebt voordat u doorgaat:

* Een geldig ExpressRoute-circuit dat is geconfigureerd met ten minste één peering. Het circuit moet volledig worden geconfigureerd door de connectiviteits provider. U (of uw connectiviteits provider) moet ten minste één van de peerings (Azure private, Azure Public of micro soft) op dit circuit configureren.
* IP-adresbereiken die worden gebruikt voor het configureren van de peerings (Azure private, Azure Public en micro soft). Bekijk de voor beelden van IP-adres toewijzingen op de [pagina ExpressRoute-routerings vereisten](expressroute-routing.md) voor meer informatie over hoe IP-adressen worden toegewezen aan interfaces aan de zijkant en aan de kant van de ExpressRoute. U kunt informatie over de peering-configuratie verkrijgen door de [configuratie pagina voor ExpressRoute-peering](expressroute-howto-routing-classic.md)te bekijken.
* Informatie van uw netwerk team of connectiviteits provider over de MAC-adressen van de interfaces die worden gebruikt met deze IP-adressen.
* De meest recente Windows Power shell-module voor Azure (versie 1,50 of hoger).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-tabellen voor uw ExpressRoute-circuit
In deze sectie vindt u instructies voor het weer geven van de ARP-tabellen voor elk type peering met behulp van Power shell. Voordat u doorgaat, moet u of uw connectiviteits provider de peering configureren. Elk circuit heeft twee paden (primair en secundair). U kunt de ARP-tabel voor elk pad afzonderlijk controleren.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabellen voor persoonlijke Azure-peering
De volgende cmdlet biedt de ARP-tabellen voor persoonlijke Azure-peering:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Hieronder ziet u een voor beeld van uitvoer voor een van de volgende paden:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabellen voor open bare Azure-peering:
De volgende cmdlet biedt de ARP-tabellen voor open bare Azure-peering:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Hieronder ziet u een voor beeld van uitvoer voor een van de volgende paden:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Hieronder ziet u een voor beeld van uitvoer voor een van de volgende paden:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabellen voor micro soft-peering
De volgende cmdlet biedt de ARP-tabellen voor micro soft-peering:

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


Voorbeeld uitvoer wordt hieronder weer gegeven voor een van de paden:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Deze informatie gebruiken
De ARP-tabel van een peering kan worden gebruikt om de configuratie en connectiviteit van laag 2 te valideren. Deze sectie bevat een overzicht van hoe ARP-tabellen in verschillende scenario's worden weer gegeven.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP-tabel wanneer een circuit een operationele (verwachte) status heeft
* De ARP-tabel bevat een vermelding voor de on-premises zijde met een geldig IP-en MAC-adres en een vergelijk bare vermelding voor de micro soft-kant.
* Het laatste octet van het on-premises IP-adres is altijd een oneven getal.
* Het laatste octet van het micro soft IP-adres is altijd een even getal.
* Hetzelfde MAC-adres wordt weer gegeven op de micro soft-zijde voor alle drie peerings (primair/secundair).

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tabel wanneer deze on-premises is of wanneer de connectiviteits provider problemen heeft
 Er wordt slechts één vermelding in de ARP-tabel weer gegeven. Hier ziet u de toewijzing tussen het MAC-adres en het IP-adres dat wordt gebruikt op de micro soft-website.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Als u een probleem ondervindt, opent u een ondersteunings aanvraag met uw connectiviteits provider om dit op te lossen.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tabel wanneer de micro soft-kant problemen heeft
* U ziet geen ARP-tabel die wordt weer gegeven voor een peering als er problemen zijn met de zijde van micro soft.
* Open een ondersteunings aanvraag met [Microsoft Azure Help en ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Geef op dat er een probleem is met laag 2-connectiviteit.

## <a name="next-steps"></a>Volgende stappen
* Controleer laag 3-configuraties voor uw ExpressRoute-circuit:
  * Een route samenvatting ophalen om de status van BGP-sessies te bepalen.
  * Een route tabel ophalen om te bepalen welke voor voegsels worden geadverteerd via ExpressRoute.
* Gegevens overdracht valideren door bytes in en uit te bekijken.
* Open een ondersteunings aanvraag met [Microsoft Azure Help en ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u nog steeds problemen ondervindt.

