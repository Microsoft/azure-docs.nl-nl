---
title: Verbinding maken met Azure Data Lake Storage Gen1 vanuit VNETs | Microsoft Docs
description: Meer informatie over het inschakelen van toegang tot Azure Data Lake Storage Gen1 van virtuele Azure-machines met beperkte toegang tot resources.
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: e319cf9dfc01546607e20572c5bf4930fd974c75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104033"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Toegang tot Azure Data Lake Storage Gen1 van Vm's binnen een Azure-VNET
Azure Data Lake Storage Gen1 is een PaaS-service die wordt uitgevoerd op open bare Internet-IP-adressen. Elke server die verbinding kan maken met het open bare Internet kan meestal ook verbinding maken met Azure Data Lake Storage Gen1-eind punten. Standaard hebben alle virtuele machines in azure VNETs toegang tot internet en kunnen ze dus toegang krijgen tot Azure Data Lake Storage Gen1. Het is echter mogelijk om Vm's in een VNET zo te configureren dat ze geen toegang tot internet hebben. Voor dergelijke Vm's is de toegang tot Azure Data Lake Storage Gen1 ook beperkt. Het blok keren van open bare Internet toegang voor Vm's in azure VNETs kan worden uitgevoerd met behulp van een van de volgende methoden:

* Door netwerk beveiligings groepen (NSG) te configureren
* Door de gebruiker gedefinieerde routes configureren (UDR)
* Door routes uit te wisselen via BGP (standaard protocol voor dynamische route ring), wanneer ExpressRoute wordt gebruikt, waardoor de toegang tot internet wordt geblokkeerd

In dit artikel wordt uitgelegd hoe u toegang tot Azure Data Lake Storage Gen1 kunt inschakelen vanuit Azure-Vm's, die zijn beperkt tot toegang tot resources met een van de drie eerder beschreven methoden.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Connectiviteit met Azure Data Lake Storage Gen1 van Vm's met beperkte connectiviteit inschakelen
Om toegang te krijgen tot Azure Data Lake Storage Gen1 van dergelijke Vm's, moet u ze configureren voor toegang tot het IP-adres voor de regio waar het Azure Data Lake Storage Gen1-account beschikbaar is. U kunt de IP-adressen voor uw Data Lake Storage Gen1-account regio's identificeren door de DNS-namen van uw accounts () te herleiden `<account>.azuredatalakestore.net` . Als u DNS-namen van uw accounts wilt omzetten, kunt u hulpprogram ma's zoals **nslookup** gebruiken. Open een opdracht prompt op de computer en voer de volgende opdracht uit:

```console
nslookup mydatastore.azuredatalakestore.net
```

De uitvoer lijkt op het volgende. De waarde voor de eigenschap **adres** is het IP-adres dat is gekoppeld aan uw data Lake Storage gen1-account.

```output
Non-authoritative answer:
Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
Address:  104.44.88.112
Aliases:  mydatastore.azuredatalakestore.net
```


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Connectiviteit van Vm's die zijn beperkt met behulp van NSG inschakelen
Wanneer een NSG-regel wordt gebruikt om de toegang tot internet te blok keren, kunt u een andere NSG maken waarmee toegang tot het IP-adres van Data Lake Storage Gen1 is toegestaan. Zie [overzicht van netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over NSG-regels. Zie [een netwerk beveiligings groep maken](../virtual-network/tutorial-filter-network-traffic.md)voor instructies over het maken van nsg's.

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Connectiviteit van Vm's beperken met behulp van UDR of ExpressRoute
Wanneer routes, ofwel Udr's of BGP-exchangeed routes, worden gebruikt om de toegang tot internet te blok keren, moet een speciale route worden geconfigureerd zodat Vm's in dergelijke subnetten toegang hebben tot Data Lake Storage Gen1-eind punten. Zie door de [gebruiker gedefinieerde routes Overview](../virtual-network/virtual-networks-udr-overview.md)(Engelstalig) voor meer informatie. Zie [Udr's maken in Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md)voor instructies voor het maken van udr's.

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Connectiviteit van Vm's die zijn beperkt met behulp van ExpressRoute inschakelen
Wanneer een ExpressRoute-circuit is geconfigureerd, hebben de on-premises servers toegang tot Data Lake Storage Gen1 via open bare peering. Meer informatie over het configureren van ExpressRoute voor open bare peering is beschikbaar op [ExpressRoute-Veelgestelde vragen](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)