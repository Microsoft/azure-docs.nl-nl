---
title: Ken de voor waarden van de Azure BareMetal-infra structuur
description: Ken de voor waarden van de Azure BareMetal-infra structuur.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861924"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Ken de voor waarden voor de BareMetal-infra structuur

In dit artikel behandelen we enkele belang rijke BareMetal-voor waarden.

- **Revisie**: er is een oorspronkelijke stempel revisie bekend als Revision 3 (Rev 3) en twee verschillende stempel revisies voor BareMetal-instantie tempels. Elke stamp wijkt af van de architectuur en nabijheid van de virtuele machines van Azure:
    - **Revisie 4** (Rev 4): een nieuwer ontwerp dat dichter bij de virtuele Azure-machine (VM) fungeert en de latentie tussen Azure Vm's en BareMetal-exemplaar eenheden verlaagt. 
    - **Revisie 4,2** (Rev 4,2): de meest recente BareMetal-infra structuur met de bestaande Rev 4-architectuur. Rev 4 biedt dichter nabijheid van de Azure virtual machine-hosts (VM). Het heeft aanzienlijke verbeteringen in de netwerk latentie tussen Azure Vm's en BareMetal exemplaar eenheden die zijn geïmplementeerd in Rev 4-stem pels of-rijen. U kunt uw BareMetal-instanties openen en beheren via de Azure Portal.    

- **Stempel**: definieert de micro soft interne implementatie grootte van BareMetal-instanties. Voordat exemplaar-eenheden kunnen worden geïmplementeerd, moet een BareMetal-instantie stempel dat bestaat uit compute-, netwerk-en opslag racks, worden geïmplementeerd op een datacenter locatie. Een dergelijke implementatie wordt een BareMetal-instantie stempel of revisie 4,2 genoemd.

- **Tenant**: een door de klant geïmplementeerde BareMetal-instantie stempel wordt geïsoleerd in een *Tenant.* Een Tenant wordt geïsoleerd in het netwerk, de opslag en de compute-laag van andere tenants. Opslag-en reken eenheden die aan de verschillende tenants zijn toegewezen, kunnen elkaar niet zien of communiceren met elkaar op het stempel niveau van de BareMetal-instantie. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Ook is er geen communicatie tussen tenants op het stempel niveau van de BareMetal-instantie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [BareMetal-infra structuur](workloads/sap/baremetal-overview-architecture.md) of over het [identificeren en gebruiken van BareMetal-exemplaar eenheden](workloads/sap/baremetal-infrastructure-portal.md). 