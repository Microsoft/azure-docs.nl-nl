---
title: 'Concepten: persoonlijke Clouds van Azure VMware-oplossing controleren en herstellen'
description: Meer informatie over hoe de Azure VMware-oplossing VMware ESXi servers bewaakt en herstelt in een persoonlijke cloud van Azure VMware-oplossing.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633135"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Persoonlijke Clouds van Azure VMware-oplossing controleren en herstellen

De Azure VMware-oplossing bewaakt voortdurend de VMware ESXi-servers op een Azure VMware-oplossing privécloud. 

## <a name="what-azure-vmware-solution-monitors"></a>Wat de Azure VMware-oplossing bewaakt

Met de Azure VMware-oplossing worden de volgende voor waarden op de host bewaakt:  

- Processor status 
- Geheugen status 
- Verbinding en energie status 
- Status van hardware-ventilator 
- Netwerk connectiviteits verlies 
- Status van hardwaresysteem bord 
- Er zijn fouten opgetreden op de schijven van een vSAN-host 
- Hardwarematige spanning 
- Status van hardwarematige Tempe ratuur 
- Energie status van hardware 
- Opslag status 
- Verbindingsfout 

> [!NOTE]
> Tenant beheerders van Azure VMware-oplossing mogen de hierboven gedefinieerde VMware vCenter-alarm signalen niet bewerken of verwijderen, aangezien deze worden beheerd door het beheer vlak van de Azure VMware-oplossing in vCenter. Deze waarschuwingen worden gebruikt door de bewaking van Azure VMware-oplossingen om het herstel proces van de Azure VMware-oplossing te activeren.

## <a name="azure-vmware-solution-host-remediation"></a>Herstel van de Azure VMware-oplossing host  

Wanneer de Azure VMware-oplossing een afbraak of fout op een Azure VMware-oplossings knooppunt detecteert, wordt het proces voor het herstellen van hosts geactiveerd. Voor het door voeren van de host moet het defecte knoop punt worden vervangen door een nieuw goedend knoop punt.  

Het herstel van de host begint met het toevoegen van een nieuw goed in orde knoop punt in het cluster. Als dat mogelijk is, wordt de defecte host in VMware vSphere onderhouds modus geplaatst. VMware vMotion verplaatst de Vm's van de defecte host naar andere beschik bare servers in het cluster, waardoor er mogelijk sprake is van een downtime van nul voor Livemigratie van werk belastingen. Als de defecte host niet in de onderhouds modus kan worden geplaatst, wordt de host uit het cluster verwijderd.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt laten zien hoe u met Azure VMware-oplossing persoonlijke Clouds bewaakt en herstelt, kunt u het volgende weten:

- [Azure VMware-oplossing persoonlijke Cloud upgrades](concepts-upgrades.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).
