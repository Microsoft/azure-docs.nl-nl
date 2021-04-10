---
title: 'Basis lijn verzamelen: Aanbevolen procedures voor prestaties & richt lijnen'
description: Bevat stappen voor het verzamelen van een prestatie basislijn als richt lijnen voor het optimaliseren van de prestaties van uw SQL Server op Azure virtual machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572379"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Basis lijn verzamelen: Best practices voor prestaties voor SQL Server op Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u informatie over het verzamelen van een prestatie basislijn als een reeks aanbevolen procedures en richt lijnen voor het optimaliseren van de prestaties van uw SQL Server op Azure Virtual Machines (Vm's).

Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Deze reeks best practices voor prestaties is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure virtual machines. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke aanbevolen optimalisatie nodig hebt. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.

## <a name="overview"></a>Overzicht

Voor een prescriptieve aanpak verzamelt u prestatie meter items met PerfMon/LogMan en Capture SQL Server wacht statistieken om de algemene belasting en mogelijke knel punten van de bron omgeving beter te begrijpen. 

Begin met het verzamelen van de CPU, het geheugen, de [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), de [door Voer](../../../virtual-machines/premium-storage-performance.md#throughput)en [latentie](../../../virtual-machines/premium-storage-performance.md#latency) van de bron-workload op piek tijden na de [controle lijst](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)voor de prestaties van de toepassing. 

Verzamel gegevens tijdens piek uren, zoals werk belastingen tijdens uw dagelijkse werk belasting, maar ook andere High Load-processen, zoals het beëindigen van de dagelijkse verwerking en weekend ETL-workloads. Overweeg om uw resources te verg Roten voor ongewoon veel werk belastingen, zoals het beëindigen van een kwart verwerking, en pas de schaal aan nadat de werk belasting is voltooid. 

Gebruik de analyse van prestaties om de [VM-grootte](../../../virtual-machines/sizes-memory.md) te selecteren die kan worden geschaald naar de prestatie vereisten van uw werk belasting.


## <a name="storage"></a>Storage

SQL Server prestaties zijn sterk afhankelijk van het I/O-subsysteem en de opslag prestaties worden gemeten door IOPS en door voer. Tenzij uw data base in het fysieke geheugen past, brengt SQL Server voortdurend database pagina's uit en uit de buffer groep. De gegevens bestanden voor SQL Server moeten anders worden behandeld. Toegang tot logboek bestanden is opeenvolgend, behalve wanneer een trans actie moet worden teruggedraaid wanneer gegevens bestanden, inclusief TempDB, wille keurig worden geopend. Als u een langzaam I/O-subsysteem hebt, kunnen uw gebruikers prestatie problemen ondervinden, zoals trage reactie tijden en taken die niet worden voltooid als gevolg van time-outs. 

De virtuele machines van Azure Marketplace hebben logboek bestanden op een fysieke schijf die standaard gescheiden is van de gegevens bestanden. Het aantal TempDB-gegevens bestanden en de grootte voldoen aan aanbevolen procedures en zijn gericht op het tijdelijke `D:\` station. 

De volgende prestatie meter items kunnen helpen bij het valideren van de i/o-door Voer die vereist is voor uw SQL Server: 
* **\LogicalDisk\Disk Lees bewerkingen per seconde** (IOPS lezen)
* **\LogicalDisk\Disk schrijf bewerkingen per seconde** (IOPS schrijven) 
* **\LogicalDisk\Disk Lees bewerkingen in bytes per seconde** (vereisten voor het lezen van de door Voer voor de gegevens, het logboek en tempdb-bestanden)
* **\LogicalDisk\Disk schrijf bewerkingen in bytes per seconde** (vereisten voor door Voer schrijven voor de gegevens, het logboek en tempdb-bestanden)

Met behulp van IOPS-en doorvoer vereisten op piek niveaus kunt u de VM-grootten evalueren die overeenkomen met de capaciteit van uw metingen. 

Als voor uw werk belasting 20.000-Lees-IOPS en 10K-schrijf-IOPS zijn vereist, kunt u E16s_v3 kiezen (met Maxi maal 32K in cache opgeslagen en 25600 niet-opgeslagen IOPS) of M16_s (met Maxi maal 20.000 in cache opgeslagen en niet-opgeslagen IOPS) met 2 P30 schijven die zijn gestripd met opslag ruimten. 

Zorg ervoor dat u zowel de door Voer als de IOPS-vereisten van de werk belasting begrijpt, aangezien Vm's verschillende schaal limieten hebben voor IOPS en door voer.

## <a name="memory"></a>Geheugen

Volg zowel het externe geheugen dat door het besturings systeem wordt gebruikt als het geheugen dat intern door SQL Server wordt gebruikt. Het identificeren van de belasting van een van beide onderdelen helpt bij het formaat van virtuele machines en het identificeren van kansen voor het afstemmen. 

De volgende prestatie meter items kunnen helpen bij het valideren van de geheugen status van een SQL Server virtuele machine: 
* [\Memory\Available mega bytes](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: geheugen Manager\Target server geheugen (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: geheugen Manager\Total server geheugen (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy schrijf bewerkingen per seconde](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Bufferbeheer\gelezen Life verwachting](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Compute in azure wordt anders beheerd dan on-premises. On-premises servers zijn voor het laatst enkele jaren gebouwd zonder een upgrade vanwege de overhead van het management en de kosten voor het verkrijgen van nieuwe hardware. Door virtualisatie worden enkele van deze problemen verholpen, maar toepassingen zijn geoptimaliseerd voor het meest voor deel van de onderliggende hardware, wat betekent dat een belang rijke wijziging in het Resource verbruik een herverdeling van de gehele fysieke omgeving vereist. 

Dit is geen uitdaging in azure waarbij een nieuwe virtuele machine in een andere reeks hardware en zelfs in een andere regio gemakkelijk te vinden is. 

In azure wilt u zo veel mogelijk gebruikmaken van de resources van de virtuele machines, daarom moeten virtuele machines van Azure zodanig worden geconfigureerd dat de gemiddelde CPU zo hoog mogelijk wordt, zonder dat dit van invloed is op de werk belasting. 

De volgende prestatie meter items kunnen helpen bij het valideren van de reken status van een SQL Server virtuele machine:
* **\Processor Information (_Total) \% processor tijd**
* **\Process (SQLSERVR) \% processor tijd**

> [!NOTE] 
> In het ideale geval kunt u zich richten op het gebruik van 80% van uw reken kracht, met pieken boven 90%, maar niet meer dan 100% gedurende langere tijd. Het is belang rijk dat u alleen de berekenings behoeften van de toepassing inricht en vervolgens wilt schalen of verlagen wanneer het bedrijf dit vereist. 


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie de andere artikelen in deze serie:
- [Snelle controle lijst](performance-guidelines-best-practices-checklist.md)
- [VM-grootte](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


Zie [beveiligings overwegingen voor SQL Server op Azure virtual machines](security-considerations-best-practices.md)voor aanbevolen procedures voor beveiliging.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).
