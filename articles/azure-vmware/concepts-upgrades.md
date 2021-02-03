---
title: Concepten-updates en upgrades voor Privécloud
description: Meer informatie over de belangrijkste upgrade processen en functies in de Azure VMware-oplossing.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8338666d70bd0ca4ea3c6b32bd8f8d205feaf7c0
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509194"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Updates en upgrades voor persoonlijke Clouds in azure VMware-oplossing

Een van de belangrijkste voor delen van persoonlijke Clouds van Azure VMware-oplossingen is dat het platform voor u wordt onderhouden. Platform onderhoud omvat geautomatiseerde updates voor een gevalideerde VMware-software bundel, om ervoor te zorgen dat u de nieuwste versie van de gevalideerde persoonlijke cloud software van Azure VMware-oplossing gebruikt.

Met name een persoonlijke cloud van Azure VMware-oplossing bevat:

- Toegewezen bare-metal server knooppunten ingericht met VMware ESXi Hyper Visor 
- vCenter-Server voor het beheren van ESXi en vSAN 
- VMware NSX-T software-gedefinieerde netwerken voor virtuele machines met vSphere-werk belasting  
- VMware vSAN-gegevens opslag voor virtuele machines met vSphere-werk belasting  
- VMware HCX voor werk belasting mobiliteit  

Naast deze onderdelen bevat een persoonlijke cloud van Azure VMware-oplossing resources in azure aan die vereist zijn voor connectiviteit en om de privécloud te kunnen uitvoeren. De Azure VMware-oplossing bewaakt voortdurend de status van zowel de aan-als de VMware-onderdelen. Wanneer er een fout wordt gedetecteerd door de Azure VMware-oplossing, wordt er actie ondernomen om de mislukte onderdelen te herstellen. 

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

Naast het maken van updates, maakt Azure VMware-oplossing een configuratie back-up van deze VMware-onderdelen:

- vCenter Server 
- NSX-T-beheer 

In het geval van een storing kan de Azure VMware-oplossing deze herstellen vanuit de back-up van de configuratie. 

Voor meer informatie over de versies van VMware-software raadpleegt u het [concept artikel private clouds and clusters](concepts-private-clouds-clusters.md) en de [Veelgestelde vragen](faq.md).

## <a name="next-steps"></a>Volgende stappen

Nu u de belangrijkste upgrade processen en functies in de Azure VMware-oplossing hebt behandeld, wilt u mogelijk meer informatie over:

- [Een privécloud maken](tutorial-create-private-cloud.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
