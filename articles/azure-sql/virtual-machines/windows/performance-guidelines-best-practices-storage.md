---
title: 'Opslag: Aanbevolen procedures voor prestaties & richt lijnen'
description: Voorziet in Aanbevolen procedures en richt lijnen voor het optimaliseren van de prestaties van uw SQL Server op Azure virtual machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572415"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Opslag: aanbevolen prestatie procedures voor het SQL Server op virtuele machines in azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u aanbevolen procedures en richt lijnen voor het optimaliseren van de prestaties van uw SQL Server op Azure Virtual Machines (Vm's).

Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Deze reeks best practices voor prestaties is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure virtual machines. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke aanbevolen optimalisatie nodig hebt. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.

Zie voor meer informatie de andere artikelen in deze reeks: [prestatie checklist](performance-guidelines-best-practices-checklist.md), [VM-grootte](performance-guidelines-best-practices-vm-size.md)en [basis lijn verzamelen](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Controlelijst

Bekijk de volgende controle lijst voor een beknopt overzicht van de best practices voor opslag die in de rest van het artikel worden behandeld in meer detail: 

- Bewaak de toepassing en [Bepaal de vereisten voor opslag bandbreedte en latentie](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) voor SQL Server gegevens, logboeken en tempdb-bestanden voordat u het schijf type kiest. 
- U kunt de prestaties van de opslag optimaliseren door te plannen voor de hoogst beschik bare IOPS in de cache en het gebruik van gegevens caching als prestatie functie voor gegevens Lees bewerkingen, terwijl u voor komt dat [virtuele machines en schijven](../../../virtual-machines/premium-storage-performance.md#throttling)worden beperkt.
- Plaats gegevens, logboeken en tempdb-bestanden op afzonderlijke stations.
    - Gebruik voor het gegevens station alleen [Premium P30-en P40-schijven](../../../virtual-machines/disks-types.md#premium-ssd) om te zorgen voor de beschik baarheid van de cache ondersteuning
    - Voor het schema van het logboek station voor capaciteits-en test prestaties en kosten bij het evalueren van de [Premium P30-P80-schijven](../../../virtual-machines/disks-types.md#premium-ssd)
      - Als de opslag latentie voor submilliseconden vereist is, gebruikt u [Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) voor het transactie logboek. 
      - Voor implementaties van de virtuele machines uit de M-serie is het verstandig om via Azure Ultra disks een [Accelerator te schrijven](../../../virtual-machines/how-to-enable-write-accelerator.md) .
    - Plaats [tempdb](/sql/relational-databases/databases/tempdb-database) op het lokale tijdelijke SSD- `D:\` station voor de meeste SQL Server workloads nadat u de optimale VM-grootte hebt gekozen. 
      - Als de capaciteit van het lokale station niet voldoende is voor TempDB, kunt u de grootte van de virtuele machine aanpassen. Zie [cache beleidsregels voor gegevens bestanden](#data-file-caching-policies) voor meer informatie.
- Bewaak meerdere Azure-gegevens schijven met [opslag ruimten](/windows-server/storage/storage-spaces/overview) om de I/O-band breedte te verhogen tot de IOPS-en doorvoer limieten van de doel-VM.
- Stel [host caching](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) in op alleen-lezen voor schijven van gegevens bestanden.
- Stel [host caching](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) in op geen voor de schijven van een logboek bestand.
    - Schakel cache voor lezen/schrijven niet in op schijven die SQL Server-bestanden bevatten. 
    - Stop de SQL Server-service altijd voordat u de cache-instellingen van de schijf wijzigt.
- Voor ontwikkel-en test werkbelastingen en back-uparchivering op lange termijn kunt u gebruikmaken van standaard opslag. Het is niet raadzaam om Standard-HDD-SDD te gebruiken voor werk belastingen voor de productie.
- [Op Credit gebaseerde schijf bursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) mag alleen worden overwogen voor kleinere werk belastingen voor ontwikkelen en testen en afdelings systemen.
- Richt het opslag account in in dezelfde regio als de SQL Server VM. 
- Schakel Azure geo-redundante opslag (geo-replicatie) uit en gebruik LRS (lokale redundante opslag) op het opslag account.
- Format teer uw gegevens schijf voor het gebruik van 64 KB Allocation Unit Size voor alle gegevens bestanden die worden geplaatst op een ander station dan het tijdelijke `D:\` station (met een standaard waarde van 4 KB). SQL Server Vm's die via Azure Marketplace worden geïmplementeerd, worden geleverd met gegevens schijven die zijn geformatteerd met Allocation Unit Size en Interleave voor de opslag groep ingesteld op 64 KB. 

Als u de controle lijst voor opslag met anderen wilt vergelijken, raadpleegt u de [controle lijst uitgebreide prestaties best practices](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Overzicht

Begin met [het meten van de opslag prestaties van uw zakelijke toepassing](performance-guidelines-best-practices-collect-baseline.md#storage)om de meest efficiënte configuratie voor SQL Server workloads op een virtuele Azure-machine te vinden. Zodra de opslag vereisten bekend zijn, selecteert u een virtuele machine die de benodigde IOPS en door Voer ondersteunt met de juiste verhouding van geheugen-naar-vCore. 

Kies een VM-grootte met voldoende opslag schaal baarheid voor uw werk belasting en een combi natie van schijven (meestal in een opslag groep) die voldoen aan de capaciteits-en prestatie vereisten van uw bedrijf. 

Het type schijf is afhankelijk van het bestands type dat wordt gehost op de schijf en de prestaties van uw piek vereisten.

> [!TIP]
> Het inrichten van een SQL Server-VM via de Azure Portal helpt u bij het proces van de opslag configuratie en implementeert de meeste best practices voor opslag, zoals het maken van afzonderlijke opslag groepen voor uw gegevens en logboek bestanden, het richten van tempdb op het `D:\` station en het inschakelen van het optimale cache beleid. Zie [SQL VM-opslag configuratie](storage-configuration.md)voor meer informatie over het inrichten en configureren van opslag. 

## <a name="vm-disk-types"></a>Typen VM-schijven

U kunt kiezen voor het prestatie niveau van uw schijven. De typen beheerde schijven die beschikbaar zijn als onderliggende opslag (aangegeven door de toenemende prestatie mogelijkheden) zijn standaard vasteschijfstations (HDD), standaard Ssd's, Premium Solid-state drives (SSD) en ultra disks. 

De prestaties van de schijf nemen toe met de capaciteit, gegroepeerd op [Premium-schijf etiketten](../../../virtual-machines/disks-types.md#premium-ssd) , zoals de P1 met 4 GiB ruimte en 120 IOPS tot de P80 met 32 TIB van opslag en 20.000 IOPS. Premium Storage ondersteunt een opslag cache waarmee de lees-en schrijf prestaties voor sommige werk belastingen worden verbeterd. Zie [Managed disks Overview](../../../virtual-machines/managed-disks-overview.md)(Engelstalig) voor meer informatie. 

Er zijn ook drie belangrijkste [schijf typen](../../../virtual-machines/managed-disks-overview.md#disk-roles) om te overwegen voor uw SQL Server op Azure VM: een besturingssysteem schijf, een tijdelijke schijf en uw gegevens schijven. Bepaal zorgvuldig wat wordt opgeslagen op het station van het besturings systeem `(C:\)` en op de tijdelijke schijf `(D:\)` . 

### <a name="operating-system-disk"></a>Besturingssysteemschijf

Een besturingssysteem schijf is een VHD die kan worden opgestart en gekoppeld als een actieve versie van een besturings systeem en wordt aangeduid als het `C:\` station. Wanneer u een virtuele Azure-machine maakt, voegt het platform ten minste één schijf aan de virtuele machine toe voor de besturingssysteem schijf. Het `C:\` station is de standaard locatie voor de installatie van toepassingen en de bestands configuratie. 

Voor productie SQL Server omgevingen gebruikt u de besturingssysteem schijf niet voor gegevens bestanden, logboek bestanden, fout Logboeken. 

### <a name="temporary-disk"></a>Tijdelijke schijf

Veel virtuele machines van Azure bevatten een ander schijf type, de tijdelijke schijf (aangeduid als het `D:\` station). Afhankelijk van de serie van de virtuele machine en de grootte van de capaciteit van deze schijf kan variëren. De tijdelijke schijf is tijdelijk, wat betekent dat de schijf opslag opnieuw wordt gemaakt (zoals in, de toewijzing ervan wordt opgeheven en opnieuw wordt toegewezen), wanneer de virtuele machine opnieuw wordt opgestart of is verplaatst naar een andere host (bijvoorbeeld voor [service Retoucheer](/troubleshoot/azure/virtual-machines/understand-vm-reboot)). 

Het tijdelijke opslag station is niet permanent voor externe opslag en daarom moeten gebruikers database bestanden, transactie logboek bestanden of iets dat moet worden bewaard, niet worden opgeslagen. 

Plaats TempDB op het lokale tijdelijke SSD- `D:\` station voor SQL Server workloads, tenzij het gebruik van een lokale cache een probleem is. Als u een virtuele machine gebruikt die [geen tijdelijke schijf](../../../virtual-machines/azure-vms-no-temp-disk.md) heeft, wordt u aangeraden TempDB op een eigen geïsoleerde schijf of opslag groep op te slaan met caching ingesteld op alleen-lezen. Zie beleid voor TempDB- [gegevens cache](performance-guidelines-best-practices-storage.md#data-file-caching-policies)voor meer informatie.

### <a name="data-disks"></a>Gegevensschijven

Gegevens schijven zijn externe opslag schijven die vaak in [opslag groepen](/windows-server/storage/storage-spaces/overview) worden gemaakt om de capaciteit en prestaties te overschrijden die een enkele schijf aan de virtuele machine kan aanbieden.

Koppel het minimale aantal schijven dat voldoet aan de vereisten voor IOPS, door Voer en capaciteit van uw workload. Het maximum aantal gegevens schijven van de kleinste virtuele machine die u wilt wijzigen, mag niet worden overschreden.

Plaats gegevens-en logboek bestanden op gegevens schijven die zijn ingericht voor de beste prestaties van de prestatie vereisten. 

Format teer uw gegevens schijf voor het gebruik van 64 KB Allocation Unit Size voor alle gegevens bestanden die worden geplaatst op een ander station dan het tijdelijke `D:\` station (met een standaard waarde van 4 KB). SQL Server Vm's die via Azure Marketplace worden geïmplementeerd, worden geleverd met gegevens schijven die zijn geformatteerd met Allocation Unit Size en Interleave voor de opslag groep ingesteld op 64 KB. 

## <a name="premium-disks"></a>Premium-schijven

Gebruik Premium SSD-schijven voor gegevens-en logboek bestanden voor productie SQL Server workloads. Premium-SSD IOPS en band breedte variëren afhankelijk van de [grootte en het type](../../../virtual-machines/disks-types.md)van de schijf. 

Voor werk belastingen van de productie gebruikt u de P30-en/of P40-schijven voor SQL Server gegevens bestanden om ervoor te zorgen dat caching wordt ondersteund en de P30 Maxi maal P80 gebruiken voor SQL Server transactie logboek bestanden.  Voor de beste total cost of ownership begint u met P30s (5000 IOPS/200 MBPS) voor gegevens-en logboek bestanden en kiest u alleen hogere capaciteiten wanneer u het aantal schijven van de virtuele machine wilt beheren.

Voor OLTP-workloads komt u overeen met de doel-IOPS per schijf (of opslag groep) met uw prestatie vereisten met behulp van werk belastingen op piek tijden en de `Disk Reads/sec`  +  `Disk Writes/sec` prestatie meter items. Voor Data Warehouse-en rapportage werk belastingen, overeenkomen met de doel doorvoer met behulp van werk belastingen op piek tijden en de `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Gebruik opslag ruimten om optimale prestaties te krijgen, twee groepen te configureren, één voor de logboek bestanden en de andere voor de gegevens bestanden. Als u schijf striping niet gebruikt, gebruikt u twee Premium SSD-schijven die zijn toegewezen aan afzonderlijke stations, waarbij één station het logboek bestand bevat en de andere de gegevens bevat.

De [ingerichte IOPS en door Voer](../../../virtual-machines/disks-types.md#premium-ssd) per schijf die wordt gebruikt als onderdeel van uw opslag groep. De gecombineerde IOPS-en doorvoer mogelijkheden van de schijven zijn de maximale capaciteit tot de doorvoer limieten van de virtuele machine.

De best practice is het gebruik van het minste aantal schijven dat mogelijk is tijdens het voldoen aan de minimale vereisten voor IOPS (en door Voer) en capaciteit. De prijs-en prestatie verhouding is echter beter met een groot aantal kleine schijven in plaats van een klein aantal grote schijven.

### <a name="scaling-premium-disks"></a>Premium-schijven schalen

Wanneer een Azure Managed disk voor het eerst wordt geïmplementeerd, is de prestatie tier voor die schijf gebaseerd op de ingerichte schijf grootte. Wijs de prestatie-laag aan tijdens de implementatie of wijzig deze daarna zonder de grootte van de schijf te wijzigen. Als de vraag toeneemt, kunt u het prestatie niveau verhogen om te voldoen aan de behoeften van uw bedrijf. 

Door de prestatie-laag te wijzigen, kunnen beheerders voorbereiden op en voldoen aan de hogere vraag zonder [schijf bursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting)te gebruiken. 

Gebruik zo lang de hogere prestaties, waarbij facturering is ontworpen om te voldoen aan de prestatie-laag van de opslag. Voer een upgrade uit voor de laag zodat deze overeenkomt met de prestatie vereisten zonder de capaciteit te verhogen. Ga terug naar de oorspronkelijke laag wanneer de extra prestaties niet meer nodig zijn.

Deze kosten efficiënte en tijdelijke uitbrei ding van prestaties is een sterk gebruiks voorbeeld voor gerichte gebeurtenissen, zoals winkelen, testen van prestaties, trainings gebeurtenissen en andere korte Vensters, waarbij betere prestaties alleen voor een korte periode nodig zijn. 

Zie [prestatie lagen voor Managed disks](../../../virtual-machines/disks-change-performance.md)voor meer informatie. 

## <a name="azure-ultra-disk"></a>Azure Ultra Disk

Als er sprake is van een reactie tijd van submilliseconden met beperkte latentie, kunt u gebruikmaken van [Azure Ultra Disk](../../../virtual-machines/disks-types.md#ultra-disk) voor het SQL Server-logboek station, of zelfs het gegevens station voor toepassingen die zeer gevoelig zijn voor I/O-latentie. 

Ultra disk kan worden geconfigureerd waar capaciteit en IOPS onafhankelijk kunnen worden geschaald. Met uiterst schijf beheerders kan een schijf met de vereisten voor capaciteit, IOPS en door voer worden ingericht op basis van de behoeften van de toepassing. 

Ultra disk wordt niet ondersteund op alle VM-reeksen en heeft andere beperkingen, zoals de beschik baarheid van regio's, redundantie en ondersteuning voor Azure Backup. Zie voor meer informatie over het [gebruik van Azure Ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md) voor een volledige lijst met beperkingen. 

## <a name="standard-hdds-and-ssds"></a>Standaard Hdd's en Ssd's

[Standaard hdd's](../../../virtual-machines/disks-types.md#standard-hdd) en ssd's hebben verschillende latenties en band breedte en worden alleen aanbevolen voor dev/test-workloads. Werk belastingen voor productie moeten premium-Ssd's gebruiken. Als u Standard-SSD (dev/test-scenario's) gebruikt, is het raadzaam om het maximum aantal gegevens schijven toe te voegen dat wordt ondersteund door de [VM-grootte](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) en schijf striping met opslag ruimten te gebruiken voor de beste prestaties.

## <a name="caching"></a>Caching

Virtuele machines die ondersteuning bieden voor Premium Storage-caching, kunnen profiteren van een extra functie die de Azure BlobCache of host-caching wordt genoemd om de IOPS-en doorvoer mogelijkheden van een virtuele machine uit te breiden. Voor virtuele machines die zijn ingeschakeld voor Premium Storage en Premium Storage caching, zijn deze twee verschillende limieten voor opslag bandbreedte die samen kunnen worden gebruikt om de opslag prestaties te verbeteren.

De door Voer van IOPS en MBps zonder cache geheugen telt op basis van de doorvoer limieten voor de schijf van een virtuele machine. De maximale cache limieten bieden een extra buffer voor lees bewerkingen waarmee u de groei en onverwachte pieken kunt aanpakken.

Schakel Premium-caching in wanneer de optie wordt ondersteund om de prestaties voor lees bewerkingen aanzienlijk te verbeteren zonder extra kosten. 

Lees-en schrijf bewerkingen naar de Azure-BlobCache (in cache opgeslagen IOPS en door Voer) worden niet meegeteld op basis van de niet-gecachede IOPS en doorvoer limieten van de virtuele machine.

> [!NOTE]
> Schijf cache gebruik wordt niet ondersteund voor schijven van 4 TiB en groter (P50 en groter). Als er meerdere schijven aan uw virtuele machine zijn gekoppeld, ondersteunt elke schijf die kleiner is dan 4 TiB de cache. Zie [schijf cache](../../../virtual-machines/premium-storage-performance.md#disk-caching)voor meer informatie. 

### <a name="uncached-throughput"></a>Door Voer niet in cache

Het maximale aantal IOPS en door Voer in de cache is de maximale limiet voor externe opslag die de virtuele machine kan verwerken. Deze limiet wordt gedefinieerd op de virtuele machine en is geen limiet voor de onderliggende schijf opslag. Deze limiet geldt alleen voor I/O op gegevens stations die op afstand zijn gekoppeld aan de virtuele machine, niet op de lokale I/O voor het tijdelijke station ( `D:\` station) of het station van het besturings systeem.

De hoeveelheid niet-gecachede IOPS en door Voer die beschikbaar is voor een virtuele machine kan worden gecontroleerd in de documentatie voor uw virtuele machines.

In de documentatie uit de [M-serie](../../../virtual-machines/m-series.md) ziet u bijvoorbeeld dat de maximale ongecachede door Voer voor de Standard_M8ms VM 5000 IOPS is en 125 Mbps van een niet in cache opgeslagen schijf doorvoer. 

![Scherm afbeelding van de niet in cache opgeslagen schijf doorvoer documentatie van de M-serie.](./media/performance-guidelines-best-practices/m-series-table.png)

Op dezelfde manier kunt u zien dat de Standard_M32ts ondersteuning biedt voor 20.000 niet-in cache opgeslagen schijf-IOPS en 500 MBps schijf doorvoer niet in cache. Deze limiet is van toepassing op het niveau van de virtuele machine, ongeacht de onderliggende Premium-schijf opslag.

Zie niet- [opgeslagen en in cache opgeslagen limieten](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)voor meer informatie.


### <a name="cached-and-temp-storage-throughput"></a>Door Voer in cache en tijdelijke opslag

De maximale limiet voor opslag doorvoer in cache en tijdelijk is een afzonderlijke limiet van de maximale doorvoer limiet voor de virtuele machine. De Azure-BlobCache bestaat uit een combi natie van het geheugen wille keurig toegang van de host van de virtuele machine en de lokaal gekoppelde SSD. Het tijdelijke station ( `D:\` station) binnen de virtuele machine wordt ook gehost op deze lokale SSD.

De maximale limiet voor de opslag doorvoer in cache en tijdelijk geldt voor de I/O ten opzichte van de lokale tijdelijke schijf ( `D:\` station) en de Azure-BlobCache **als** host caching is ingeschakeld. 

Wanneer caching is ingeschakeld voor Premium Storage, kunnen virtuele machines de limieten overschrijden van de externe opslag die niet in de cache is opgeslagen voor de virtuele machine en door voer.  

Alleen bepaalde virtuele machines ondersteunen zowel Premium Storage als Premium Storage caching (die moeten worden geverifieerd in de documentatie van de virtuele machine). De documentatie uit de [M-serie](../../../virtual-machines/m-series.md) geeft bijvoorbeeld aan dat zowel Premium Storage als Premium Storage-caching wordt ondersteund: 

![Scherm opname van Premium Storage ondersteuning voor de M-serie.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

De limieten van de cache variëren, afhankelijk van de grootte van de virtuele machine. De Standard_M8ms virtuele machine ondersteunt bijvoorbeeld 10000 schijf met cache schijven en 1000 MBps schijf doorvoer in cache met een totale cache grootte van 793 GiB. Op dezelfde manier ondersteunt de Standard_M32ts VM 40000 in cache opgeslagen schijf-IOPS en 400 MBps schijf doorvoer in cache met een totale cache grootte van 3174 GiB. 

![Scherm opname van de schijf doorvoer documentatie van de M-serie in de cache.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

U kunt host-caching hand matig inschakelen op een bestaande virtuele machine. Stop alle werk belastingen van toepassingen en de SQL Server Services voordat er wijzigingen in het cache beleid van uw virtuele machine worden aangebracht. Als u de cache-instellingen van de virtuele machine wijzigt, wordt de doel schijf losgekoppeld en opnieuw gekoppeld nadat de instellingen zijn toegepast.

### <a name="data-file-caching-policies"></a>Beleids regels voor het opslaan van gegevens bestanden

Het beleid voor opslag cache is afhankelijk van het type SQL Server gegevens bestanden die worden gehost op het station. 

De volgende tabel bevat een samen vatting van de aanbevolen cache beleidsregels op basis van het type SQL Server gegevens: 

|SQL Server schijf |Aanbeveling |
|---------|---------|
| **Gegevensschijf** | Schakel `Read-only` caching in voor de schijven die SQL Server gegevens bestanden hosten. <br/> Lees bewerkingen van de cache zijn sneller dan de Lees bewerkingen van de gegevens schijf die niet in de cache zijn opgeslagen. <br/> Niet-gecachede IOPS en door Voer, inclusief IOPS en door Voer in cache resulteert in de totale mogelijke prestaties van de virtuele machine binnen de Vm's limieten, maar de werkelijke prestaties variëren op basis van de mogelijkheid van de werk belasting voor het gebruik van de cache (cache treffers). <br/>|
|**Transactie logboek schijf**|Stel het cache beleid in op `None` schijven die als host optreden voor het transactie logboek.  Het is niet mogelijk om caching in te scha kelen voor de transactie logboek schijf en als u `Read-only` of `Read/Write` caching is ingeschakeld op het logboek station, de prestaties van de schrijf bewerkingen op het station kunnen afnemen en de beschik bare hoeveelheid cache voor lees bewerkingen op het gegevens station te verlagen.  |
|**Besturingssysteem schijf** | Het standaard beleid voor opslaan in cache kan `Read-only` of `Read/write` voor het station van het besturings systeem. <br/> Het is niet raadzaam om het cache niveau van het station voor het besturings systeem te wijzigen.  |
| **tempdb**| Als TempDB niet kan worden geplaatst op het tijdelijke station `D:\` vanwege capaciteits redenen, wijzigt u de grootte van de virtuele machine in een grotere tijdelijke schijf of plaatst u TempDB op een afzonderlijk gegevens station waarvoor de `Read-only` cache is geconfigureerd. <br/> De cache van de virtuele machine en het tijdelijke station gebruiken beide de lokale SSD, dus houd er dan rekening mee wanneer de grootte van tempdb-I/O wordt berekend op basis van de limieten voor het aantal IOPS en de door Voer van de virtuele machine bij het hosten van het tijdelijke station.| 
| | | 


Zie [schijf cache](../../../virtual-machines/premium-storage-performance.md#disk-caching)voor meer informatie. 


## <a name="disk-striping"></a>Schijf striping

Analyseer de door Voer en de band breedte die is vereist voor uw SQL-gegevens bestanden om het aantal gegevens schijven, inclusief het logboek bestand en tempdb, te bepalen. Limieten voor door Voer en band breedte variëren per VM-grootte. Zie [VM-grootte](../../../virtual-machines/sizes.md) voor meer informatie.

Voeg extra gegevens schijven toe en gebruik schijf striping voor meer door voer. Een toepassing die bijvoorbeeld 12.000 IOPS en 180 MB/s-door Voer heeft, kan drie striped P30 schijven gebruiken om 15.000 IOPS en 600 MB/s aan te bieden. 

Zie [schijf striping](storage-configuration.md#disk-striping)voor meer informatie over het configureren van schijf striping. 

## <a name="disk-capping"></a>Schijf limiet 

Er zijn doorvoer limieten op het niveau van de schijf en de virtuele machine. De maximale IOPS-limieten per VM en per schijf verschillen en zijn onafhankelijk van elkaar.

Toepassingen die resources buiten deze limieten gebruiken, worden beperkt (ook wel ' begrenzing ' genoemd). Selecteer een virtuele machine en schijf grootte in een schijf striping die voldoet aan de vereisten van de toepassing en geen beperkingen opdoen. Als u wilt afgetopten, gebruik dan caching of stemt u de toepassing af, zodat er minder door Voer is vereist.

Een toepassing die bijvoorbeeld 12.000 IOPS en 180 MB/s nodig heeft, kan: 
- Gebruik de [Standard_M32ms](../../../virtual-machines/m-series.md) met een maximale door Voer van de schijf in de cache van 20.000 IOPS en 500 Mbps.
- Bewaak drie P30 schijven om 15.000 IOPS en 600 MB/s door voer te leveren.
- Gebruik een [Standard_M16ms](../../../virtual-machines/m-series.md) virtuele machine en gebruik de host-caching om lokale cache te gebruiken voor gebruik door voer. 

Virtuele machines die zijn geconfigureerd om te worden opgeschaald tijdens het hoge gebruik, moeten opslag inrichten met voldoende IOPS en door Voer ter ondersteuning van de maximale VM-grootte, terwijl het totale aantal schijven kleiner is dan of gelijk is aan het maximum aantal dat wordt ondersteund door de kleinste VM-SKU die is bedoeld om te worden gebruikt.

Zie [schijf-i/o](../../../virtual-machines/disks-performance.md)-beperking voor meer informatie over de limieten voor schijf limieten en het gebruik van caching om te vermijden.

> [!NOTE] 
> Sommige schijf limieten kunnen nog steeds leiden tot goede prestaties voor gebruikers. u kunt werk belastingen afstemmen en beheren in plaats van het formaat te wijzigen in een grotere virtuele machine om de kosten en prestaties voor het bedrijf te verdelen. 


## <a name="write-acceleration"></a>Schrijf versnelling

Write Acceleration is een schijf functie die alleen beschikbaar is voor de [M-serie](https://docs.microsoft.com/azure/virtual-machines/m-series) virtual machines (vm's). Het doel van schrijf versnelling is om de I/O-latentie van schrijf bewerkingen te verbeteren voor Azure Premium Storage als u een I/O-latentie van één cijfer nodig hebt als gevolg van hoge betrouw bare OLTP-workloads of Data Warehouse omgevingen. 

Gebruik schrijf versnelling om de schrijf latentie te verbeteren voor het station dat als host fungeert voor de logboek bestanden. Gebruik geen schrijf versnelling voor SQL Server gegevens bestanden. 

Write Accelerator-schijven delen dezelfde IOPS-limiet als de virtuele machine. Gekoppelde schijven kunnen niet groter zijn dan de limiet van Write Accelerator IOPS voor een virtuele machine.  

De volgende tabel geeft een overzicht van het aantal gegevens schijven en IOPS dat per virtuele machine wordt ondersteund: 

| VM-SKU  | Aantal Write Accelerator schijven  | Write Accelerator schijf-IOPS per VM  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20.000  |
| M128ms, M128s  | 16  | 20.000  |
| M208ms_v2, M208s_v2  | 8  | 10.000  |
| M64ms, M64ls, M64s  |  8 | 10.000 |
| M32 MS, M32ls, M32ts, M32s  | 4  | 5000  |
| M16 MS, M16s  | 2 | 2500 |
| M8 MS, M8s  | 1 | 1250 |

Er zijn een aantal beperkingen bij het gebruik van schrijf versnelling. Zie [beperkingen bij het gebruik van write Accelerator](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)voor meer informatie.


### <a name="comparing-to-azure-ultra-disk"></a>Vergelijken met Azure Ultra Disk

Het grootste verschil tussen schrijf versnelling en Azure Ultra disks is dat schrijf versnelling een virtuele-machine functie is die alleen beschikbaar is voor de M-serie en Azure Ultra disks is een opslag optie. Write Acceleration is een cache met schrijf optimalisatie met eigen beperkingen op basis van de grootte van de virtuele machine. Azure Ultra disks is een schijf opslag optie met lage latentie voor Azure Virtual Machines. 

Gebruik, indien mogelijk, schrijf versnelling over Ultra schijven voor de transactie logboek schijf. Gebruik Azure Ultra disks voor virtuele machines die geen schrijf versnelling ondersteunen maar een lage latentie vereisen voor het transactie logboek. 

## <a name="monitor-storage-performance"></a>Opslag prestaties bewaken

Als u de opslag behoeften wilt beoordelen en wilt bepalen hoe goed de opslag wordt uitgevoerd, moet u weten wat er moet worden gemeten en wat deze indica toren betekenen. 

[IOPS (invoer/uitvoer per seconde)](../../../virtual-machines/premium-storage-performance.md#iops) is het aantal aanvragen dat de toepassing per seconde maakt voor opslag. IOPS meten met prestatie meter items `Disk Reads/sec` en `Disk Writes/sec` . Toepassingen van [OLTP (online Trans Action processing)](/azure/architecture/data-guide/relational-data/online-transaction-processing) moeten hogere IOPS-schijven best rijken om optimale prestaties te kunnen garanderen. Toepassingen zoals systemen voor betalings verwerking, online winkelen en verkooppunt systemen zijn alle voor beelden van OLTP-toepassingen.

[Door Voer](../../../virtual-machines/premium-storage-performance.md#throughput) is het volume van de gegevens die worden verzonden naar de onderliggende opslag, vaak gemeten door mega bytes per seconde. Meting door Voer met de prestatie meter items `Disk Read Bytes/sec` en `Disk Write Bytes/sec` . [Gegevens opslag](/azure/architecture/data-guide/relational-data/data-warehousing) is geoptimaliseerd rond de maximale door Voer voor IOPS. Toepassingen zoals gegevens archieven voor analyse, rapportage, ETL workstreams en andere business intelligence doelen zijn alle voor beelden van toepassingen voor gegevens opslag.

De grootte van i/O-eenheden beïnvloedt de IOPS-en doorvoer mogelijkheden als kleinere I/O-grootten, waarbij hogere IOPS en grotere I/O-grootten hogere door Voer leveren. SQL Server kiest automatisch de optimale I/O-grootte. Zie voor meer informatie over [het optimaliseren van IOPS, door Voer en latentie voor uw toepassingen](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Er zijn specifieke Azure Monitor metrische gegevens die niet waardevol zijn voor het detecteren van een afgetopting van de virtuele machine en het schijf niveau, evenals het verbruik en de status van de AzureBlob-cache. Zie [metrische gegevens over opslag gebruik](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics)als u belang rijke prestatie meter items wilt identificeren om toe te voegen aan uw bewakings oplossing en Azure Portal dash board. 

> [!NOTE]
> Azure Monitor biedt momenteel geen metrische gegevens op schijf niveau voor het tijdelijke tijdelijke station `(D:\)` . In het cache geheugen geconsumeerde percentages van de VM en de in de cache opgeslagen band breedte van de VM worden IOPS en door Voer van zowel het tijdelijke tijdelijke station als de host-caching weer gegeven `(D:\)` .


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over best practices voor prestaties de andere artikelen in deze serie:
- [Snelle controle lijst](performance-guidelines-best-practices-checklist.md)
- [VM-grootte](performance-guidelines-best-practices-vm-size.md)
- [Basis lijn verzamelen](performance-guidelines-best-practices-collect-baseline.md)

Zie [beveiligings overwegingen voor SQL Server op Azure virtual machines](security-considerations-best-practices.md)voor aanbevolen procedures voor beveiliging.

Raadpleeg de blog [optimalisatie voor OLTP-prestaties](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)voor gedetailleerde tests van SQL Server prestaties op virtuele Azure-machines met TPC-E en TPC_C-benchmarks.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).
