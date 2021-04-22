---
title: Concepten - Privécloudupdates en -upgrades
description: Meer informatie over de belangrijkste upgradeprocessen en -functies in Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ced5832a6d994f6cbc7e659d44ce4f6ac88681d0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876810"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware Solution privécloudupdates en -upgrades uitvoeren

Een voordeel van Azure VMware Solution privé clouds is dat het platform voor u wordt onderhouden. Onderhoud omvat geautomatiseerde updates voor een met VMware gevalideerde softwarebundel om ervoor te zorgen dat u de nieuwste versie van Azure VMware Solution privécloudsoftware gebruikt.

Een privécloud met Azure VMware Solution omvat met name:

- Toegewezen bare-metalserverknooppunten die zijn ingericht met VMware ESXi hypervisor 
- vCenter-server voor het beheren van ESXi en vSAN 
- VMware NSX-T-software-gedefinieerde netwerken voor vSphere-workload-VM's  
- VMware vSAN-gegevensstore voor vSphere-workload-VM's  
- VMware HCX voor workloadmobiliteit  

Een Azure VMware Solution privécloud bevat ook resources in de Azure-onderlaag die vereist zijn voor connectiviteit en om de privécloud te kunnen gebruiken. Azure VMware Solution bewaakt continu de status van zowel de onderlaag als de VMware-onderdelen. Wanneer Azure VMware Solution een fout detecteert, wordt actie ondernomen om de mislukte onderdelen te herstellen. 

## <a name="what-components-get-updated"></a>Welke onderdelen worden bijgewerkt?   

Azure VMware Solution de volgende VMware-onderdelen bijgewerkt: 

- vCenter Server en ESXi die worden uitgevoerd op de bare-metalserverknooppunten 
- vSAN 
- NSX-T 

Azure VMware Solution werkt ook de software in de onderlaag bij, zoals stuurprogramma's, software op de netwerkswitches en firmware op de bare-metalknooppunten. 

## <a name="types-of-updates"></a>Typen updates

Azure VMware Solution volgende typen updates worden toegepast op VMware-onderdelen:

- Patches: beveiligingspatches en bugfixes uitgebracht door VMware. 
- Updates: kleine versie-updates van een of meer VMware-onderdelen. 
- Upgrades: belangrijke versie-updates van een of meer VMware-onderdelen.

U krijgt een melding voordat en nadat patches zijn toegepast op uw privé clouds. We werken ook samen met u om een onderhoudsvenster te plannen voordat we updates of upgrades toepassen op uw privécloud. 

## <a name="vmware-appliance-backup"></a>Back-up van VMware-apparaat 

Azure VMware Solution maakt ook een configuratieback-up van de volgende VMware-onderdelen:

- vCenter Server 
- NSX-T Manager 

In tijden van fouten kunnen Azure VMware Solution onderdelen herstellen vanuit de configuratieback-up. 

## <a name="vmware-software-versions"></a>VMware-softwareversies
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Volgende stappen

Nu u de belangrijkste upgradeprocessen en -functies in Azure VMware Solution hebt behandeld, wilt u mogelijk meer informatie over:

- [Een privécloud maken](tutorial-create-private-cloud.md)
- [Een resource Azure VMware Solution inschakelen](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
