---
title: Concepten-updates en upgrades voor Privécloud
description: Meer informatie over de belangrijkste upgrade processen en functies in de Azure VMware-oplossing.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586145"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Updates en upgrades voor persoonlijke Clouds in azure VMware-oplossing

Een van de voor delen van persoonlijke Clouds van Azure VMware-oplossingen is dat het platform voor u wordt onderhouden. Onderhoud omvat geautomatiseerde updates voor een gevalideerde VMware-software bundel om ervoor te zorgen dat u de nieuwste versie van de persoonlijke cloud software van Azure VMware kunt gebruiken.

Met name een persoonlijke cloud van Azure VMware-oplossing bevat:

- Toegewezen bare-metal server knooppunten ingericht met VMware ESXi Hyper Visor 
- vCenter-Server voor het beheren van ESXi en vSAN 
- VMware NSX-T software-gedefinieerde netwerken voor virtuele machines met vSphere-werk belasting  
- VMware vSAN-gegevens opslag voor virtuele machines met vSphere-werk belasting  
- VMware HCX voor werk belasting mobiliteit  

Een Azure VMware-oplossing privécloud bevat ook bronnen in de Azure-aan die vereist zijn voor connectiviteit en om de privécloud te kunnen uitvoeren. De Azure VMware-oplossing bewaakt voortdurend de status van zowel de aan-als de VMware-onderdelen. Wanneer er een fout wordt gedetecteerd door de Azure VMware-oplossing, wordt er actie ondernomen om de mislukte onderdelen te herstellen. 

## <a name="what-components-get-updated"></a>Welke onderdelen worden bijgewerkt?   

De Azure VMware-oplossing werkt de volgende VMware-onderdelen bij: 

- vCenter Server en ESXi die worden uitgevoerd op de bare-metal server knooppunten 
- vSAN 
- NSX-T 

De Azure VMware-oplossing werkt ook de software in de aan bij, zoals Stuur Programma's, software op de netwerk switches en firmware op de bare-metal knoop punten. 

## <a name="types-of-updates"></a>Typen updates

Met de Azure VMware-oplossing worden de volgende typen updates toegepast op VMware-onderdelen:

- Patches: beveiligings patches en oplossingen voor oplossingen die worden vrijgegeven door VMware. 
- Updates: secundaire versie-updates van een of meer VMware-onderdelen. 
- Upgrades: belang rijke versie-updates van een of meer VMware-onderdelen.

U wordt gewaarschuwd voor en nadat patches zijn toegepast op uw persoonlijke Clouds. We werken ook samen met u bij het plannen van een onderhouds venster voordat u updates of upgrades toepast op uw privécloud. 

## <a name="vmware-appliance-backup"></a>VMware-apparaat back-up 

Azure VMware-oplossing maakt ook een configuratie back-up van de volgende VMware-onderdelen:

- vCenter Server 
- NSX-T-beheer 

In het geval van een storing kan de Azure VMware-oplossing deze onderdelen herstellen vanuit de back-up van de configuratie. 

## <a name="vmware-software-versions"></a>VMware-software versies
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Volgende stappen

Nu u de belangrijkste upgrade processen en functies in de Azure VMware-oplossing hebt behandeld, wilt u mogelijk meer informatie over:

- [Een privécloud maken](tutorial-create-private-cloud.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
