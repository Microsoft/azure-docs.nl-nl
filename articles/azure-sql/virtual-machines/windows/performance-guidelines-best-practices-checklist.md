---
title: 'Controle lijst: Aanbevolen procedures voor prestaties & richt lijnen'
description: Biedt een snelle controle lijst voor het controleren van uw aanbevolen procedures en richt lijnen voor het optimaliseren van de prestaties van uw SQL Server op Azure virtual machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572384"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Controle lijst: aanbevolen prestatie procedures voor het SQL Server op virtuele machines van Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dit artikel bevat een snelle controle lijst als een reeks aanbevolen procedures en richt lijnen voor het optimaliseren van de prestaties van uw SQL Server op Azure Virtual Machines (Vm's). 

Zie voor uitgebreide informatie de andere artikelen in deze serie: [VM-grootte](performance-guidelines-best-practices-vm-size.md), [opslag](performance-guidelines-best-practices-storage.md), [basis lijn verzamelen](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Overzicht

Wanneer u SQL Server uitvoert op Azure Virtual Machines, moet u dezelfde opties voor het afstemmen van de prestaties van de data base blijven gebruiken die van toepassing zijn op SQL Server in on-premises server omgevingen. De prestaties van een relationele data base in een open bare Cloud zijn echter afhankelijk van veel factoren, zoals de grootte van een virtuele machine en de configuratie van de gegevens schijven.

Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Deze reeks best practices voor prestaties is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure virtual machines. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke aanbevolen optimalisatie nodig hebt. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.

## <a name="vm-size"></a>VM-grootte

Hieronder vindt u een korte controle lijst met aanbevolen procedures voor het uitvoeren van uw SQL Server op Azure VM: 

- Gebruik VM-grootten met vier of meer vCPU zoals de [Standard_M8-4 MS](/../../virtual-machines/m-series), de [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)of de [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) of hoger. 
- De grootte van virtuele machines die zijn [geoptimaliseerd voor geheugen](../../../virtual-machines/sizes-memory.md) gebruiken voor de beste prestaties van SQL Server werk belastingen. 
- De [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) Series, de [M-](../../../virtual-machines/m-series.md)en de [Mv2-](../../../virtual-machines/mv2-series.md) series bieden de optimale geheugen-naar-vCore-verhouding die vereist is voor OLTP-workloads. Virtuele machines uit de M-serie bieden de hoogste geheugen-naar-vCore verhouding die is vereist voor essentiële workloads en die ook ideaal zijn voor Data Warehouse-workloads. 
- Overweeg een hogere geheugen-naar-vCore-verhouding voor bedrijfs kritieke en Data Warehouse-workloads. 
- Gebruik de installatie kopieën van de Azure virtual machine Marketplace als de SQL Server instellingen en opslag opties zijn geconfigureerd voor optimale SQL Server prestaties. 
- Verzamel de prestatie kenmerken van de doel-workload en gebruik deze om de juiste VM-grootte voor uw bedrijf te bepalen.

Zie voor meer informatie de uitgebreide [Aanbevolen procedures voor VM-grootte](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Storage

Hier volgt een korte controle lijst met aanbevolen procedures voor opslag configuratie voor het uitvoeren van uw SQL Server op Azure VM: 

- Bewaak de toepassing en [Bepaal de vereisten voor opslag bandbreedte en latentie](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) voor SQL Server gegevens, logboeken en tempdb-bestanden voordat u het schijf type kiest. 
- U kunt de prestaties van de opslag optimaliseren door te plannen voor de hoogst beschik bare IOPS in het cache geheugen en het gebruik van gegevens caching als prestatie functie voor gegevens Lees bewerkingen uit te stellen en te voor komen dat [virtuele machines en schijven worden gelimiteerd/beperkt](../../../virtual-machines/premium-storage-performance.md#throttling).
- Plaats gegevens, logboeken en tempdb-bestanden op afzonderlijke stations.
    - Gebruik voor het gegevens station alleen [Premium P30-en P40-schijven](../../../virtual-machines/disks-types.md#premium-ssd) om te zorgen voor de beschik baarheid van de cache ondersteuning
    - Voor het schema van het logboek station voor capaciteits-en test prestaties en kosten bij de evaluatie van de [Premium-P30-P80-schijven](../../../virtual-machines/disks-types.md#premium-ssd).
      - Als de opslag latentie voor submilliseconden vereist is, gebruikt u [Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) voor het transactie logboek. 
      - Voor implementaties van de virtuele machines uit de M-serie moet u [Write Accelerator](../../../virtual-machines/how-to-enable-write-accelerator.md) over het gebruik van Azure Ultra disks.
    - Plaats [tempdb](/sql/relational-databases/databases/tempdb-database) op het lokale tijdelijke SSD- `D:\` station voor de meeste SQL Server workloads nadat u de optimale VM-grootte hebt gekozen. 
      - Als de capaciteit van het lokale station niet voldoende is voor TempDB, kunt u de grootte van de virtuele machine aanpassen. Zie [cache beleidsregels voor gegevens bestanden](performance-guidelines-best-practices-storage.md#data-file-caching-policies) voor meer informatie.
- Bewaak meerdere Azure-gegevens schijven met [opslag ruimten](/windows-server/storage/storage-spaces/overview) om de I/O-band breedte te verhogen tot de IOPS-en doorvoer limieten van de doel-VM.
- Stel [host caching](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) in op alleen-lezen voor schijven van gegevens bestanden.
- Stel [host caching](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) in op geen voor de schijven van een logboek bestand.
    - Schakel cache voor lezen/schrijven niet in op schijven die SQL Server-bestanden bevatten. 
    - Stop de SQL Server-service altijd voordat u de cache-instellingen van de schijf wijzigt.
- Overweeg het gebruik van standaard opslag voor werk belastingen voor ontwikkelen en testen. Het is niet raadzaam om Standard-HDD-SDD te gebruiken voor werk belastingen voor de productie.
- [Op Credit gebaseerde schijf bursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) mag alleen worden overwogen voor kleinere werk belastingen voor ontwikkelen en testen en afdelings systemen.
- Richt het opslag account in in dezelfde regio als de SQL Server VM. 
- Schakel Azure geo-redundante opslag (geo-replicatie) uit en gebruik LRS (lokale redundante opslag) op het opslag account.
- Format teer uw gegevens schijf voor het gebruik van 64 KB Allocation Unit Size voor alle gegevens bestanden die worden geplaatst op een ander station dan het tijdelijke `D:\` station (met een standaard waarde van 4 KB). SQL Server Vm's die via Azure Marketplace worden geïmplementeerd, worden geleverd met gegevens schijven die zijn geformatteerd met Allocation Unit Size en Interleave voor de opslag groep ingesteld op 64 KB. 

Zie de uitgebreide [Aanbevolen procedures voor opslag](performance-guidelines-best-practices-storage.md)voor meer informatie. 


## <a name="azure--sql-feature-specific"></a>Azure & SQL-functie specifiek

Hieronder vindt u een korte controle lijst met aanbevolen procedures voor SQL Server en Azure-specifieke configuraties wanneer u uw SQL Server op Azure VM uitvoert: 

- Meld u aan bij de [SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md) om een aantal [functie voordelen](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)te ontgrendelen. 
- Schakel database pagina compressie in.
- Schakel de initialisatie van Instant file in voor gegevens bestanden.
- Beperk de automatische groei van de data base.
- Schakel autoshrink van de data base uit.
- Verplaats alle data bases naar gegevens schijven, inclusief systeem databases.
- Verplaats SQL Server fouten logboek en traceer bestands mappen naar gegevens schijven.
- Standaard back-up en database bestands locaties configureren.
- [Vergrendelde pagina's in het geheugen inschakelen](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Evalueer en pas de [meest recente cumulatieve updates](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) toe op de geïnstalleerde versie van SQL Server.
- Maak rechtstreeks een back-up naar Azure Blob-opslag.
- Gebruik meerdere [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) -bestanden, 1 bestand per kern, Maxi maal 8 bestanden.



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie de andere artikelen in deze serie:
- [VM-grootte](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Basis lijn verzamelen](performance-guidelines-best-practices-collect-baseline.md)

Zie [beveiligings overwegingen voor SQL Server op Azure virtual machines](security-considerations-best-practices.md)voor aanbevolen procedures voor beveiliging.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).
