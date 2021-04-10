---
title: Ken de voor waarden van de Azure BareMetal-infra structuur
description: Ken de voor waarden van de Azure BareMetal-infra structuur.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 53a601cc4556198479d8ca5d7495942d4dc2762c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580132"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Ken de voor waarden voor de BareMetal-infra structuur

In dit artikel behandelen we enkele belang rijke termen met betrekking tot de BareMetal-infra structuur.

- **Revisie**: er is een oorspronkelijke stempel revisie bekend als Revision 3 (Rev 3) en twee extra stempel revisies voor BareMetal-instantie tempels. Elke stamp wijkt af van de architectuur en nabijheid van de virtuele machines van Azure:
    - **Revisie 4** (Rev 4): een nieuwer ontwerp dat dichter bij de virtuele Azure-machine (VM) fungeert en de latentie tussen Azure-vm's en SAP Hana exemplaren verlaagt. 
    - **Revisie 4,2** (Rev 4,2): de meest recente BareMetal-infra structuur met de bestaande Rev 4-architectuur. Rev 4 biedt dichter nabijheid van de Azure virtual machine-hosts (VM). Het heeft aanzienlijke verbeteringen in de netwerk latentie tussen virtuele Azure-machines en BareMetal-instanties die zijn geïmplementeerd in Rev 4-stem pels of-rijen. U kunt uw BareMetal-instanties openen en beheren via de Azure Portal.    

- **Stempel**: definieert de micro soft interne implementatie grootte van BareMetal-instanties. Voordat exemplaren kunnen worden geïmplementeerd, moet een BareMetal-instantie stempel met reken-, netwerk-en opslag racks worden geïmplementeerd op een datacenter locatie. Een dergelijke implementatie wordt een BareMetal-instantie stempel genoemd.

- **Tenant**: een klant die een BareMetal-instantie stempel implementeert, wordt geïsoleerd als een *Tenant.* Een Tenant wordt geïsoleerd in het netwerk, de opslag en de compute-laag van andere tenants. Opslag-en reken eenheden die aan de verschillende tenants zijn toegewezen, kunnen elkaar niet zien of communiceren met elkaar op het stempel niveau van de BareMetal-instantie. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Ook is er geen communicatie tussen tenants op het stempel niveau van de BareMetal-instantie.

## <a name="next-steps"></a>Volgende stappen
Nu u een belang rijke terminologie van de BareMetal-infra structuur hebt geïntroduceerd, kunt u het volgende weten:
- Meer informatie over de [BareMetal-infra structuur](concepts-baremetal-infrastructure-overview.md).
- [Verbinding maken met BareMetal-infrastructuur instanties in azure](connect-baremetal-infrastructure.md).

