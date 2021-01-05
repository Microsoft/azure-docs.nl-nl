---
title: Ken de voor waarden van de Azure BareMetal-infra structuur
description: Ken de voor waarden van de Azure BareMetal-infra structuur.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829122"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Ken de voor waarden voor de BareMetal-infra structuur

In dit artikel behandelen we enkele belang rijke BareMetal-voor waarden.

- **Revisie**: er zijn twee verschillende stempel revisies voor BareMetal-instantie tempels. Elke versie wijkt af van de architectuur en nabijheid van de hosts van virtuele Azure-machines:
    - **Revisie 3** (Rev 3): is het oorspronkelijke ontwerp.
    - **Revisie 4** (Rev 4): is een nieuw ontwerp dat dichter bij de virtuele Azure-machine (VM) fungeert en de latentie tussen Azure Vm's en BareMetal-exemplaar eenheden verlaagt. 
    - **Revisie 4,2** (Rev 4,2): is de meest recente BareMetal-infra structuur die gebruikmaakt van de bestaande Rev 4-architectuur. U kunt uw BareMetal-instanties openen en beheren via de Azure Portal.  

- **Stempel**: definieert de micro soft interne implementatie grootte van BareMetal-instanties. Voordat exemplaar-eenheden kunnen worden geïmplementeerd, moet een BareMetal-instantie stempel die uit reken-, netwerk-en opslag rekken bestaat, op een datacenter locatie worden geïmplementeerd. Een dergelijke implementatie wordt een BareMetal-instantie stempel of revisie 4,2 genoemd.

- **Tenant**: een door de klant geïmplementeerde BareMetal-instantie stempel wordt geïsoleerd in een *Tenant.* Een Tenant wordt geïsoleerd in het netwerk, de opslag en de compute-laag van andere tenants. Opslag-en reken eenheden die aan de verschillende tenants zijn toegewezen, kunnen elkaar niet zien of communiceren met elkaar op het stempel niveau van de BareMetal-instantie. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Ook is er geen communicatie tussen tenants op het stempel niveau van de BareMetal-instantie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het identificeren en gebruiken van BareMetal-exemplaar eenheden via de [Azure Portal](workloads/sap/baremetal-infrastructure-portal.md).


 