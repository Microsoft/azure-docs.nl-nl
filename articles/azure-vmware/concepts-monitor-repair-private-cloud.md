---
title: 'Concepten: persoonlijke Clouds van Azure VMware-oplossing controleren en herstellen'
description: Meer informatie over hoe de Azure VMware-oplossing VMware ESXi servers bewaakt en herstelt in een persoonlijke cloud van Azure VMware-oplossing.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 11a3c53bff7ce7b67b677977eddb9829f336672d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684692"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Persoonlijke Clouds van Azure VMware-oplossing controleren en herstellen

De Azure VMware-oplossing bewaakt voortdurend de VMware ESXi-servers op een Azure VMware-oplossing privécloud. 

## <a name="what-azure-vmware-solution-monitors"></a>Wat de Azure VMware-oplossing bewaakt

Met de Azure VMware-oplossing wordt het volgende bewaakt voor fout voorwaarden op de host:  

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

Wanneer de Azure VMware-oplossing een afbraak of fout op een Azure VMware-oplossings knooppunt in de privécloud van een Tenant detecteert, wordt het proces voor het herstellen van hosts geactiveerd. Voor het door voeren van de host moet het defecte knoop punt worden vervangen door een nieuw goedend knoop punt.  

Het proces voor het door voeren van een host begint met het toevoegen van een nieuw ongezond knoop punt in het cluster. Als dat mogelijk is, wordt de defecte host in VMware vSphere onderhouds modus geplaatst. VMware vMotion wordt gebruikt om de virtuele machines van de defecte host te verplaatsen naar andere beschik bare servers in het cluster, waardoor er sprake is van een Livemigratie van geen uitval tijd van werk belastingen. In scenario's waarin de defecte host niet in de onderhouds modus kan worden geplaatst, wordt de host uit het cluster verwijderd.

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele onderwerpen die u mogelijk wilt meer informatie over:

- [Azure VMware-oplossing, upgrades voor privécloud](concepts-upgrades.md)
- [Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Bescherm uw Azure VMware-oplossing-Vm's met Azure Security Center-integratie](azure-security-integration.md)
- [Back-ups maken van Azure VMware-oplossing Vm's met Azure Backup Server](backup-azure-vmware-solution-virtual-machines.md)
- [Herstel na nood geval voor virtuele machines met behulp van de Azure VMware-oplossing volt ooien](disaster-recovery-for-virtual-machines.md)
