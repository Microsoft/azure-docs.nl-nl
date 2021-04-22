---
title: 'Concepten : privé clouds bewaken en Azure VMware Solution herstellen'
description: Ontdek hoe Azure VMware Solution servers in VMware ESXi privécloud bewaakt en Azure VMware Solution herstelt.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 9fa94d6093e03432f20672ecf5c0160ce479e175
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871665"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Privé clouds bewaken Azure VMware Solution herstellen

Azure VMware Solution bewaakt continu de VMware ESXi servers in een Azure VMware Solution privécloud. 

## <a name="what-azure-vmware-solution-monitors"></a>Wat Azure VMware Solution monitors

Azure VMware Solution controleert de volgende voorwaarden op de host:  

- Processorstatus 
- Geheugenstatus 
- Verbindings- en energietoestand 
- Status van hardwareventilator 
- Netwerkverbindingsverlies 
- Status van hardwaresysteembord 
- Er zijn fouten opgetreden op de schijf(en) van een vSAN-host 
- Hardwarespanning 
- Status van de hardwaretemperatuur 
- Status van hardware-energie 
- Opslagstatus 
- Verbindingsfout 

> [!NOTE]
> Azure VMware Solution mogen de hierboven gedefinieerde VMware vCenter-alarmen niet bewerken of verwijderen, omdat deze worden beheerd door het Azure VMware Solution-besturingsvlak in vCenter. Deze alarmen worden gebruikt door Azure VMware Solution bewaking om het herstelproces van Azure VMware Solution host te activeren.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware Solution host herstellen  

Wanneer Azure VMware Solution een degradatie of fout op een Azure VMware Solution detecteert, wordt het hostbemiddelingsproces activeert. Bij het herstellen van de host moet het defecte knooppunt worden vervangen door een nieuw, goed werkend knooppunt.  

Herstel van de host begint met het toevoegen van een nieuw knooppunt met een goede status in het cluster. Vervolgens wordt, indien mogelijk, de defecte host in VMware vSphere onderhoudsmodus geplaatst. VMware vMotion verplaatst de VM's van de defecte host naar andere beschikbare servers in het cluster, waardoor er mogelijk geen downtime is voor livemigratie van workloads. Als de defecte host niet in de onderhoudsmodus kan worden geplaatst, wordt de host uit het cluster verwijderd.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt behandeld hoe u Azure VMware Solution bewaakt en herstelt van privé clouds, wilt u mogelijk meer informatie over:

- [Azure VMware Solution privécloudupgrades](concepts-upgrades.md)
- [Een resource Azure VMware Solution inschakelen](enable-azure-vmware-solution.md)
