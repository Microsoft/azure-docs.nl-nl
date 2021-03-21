---
title: Privé-IP-adressen in azure
titlesuffix: Azure Virtual Network
description: Meer informatie over privé-IP-adressen in Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223258"
---
# <a name="private-ip-addresses"></a>Privé-IP-adressen
Met persoonlijke Ip's kan de communicatie tussen resources in Azure worden toegestaan. 

Resources kunnen zijn:
* Azure-Services, zoals:
    * Netwerkinterfaces van virtuele machines
    * Interne load balancers (ILB's)
    * Toepassingsgateways
* In een [virtueel netwerk](virtual-networks-overview.md).
* On-premises netwerk via een VPN-gateway of een ExpressRoute-circuit.

Met persoonlijke IP-adressen kunnen deze bronnen worden gecommuniceerd zonder gebruik te maken van een openbaar netwerk.

## <a name="allocation-method"></a>Toewijzingsmethode

Azure wijst privé IP-adressen toe aan bronnen vanuit het adres bereik van het subnet van het virtuele netwerk waar de bron zich bevindt.

Azure reserveert de eerste vier adressen in elk adres bereik van het subnet. De adressen kunnen niet worden toegewezen aan resources. Als het adres bereik van het subnet bijvoorbeeld 10.0.0.0/16 is, zijn de adressen 10.0.0.0-10.0.0.3 en 10.0.255.255 niet beschikbaar. IP-adressen binnen het adresbereik van het subnet kunnen slechts aan één resource tegelijkertijd worden toegewezen. 

Er zijn twee methoden voor het ontvangen van een privé-IP-adres:

- **Dynamisch**: Azure wijst het volgende beschikbare niet-toegewezen of niet-gereserveerde IP-adres in het adresbereik van het subnet toe. Azure wijst 10.0.0.10 bijvoorbeeld toe aan een nieuwe resource als de adressen van 10.0.0.4 tot en met 10.0.0.9 al aan andere bronnen zijn toegewezen. 

    Dynamisch is de standaardmethode voor toewijzing. Na de toewijzing worden dynamische IP-adressen vrijgegeven als een netwerk interface:
    
    * Verwijderd
    * Opnieuw toegewezen aan een ander subnet binnen hetzelfde virtuele netwerk.
    * De toewijzings methode wordt gewijzigd in statisch en er is een ander IP-adres opgegeven. 
    
    Standaard wijst Azure het vorige dynamisch toegewezen adres toe als het statische adres wanneer u de toewijzingsmethode van dynamisch wijzigt in statisch.

- **Statisch**: u selecteert elk beschikbaar niet-toegewezen of niet-gereserveerde IP-adres in het adresbereik van het subnet en wijst dit toe. 

    Bijvoorbeeld, het adres bereik van een subnet is 10.0.0.0/16 en de adressen 10.0.0.4-10.0.0.9 worden toegewezen aan andere resources. U kunt elk adres tussen 10.0.0.10-10.0.255.254 toewijzen. Statische adressen worden alleen vrijgegeven als een netwerkinterface wordt verwijderd. 
    
    Azure wijst het statische IP-adres toe als het dynamische IP-adres wanneer de toewijzings methode wordt gewijzigd. De nieuwe toewijzing gebeurt ook als het adres niet het volgende beschik bare deel uitmaakt van het subnet. Het adres verandert wanneer de netwerk interface wordt toegewezen aan een ander subnet.
    
    Als u de netwerk interface aan een ander subnet wilt toewijzen, wijzigt u de toewijzings methode van statisch in dynamisch. Wijs de netwerk interface toe aan een ander subnet en wijzig de toewijzings methode vervolgens weer in statisch. Wijs een IP-adres uit het adres bereik van het nieuwe subnet.
    
## <a name="virtual-machines"></a>Virtuele machines

Een of meer privé-IP-adressen zijn toegewezen aan een of meer **netwerk interfaces**. De netwerk interfaces worden toegewezen aan een virtuele [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine. Voor elk privé-IP-adres kunt u de toewijzingsmethode opgeven als dynamisch of statisch.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Interne DNS-hostnaamomzetting (voor virtuele machines)

Virtuele Azure-machines worden standaard geconfigureerd met door [Azure beheerde DNS-servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) . U kunt expliciet aangepaste DNS-servers configureren. Deze DNS-servers bieden een interne naam omzetting voor virtuele machines die zich binnen hetzelfde virtuele netwerk bevinden.

Een toewijzing van de hostnaam aan het privé-IP-adres van een virtuele machine wordt toegevoegd aan de door Azure beheerde DNS-servers. 

Een hostnaam wordt toegewezen aan het primaire IP-adres van de hoofd netwerk interface wanneer een virtuele machine:

* Meerdere netwerkinterfaces
* Meerdere IP-adressen
* Beide

Met Vm's die zijn geconfigureerd met door Azure beheerde DNS worden de hostnamen in hetzelfde virtuele netwerk opgelost. Gebruik een aangepaste DNS-server om hostnamen van Vm's in verbonden virtuele netwerken op te lossen.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Interne load balancers (ILB) en toepassingsgateways

U kunt een privé-IP-adres toewijzen aan de **front-end-** configuratie van een:

* [Interne Load Balancer van Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Dit privé-IP-adres fungeert als een intern eind punt. Het interne eind punt is alleen toegankelijk voor de resources binnen het virtuele netwerk en de externe netwerken die ermee zijn verbonden. Er kan een dynamisch of statisch IP-adres worden toegewezen.

## <a name="at-a-glance"></a>In een oogopslag
In de volgende tabel ziet u de eigenschap waarmee een privé-IP kan worden gekoppeld aan een resource. 

De mogelijke toewijzings methoden die kunnen worden gebruikt, worden ook weer gegeven:

* Dynamisch
* Statisch

| Resource op het hoogste niveau | IP-adreskoppeling | Dynamisch | Statisch |
| --- | --- | --- | --- |
| Virtuele machine |Netwerkinterface |Ja |Ja |
| Load balancer |Front-end-configuratie |Ja |Ja |
| Toepassingsgateway |Front-end-configuratie |Ja |Ja |

## <a name="limits"></a>Limieten
De limieten voor IP-adres Sering vindt u in de volledige set [limieten voor netwerken](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. De limieten gelden per regio en per abonnement. [Neem contact](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) op met ondersteuning om de standaard limieten te verhogen tot de maximum limieten op basis van uw bedrijfs behoeften.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [openbare IP-adressen in azure](public-ip-addresses.md)
* [Een virtuele machine met een statisch privé-IP-adres implementeren via Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)