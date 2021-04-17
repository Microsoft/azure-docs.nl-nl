---
title: 'Opslag: Best practices voor prestaties & richtlijnen'
description: Hier vindt u best practices en richtlijnen voor opslag om de prestaties van uw SQL Server virtuele Azure-machine (VM) te optimaliseren.
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
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389720"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Opslag: Best practices voor prestaties voor SQL Server azure-VM's
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dit artikel bevat de best practices en richtlijnen voor opslag om de prestaties voor uw SQL Server op Azure Virtual Machines (VM's) te optimaliseren.

Er is doorgaans een afweging tussen optimaliseren voor kosten en optimaliseren voor prestaties. Deze reeks best practices voor prestaties is gericht op het verkrijgen van de *beste* prestaties voor SQL Server in Azure Virtual Machines. Als uw workload minder veeleisende is, hebt u mogelijk niet elke aanbevolen optimalisatie nodig. Houd rekening met uw prestatiebehoeften, kosten en workloadpatronen wanneer u deze aanbevelingen evalueert.

Zie de andere artikelen in deze [reeks:](performance-guidelines-best-practices-checklist.md)Controlelijst voor prestaties, [VM-grootte](performance-guidelines-best-practices-vm-size.md)en [Basislijn verzamelen voor meer informatie.](performance-guidelines-best-practices-collect-baseline.md) 

## <a name="checklist"></a>Controlelijst

Bekijk de volgende controlelijst voor een kort overzicht van de best practices voor opslag die in de rest van het artikel gedetailleerder worden behandeld: 

- Controleer de toepassing en bepaal de vereisten voor opslagbandbreedte en [-latentie](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) voor SQL Server gegevens-, logboek- en tempdb-bestanden voordat u het schijftype kiest. 
- Als u de opslagprestaties wilt optimaliseren, moet u de hoogste niet-gecachede IOPS plannen en gegevenscacheding gebruiken als prestatiefunctie voor het lezen van gegevens, terwijl de limiet voor virtuele machines en [schijven wordt vermeden.](../../../virtual-machines/premium-storage-performance.md#throttling)
- Plaats gegevens-, logboek- en tempdb-bestanden op afzonderlijke stations.
    - Gebruik voor het gegevensstation alleen [Premium P30- en P40-schijven](../../../virtual-machines/disks-types.md#premium-ssd) om de beschikbaarheid van cacheondersteuning te garanderen
    - Voor het logboekstationplan voor capaciteit en testprestaties versus kosten tijdens het evalueren van de [Premium P30 - P80-schijven](../../../virtual-machines/disks-types.md#premium-ssd)
      - Als opslaglatentie van minder dan een seconde is vereist, gebruikt u [Azure Ultra Disks](../../../virtual-machines/disks-types.md#ultra-disk) voor het transactielogboek. 
      - Voor implementaties van virtuele machines uit de M-serie kunt u gebruikmaken van [write accelerator](../../../virtual-machines/how-to-enable-write-accelerator.md) over het gebruik van Azure Ultra Disks.
    - Plaats [tempdb op](/sql/relational-databases/databases/tempdb-database) het lokale tijdelijke SSD-station voor de meeste SQL Server `D:\` werkbelastingen na het kiezen van de optimale VM-grootte. 
      - Als de capaciteit van het lokale station niet voldoende is voor tempdb, kunt u overwegen om de VM te vergroten. Zie [Beleidsregels voor gegevensbestands caching](#data-file-caching-policies) voor meer informatie.
- Stripe meerdere Azure-gegevensschijven met [opslagruimten](/windows-server/storage/storage-spaces/overview) om de I/O-bandbreedte te verhogen tot aan de IOPS- en doorvoerlimieten van de doel-virtuele machine.
- Stel [host caching in](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) op alleen-lezen voor gegevensbestandsschijven.
- Stel [host caching in](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) op geen voor logboekbestandsschijven.
    - Lees-/schrijf caching niet inschakelen op schijven die SQL Server bevatten. 
    - Stop altijd de SQL Server-service voordat u de cache-instellingen van uw schijf verandert.
- Voor ontwikkel- en testworkloads en back-uparchivering op de lange termijn kunt u standaardopslag gebruiken. Het wordt afgeraden om een Standard - HDD/SDD te gebruiken voor productieworkloads.
- [Op tegoed gebaseerde schijf bursting](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) moet alleen worden overwogen voor kleinere werkbelastingen voor ontwikkelen/testen en afdelingssystemen.
- Het opslagaccount inrichten in dezelfde regio als de SQL Server VM. 
- Schakel geografisch redundante Azure-opslag (geo-replicatie) uit en gebruik LRS (lokaal redundante opslag) in het opslagaccount.
- Maak uw gegevensschijf zo op dat deze een blokgrootte van 64 kB (grootte van toewijzingseenheid) gebruikt voor alle gegevensbestanden die op een ander station dan het tijdelijke station zijn geplaatst (met een standaardgrootte van `D:\` 4 kB). SQL Server VM's die zijn geïmplementeerd via Azure Marketplace worden er gegevensschijven met een blokgrootte en interleave voor de opslaggroep op 64 kB ingesteld. 

Zie de uitgebreide controlelijst voor best practices voor prestaties als u de controlelijst voor opslag met de andere [wilt vergelijken.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Overzicht

Als u de meest efficiënte configuratie voor SQL Server workloads op een Azure-VM wilt vinden, begint u met het meten van de [opslagprestaties van uw zakelijke toepassing.](performance-guidelines-best-practices-collect-baseline.md#storage) Zodra de opslagvereisten bekend zijn, selecteert u een virtuele machine die ondersteuning biedt voor de benodigde IOPS en doorvoer met de juiste geheugen-naar-vCore-verhouding. 

Kies een VM-grootte met voldoende schaalbaarheid voor opslag voor uw workload en een combinatie van schijven (meestal in een opslaggroep) die voldoen aan de capaciteits- en prestatievereisten van uw bedrijf. 

Het type schijf is afhankelijk van zowel het bestandstype dat op de schijf wordt gehost als de prestatievereisten voor pieken.

> [!TIP]
> Het inrichten van een SQL Server-VM via de Azure Portal helpt u bij het opslagconfiguratieproces en implementeert de meeste best practices voor opslag, zoals het maken van afzonderlijke opslaggroepen voor uw gegevens en logboekbestanden, het richten van tempdb op het station en het inschakelen van het optimale `D:\` cachingbeleid. Zie SQL VM-opslagconfiguratie voor meer informatie over het inrichten en configureren [van opslag.](storage-configuration.md) 

## <a name="vm-disk-types"></a>Typen VM-schijven

U kunt kiezen uit het prestatieniveau voor uw schijven. De typen beheerde schijven die beschikbaar zijn als onderliggende opslag (vermeld door de prestatiemogelijkheden te verhogen) zijn Standaard harde schijven (HDD), Standard SSD's, Premium SOLID-State Drives (SSD) en ultraschijven. 

De prestaties van de schijf nemen toe [](../../../virtual-machines/disks-types.md#premium-ssd) met de capaciteit, gegroepeerd op Premium-schijflabels, zoals de P1 met 4 GiB aan ruimte en 120 IOPS naar de P80 met 32 TiB aan opslag en 20.000 IOPS. Premium Storage ondersteunt een opslagcache waarmee u de lees- en schrijfprestaties voor sommige werkbelastingen kunt verbeteren. Zie Overzicht van [beheerde schijven voor meer informatie.](../../../virtual-machines/managed-disks-overview.md) 

Er zijn ook drie belangrijke [schijftypen](../../../virtual-machines/managed-disks-overview.md#disk-roles) om rekening mee te houden voor uw SQL Server op azure-VM's: een besturingssysteemschijf, een tijdelijke schijf en uw gegevensschijven. Kies zorgvuldig wat wordt opgeslagen op het besturingssysteemstation en `(C:\)` het tijdelijke tijdelijke `(D:\)` station. 

### <a name="operating-system-disk"></a>Besturingssysteemschijf

Een besturingssysteemschijf is een VHD die kan worden opgestart en bevestigd als een werkende versie van een besturingssysteem en wordt gelabeld als het `C:\` station. Wanneer u een virtuele Azure-machine maakt, koppelt het platform ten minste één schijf aan de virtuele machine voor de besturingssysteemschijf. Het `C:\` station is de standaardlocatie voor installatie van toepassingen en bestandsconfiguratie. 

Gebruik voor SQL Server productieomgevingen de besturingssysteemschijf niet voor gegevensbestanden, logboekbestanden en foutlogboeken. 

### <a name="temporary-disk"></a>Tijdelijke schijf

Veel virtuele Azure-machines bevatten een ander schijftype, de tijdelijke schijf (gelabeld als het `D:\` station). Afhankelijk van de reeks en grootte van de virtuele machine varieert de capaciteit van deze schijf. De tijdelijke schijf is tijdelijk, wat betekent dat de schijfopslag opnieuw wordt gemaakt (zoals in, de toewijzing ervan wordt weer ingetrokken [](/troubleshoot/azure/virtual-machines/understand-vm-reboot)en opnieuw toegewezen), wanneer de virtuele machine opnieuw wordt opgestart of naar een andere host wordt verplaatst (bijvoorbeeld voor serviceherstel). 

Het tijdelijke opslagstation wordt niet persistent gemaakt voor externe opslag en mag daarom geen databasebestanden van gebruikers, transactielogboekbestanden of andere bestanden opslaan die moeten worden bewaard. 

Plaats tempdb op het lokale tijdelijke `D:\` SSD-station voor SQL Server werkbelastingen, tenzij het verbruik van de lokale cache een probleem is. Als u een virtuele machine gebruikt die geen tijdelijke schijf [heeft,](../../../virtual-machines/azure-vms-no-temp-disk.md) is het raadzaam om tempdb op een eigen geïsoleerde schijf of opslaggroep te plaatsen met caching ingesteld op alleen-lezen. Zie [tempdb data caching policies (Tempdb-beleid voor gegevens caching) voor meer informatie.](performance-guidelines-best-practices-storage.md#data-file-caching-policies)

### <a name="data-disks"></a>Gegevensschijven

Gegevensschijven zijn externe opslagschijven die vaak worden gemaakt [in](/windows-server/storage/storage-spaces/overview) opslaggroepen om de capaciteit en prestaties te overschrijden die elke schijf kan bieden aan de virtuele machine.

Koppel het minimum aantal schijven dat voldoet aan de IOPS-, doorvoer- en capaciteitsvereisten van uw workload. Niet groter zijn dan het maximum aantal gegevensschijven van de kleinste virtuele machine die u van plan bent om te worden gesizeed.

Plaats gegevens en logboekbestanden op gegevensschijven die zijn ingericht voor de beste prestatievereisten. 

Maak uw gegevensschijf zo op dat deze een grootte van 64 kB gebruikt voor alle gegevensbestanden die op een ander station zijn geplaatst dan het tijdelijke station (met een standaardgrootte `D:\` van 4 kB). SQL Server VM's die zijn geïmplementeerd via Azure Marketplace worden er gegevensschijven met de grootte van de toewijzingseenheid en interleave voor de opslaggroep op 64 kB ingesteld. 

## <a name="premium-disks"></a>Premium-schijven

Gebruik Premium SSD-schijven voor gegevens en logboekbestanden voor productie- SQL Server workloads. Premium - SSD IOPS en bandbreedte varieert op basis van de [schijfgrootte en het type](../../../virtual-machines/disks-types.md). 

Gebruik voor productieworkloads de P30- en/of P40-schijven voor SQL Server-gegevensbestanden om ondersteuning voor caching te garanderen en gebruik P30 tot P80 voor SQL Server-transactielogboekbestanden.  Voor de beste total cost of ownership begint u met P30s (5000 IOPS/200 MBPS) voor gegevens- en logboekbestanden en kiest u alleen hogere capaciteiten wanneer u het aantal schijven van de virtuele machine wilt beheren.

Voor OLTP-workloads moet u de doel-IOPS per schijf (of opslaggroep) vergelijken met uw prestatievereisten met behulp van workloads tijdens piektijden en de `Disk Reads/sec`  +  `Disk Writes/sec` prestatiemeters. Voor datawarehouse- en rapportageworkloads moet u de doeldoorvoer met behulp van workloads tijdens piektijden en de `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` overeenkomen. 

Gebruik Opslagruimten voor optimale prestaties, configureer twee pools, één voor de logboekbestanden en de andere voor de gegevensbestanden. Als u geen schijfstriping gebruikt, gebruikt u twee Premium SSD-schijven die zijn toe te kennen aan afzonderlijke stations, waarbij het ene station het logboekbestand bevat en de andere de gegevens bevat.

De [inrichtende IOPS en doorvoer](../../../virtual-machines/disks-types.md#premium-ssd) per schijf die wordt gebruikt als onderdeel van uw opslaggroep. De gecombineerde IOPS- en doorvoermogelijkheden van de schijven zijn de maximale capaciteit tot de doorvoerlimieten van de virtuele machine.

De best practice is om zo min mogelijk schijven te gebruiken en tegelijkertijd te voldoen aan de minimale vereisten voor IOPS (en doorvoer) en capaciteit. Het evenwicht tussen prijs en prestaties is echter meestal beter met een groot aantal kleine schijven in plaats van een klein aantal grote schijven.

### <a name="scaling-premium-disks"></a>Premium-schijven schalen

Wanneer een Azure Managed Disk voor het eerst wordt geïmplementeerd, is de prestatielaag voor die schijf gebaseerd op de ingerichte schijfgrootte. Wijs de prestatielaag aan bij de implementatie of wijzig deze later, zonder de grootte van de schijf te wijzigen. Als de vraag toeneemt, kunt u het prestatieniveau verhogen om te voldoen aan de behoeften van uw bedrijf. 

Door de prestatielaag te wijzigen, kunnen beheerders zich voorbereiden op en voldoen aan een hogere vraag zonder afhankelijk te zijn van [schijf bursting.](../../../virtual-machines/disk-bursting.md#credit-based-bursting) 

Gebruik de hogere prestaties zo lang als nodig is, waarbij facturering is ontworpen om te voldoen aan de opslagprestatielaag. Werk de laag bij om aan de prestatievereisten te voldoen zonder de capaciteit te verhogen. Ga terug naar de oorspronkelijke laag wanneer de extra prestaties niet meer nodig zijn.

Deze rendabele en tijdelijke uitbreiding van de prestaties is een sterke use-case voor gerichte gebeurtenissen, zoals winkel, prestatietesten, trainingsgebeurtenissen en andere korte tijdvensters waar betere prestaties slechts voor een korte termijn nodig zijn. 

Zie Prestatielagen voor [beheerde schijven voor meer informatie.](../../../virtual-machines/disks-change-performance.md) 

## <a name="azure-ultra-disk"></a>Azure Ultra Disk

Als er reactietijden van minder dan een seconde nodig zijn met een gereduceerde latentie, kunt u [azure ultra disk](../../../virtual-machines/disks-types.md#ultra-disk) gebruiken voor het SQL Server-logboekstation of zelfs het gegevensstation voor toepassingen die zeer gevoelig zijn voor I/O-latentie. 

Ultra disk kan worden geconfigureerd waar capaciteit en IOPS onafhankelijk van elkaar kunnen worden geschaald. Met Ultra Disk Kunnen beheerders een schijf inrichten met de vereisten voor capaciteit, IOPS en doorvoer op basis van de behoeften van de toepassing. 

Ultra disk wordt niet ondersteund op alle VM-serie en heeft andere beperkingen, zoals de beschikbaarheid van regio's, redundantie en ondersteuning voor Azure Backup. Zie Azure [Ultra Disks gebruiken](../../../virtual-machines/disks-enable-ultra-ssd.md) voor een volledige lijst met beperkingen voor meer informatie. 

## <a name="standard-hdds-and-ssds"></a>Standard-HDD's en -HDD's

[Standard-HDD's](../../../virtual-machines/disks-types.md#standard-hdd) en - SD's hebben verschillende latentie en bandbreedte en worden alleen aanbevolen voor dev/test-workloads. Productieworkloads moeten premium - SD's gebruiken. Als u gebruik maakt van Standard - SSD (scenario's voor dev/test), wordt aanbevolen om het maximum aantal gegevensschijven toe te voegen dat door uw [VM-grootte](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) wordt ondersteund en schijfstriping met Opslagruimten te gebruiken voor de beste prestaties.

## <a name="caching"></a>Caching

Virtuele machines die ondersteuning bieden voor premium opslagcaching, kunnen profiteren van een extra functie, Azure BlobCache of hostcache, om de IOPS- en doorvoermogelijkheden van een virtuele machine uit te breiden. Virtuele machines die zijn ingeschakeld voor zowel Premium Storage als Premium Storage-caching hebben deze twee verschillende limieten voor opslagbandbreedte die samen kunnen worden gebruikt om de opslagprestaties te verbeteren.

De IOPS- en MBps-doorvoer zonder caching telt mee voor de doorvoerlimieten voor niet-gecachede schijven van een virtuele machine. De maximale limieten in de cache bieden een extra buffer voor leesgegevens die helpen bij het aanpakken van groei en onverwachte pieken.

Schakel Premium-caching in wanneer de optie wordt ondersteund om de prestaties voor leesgegevens op het gegevensstation aanzienlijk te verbeteren zonder extra kosten. 

Lees- en schrijfsnelheid naar de Azure BlobCache (IOPS en doorvoer in cache) tellen niet mee voor de niet-gecachede IOPS en doorvoerlimieten van de virtuele machine.

> [!NOTE]
> Schijf caching wordt niet ondersteund voor schijven van 4 TiB en groter (P50 en groter). Als er meerdere schijven aan uw VM zijn gekoppeld, biedt elke schijf die kleiner is dan 4 TiB ondersteuning voor caching. Zie Schijf in [de caching voor meer informatie.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 

### <a name="uncached-throughput"></a>Niet-gecachede doorvoer

De maximale niet-gecachede schijf-IOPS en doorvoer is de maximale limiet voor externe opslag die de virtuele machine kan verwerken. Deze limiet wordt gedefinieerd op de virtuele machine en is geen limiet van de onderliggende schijfopslag. Deze limiet geldt alleen voor I/O voor gegevensstations die extern zijn gekoppeld aan de VM, niet op de lokale I/O voor het tijdelijke station (station) of `D:\` het besturingssysteemstation.

De hoeveelheid niet-gecachede IOPS en doorvoer die beschikbaar is voor een virtuele machine, kan worden gecontroleerd in de documentatie voor uw virtuele machine.

In de documentatie van de [M-serie](../../../virtual-machines/m-series.md) ziet u bijvoorbeeld dat de maximale niet-gecachede doorvoer voor de Standard_M8ms-VM 5000 IOPS en 125 MBps niet-gecachede schijfdoorvoer is. 

![Schermopname van documentatie over niet-gecachede schijfdoorvoer uit de M-serie.](./media/performance-guidelines-best-practices/m-series-table.png)

U kunt ook zien dat de Standard_M32ts ondersteuning biedt voor 20.000 niet-gecachede schijf-IOPS en 500 MBps ongecachede schijfdoorvoer. Deze limiet wordt bepaald op het niveau van de virtuele machine, ongeacht de onderliggende Premium Disk Storage.

Zie niet-gecachede en in [cache opgeslagen limieten voor meer informatie.](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)


### <a name="cached-and-temp-storage-throughput"></a>Doorvoer in cache en tijdelijke opslag

De maximale doorvoerlimiet voor opslag in cache en tijdelijke opslag is een afzonderlijke limiet van de limiet voor niet-gecachede doorvoer op de virtuele machine. De Azure BlobCache bestaat uit een combinatie van het willekeurig toegangsgeheugen van de virtuele machinehost en de lokaal gekoppelde SSD. Het tijdelijke station `D:\` (station) binnen de virtuele machine wordt ook gehost op deze lokale SSD.

De maximale doorvoerlimiet voor opslag in cache en tijdelijke opslag bepaalt de I/O voor het lokale tijdelijke station (station) en de Azure BlobCache alleen als `D:\` hostcache  is ingeschakeld. 

Wanneer caching is ingeschakeld voor Premium-opslag, kunnen virtuele machines worden geschaald buiten de beperkingen van de niet-gecachede VM IOPS en doorvoerlimieten voor externe opslag.  

Alleen bepaalde virtuele machines ondersteunen zowel Premium Storage als Premium Storage-caching (die moet worden geverifieerd in de documentatie van de virtuele machine). De documentatie uit [de M-serie](../../../virtual-machines/m-series.md) geeft bijvoorbeeld aan dat zowel Premium Storage als Premium Storage-caching wordt ondersteund: 

![Schermopname met ondersteuning voor Premium Storage M-serie.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

De limieten van de cache variëren op basis van de grootte van de virtuele machine. De virtuele Standard_M8ms ondersteunt bijvoorbeeld 10000 schijf-IOPS in cache en 1000 MBps schijfdoorvoer in de cache met een totale cachegrootte van 793 GiB. Op dezelfde manier ondersteunt de Standard_M32ts-VM schijf-IOPS in cache 40000 en 400 MBps schijfdoorvoer in de cache met een totale cachegrootte van 3174 GiB. 

![Schermopname van documentatie over schijfdoorvoer in cache uit de M-serie.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

U kunt host-caching handmatig inschakelen op een bestaande VM. Stop alle toepassingsworkloads en SQL Server services voordat er wijzigingen worden aangebracht in het cachingbeleid van uw virtuele machine. Als u een van de cache-instellingen van de virtuele machine verandert, wordt de doelschijf losgekoppeld en opnieuw vastgemaakt nadat de instellingen zijn toegepast.

### <a name="data-file-caching-policies"></a>Beleid voor gegevensbestands caching

Uw beleid voor opslag in de opslagopslag is afhankelijk van het type SQL Server gegevensbestanden die op het station worden gehost. 

De volgende tabel bevat een overzicht van de aanbevolen beleidsregels voor opslaan in de SQL Server gegevens: 

|SQL Server schijf |Aanbeveling |
|---------|---------|
| **Gegevensschijf** | Schakel `Read-only` caching in voor de schijven die als host SQL Server gegevensbestanden. <br/> Leesgegevens uit de cache zijn sneller dan de niet-gecachede leesgegevens van de gegevensschijf. <br/> Niet-gecachede IOPS en doorvoer plus IOPS in cache en doorvoer leveren de totale mogelijke prestaties op die beschikbaar zijn vanaf de virtuele machine binnen de VM-limieten, maar de werkelijke prestaties variëren afhankelijk van de mogelijkheid van de werkbelasting om de cache te gebruiken (verhouding cache treffers). <br/>|
|**Transactielogboekschijf**|Stel het cachingbeleid in op `None` voor schijven die het transactielogboek hosten.  Het inschakelen van caching voor de schijf van het transactielogboek heeft geen prestatievoordeel. In feite kan het inschakelen van of opslaan in cache op het logboekstation de prestaties van de schrijf schrijfprestaties ten opzichte van het station verminderen en de hoeveelheid cache die beschikbaar is voor leesbare gegevens op het gegevensstation `Read-only` `Read/Write` verminderen.  |
|**Besturingssysteemschijf** | Het standaardbeleid voor caching kan `Read-only` of `Read/write` voor het besturingssysteemstation zijn. <br/> Het wordt niet aanbevolen om het cachingniveau van het besturingssysteemstation te wijzigen.  |
| **tempdb**| Als tempdb vanwege capaciteitsredenen niet op het tijdelijke station kan worden geplaatst, kunt u de virtuele machine vergroten of vergroten of weer groter maken of tempdb op een afzonderlijk gegevensstation plaatsen met `D:\` `Read-only` caching geconfigureerd. <br/> De cache van de virtuele machine en het tijdelijke station gebruiken beide de lokale SSD, dus houd hier rekening mee wanneer de tempdb-I/O wordt meegetelde voor de limieten voor IOPS en doorvoer van virtuele machines in de cache wanneer deze worden gehost op het tijdelijke station.| 
| | | 


Zie Schijf [caching voor meer informatie.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Schijf striping

Analyseer de doorvoer en bandbreedte die vereist zijn voor uw SQL-gegevensbestanden om het aantal gegevensschijven te bepalen, inclusief het logboekbestand en tempdb. De doorvoer- en bandbreedtelimieten variëren per VM-grootte. Zie [VM-grootte](../../../virtual-machines/sizes.md) voor meer informatie

Voeg extra gegevensschijven toe en gebruik schijfstriping voor meer doorvoer. Een toepassing die bijvoorbeeld 12.000 IOPS- en 180 MB/s-doorvoer nodig heeft, kan drie gestreepte P30-schijven gebruiken om 15.000 IOPS en 600 MB/s doorvoer te leveren. 

Zie Schijf striping als u schijf [striping wilt configureren.](storage-configuration.md#disk-striping) 

## <a name="disk-capping"></a>Schijflimiet 

Er zijn doorvoerlimieten op schijf- en virtuele machineniveau. De maximale IOPS-limieten per VM en per schijf verschillen en zijn onafhankelijk van elkaar.

Toepassingen die resources buiten deze limieten verbruiken, worden beperkt (ook wel beperkt genoemd). Selecteer een virtuele machine en schijfgrootte in een schijfstree die voldoet aan de toepassingsvereisten en die geen limieten kent. Als u limieten wilt aanpakken, gebruikt u caching of werkt u de toepassing af zodat er minder doorvoer nodig is.

Een toepassing die bijvoorbeeld 12.000 IOPS en 180 MB/s nodig heeft, kan: 
- Gebruik de [Standard_M32ms](../../../virtual-machines/m-series.md) met een maximale niet-gecachede schijfdoorvoer van 20.000 IOPS en 500 MBps.
- Stripe drie P30-schijven om 15.000 IOPS en een doorvoer van 600 MB/s te leveren.
- Gebruik een [virtuele Standard_M16ms](../../../virtual-machines/m-series.md) machine en gebruik hostcacheopslag om lokale cache te gebruiken voor het verbruik van doorvoer. 

Virtuele machines die zijn geconfigureerd om omhoog te schalen in tijden van hoog gebruik, moeten opslag inrichten met voldoende IOPS en doorvoer om de maximale VM-grootte te ondersteunen, terwijl het totale aantal schijven kleiner dan of gelijk is aan het maximumaantal dat wordt ondersteund door de kleinste VM-SKU die moet worden gebruikt.

Zie [Schijf-I/O-limieten](../../../virtual-machines/disks-performance.md)voor meer informatie over schijflimietbeperkingen en het gebruik van caching om limieten te voorkomen.

> [!NOTE] 
> Sommige schijflimieten kunnen nog steeds resulteren in voldoende prestaties voor gebruikers; werkbelastingen afstemmen en onderhouden in plaats van de schaal aan te brengen op een grotere VM om het beheer van kosten en prestaties voor het bedrijf in balans te brengen. 


## <a name="write-acceleration"></a>Schrijfversnelling

Write Acceleration is een schijffunctie die alleen beschikbaar is voor de [M-serie](https://docs.microsoft.com/azure/virtual-machines/m-series) Virtual Machines (VM's). Het doel van Schrijfversnelling is het verbeteren van de I/O-latentie van schrijfingen voor Azure Premium Storage wanneer u een I/O-latentie van één cijfer nodig hebt vanwege essentiële OLTP-workloads of datawarehouse-omgevingen met een hoog volume. 

Gebruik Write Acceleration om de schrijflatentie te verbeteren naar het station dat als host voor de logboekbestanden wordt gebruikt. Gebruik Write Acceleration niet voor SQL Server gegevensbestanden. 

Write Accelerator schijven dezelfde IOPS-limiet delen als de virtuele machine. Gekoppelde schijven mogen de limiet Write Accelerator IOPS voor een VM niet overschrijden.  

De volgende tabel geeft een overzicht van het aantal gegevensschijven en IOPS dat per virtuele machine wordt ondersteund: 

| VM-SKU  | Aantal Write Accelerator schijven  | Write Accelerator schijf-IOPS per VM  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20.000  |
| M128ms, M128s  | 16  | 20.000  |
| M208ms_v2, M208s_v2  | 8  | 10.000  |
| M64ms, M64ls, M64s  |  8 | 10.000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Er zijn een aantal beperkingen voor het gebruik van Write Acceleration. Zie Beperkingen bij het gebruik [van](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)Write Accelerator.


### <a name="comparing-to-azure-ultra-disk"></a>Vergelijking met Azure Ultra Disk

Het grootste verschil tussen Write Acceleration en Azure Ultra Disks is dat Write Acceleration een functie voor virtuele machines is die alleen beschikbaar is voor de M-serie en Azure Ultra Disks is een opslagoptie. Write Acceleration is een voor schrijven geoptimaliseerde cache met eigen beperkingen op basis van de grootte van de virtuele machine. Azure Ultra Disks is een schijfopslagoptie met lage latentie voor Azure Virtual Machines. 

Gebruik indien mogelijk Write Acceleration over ultra disks voor de transactielogboekschijf. Gebruik Azure Ultra Disks voor virtuele machines die geen ondersteuning bieden voor Write Acceleration, maar een lage latentie in het transactielogboek vereisen. 

## <a name="monitor-storage-performance"></a>Opslagprestaties bewaken

Als u de opslagbehoeften wilt beoordelen en wilt bepalen hoe goed de opslag presteert, moet u begrijpen wat u moet meten en wat deze indicatoren betekenen. 

[IOPS (invoer/uitvoer per seconde)](../../../virtual-machines/premium-storage-performance.md#iops) is het aantal aanvragen dat de toepassing doet voor opslag per seconde. Meet IOPS met prestatiemetertellers `Disk Reads/sec` en `Disk Writes/sec` . [OLTP-toepassingen (online transactieverwerking)](/azure/architecture/data-guide/relational-data/online-transaction-processing) moeten een hogere IOPS mogelijk maken voor optimale prestaties. Toepassingen zoals systemen voor betalingsverwerking, onlinewinkelen en verkooppunten zijn allemaal voorbeelden van OLTP-toepassingen.

[Doorvoer](../../../virtual-machines/premium-storage-performance.md#throughput) is de hoeveelheid gegevens die naar de onderliggende opslag wordt verzonden, vaak gemeten in megabytes per seconde. Meet de doorvoer met de prestatiemetertellers `Disk Read Bytes/sec` en `Disk Write Bytes/sec` . [Datawarehousing](/azure/architecture/data-guide/relational-data/data-warehousing) is geoptimaliseerd voor het maximaliseren van de doorvoer via IOPS. Toepassingen zoals gegevensopslag voor analyse, rapportage, ETL-werkstreams en andere business intelligence zijn allemaal voorbeelden van datawarehousingtoepassingen.

I/O-eenheden zijn van invloed op IOPS en doorvoermogelijkheden, omdat kleinere I/O-grootten een hogere IOPS opleveren en grotere I/O-grootten een hogere doorvoer opleveren. SQL Server kiest automatisch de optimale I/O-grootte. Zie [IOPS,](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)doorvoer en latentie optimaliseren voor uw toepassingen voor meer informatie over . 

Er zijn specifieke Azure Monitor die waardevol zijn voor het detecteren van limieten op het niveau van de virtuele machine en schijf, evenals het verbruik en de status van de AzureBlob-cache. Zie Metrische gegevens over opslaggebruik om belangrijke tellers te identificeren die u aan uw bewakingsoplossing en Azure Portal dashboard [wilt toevoegen.](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics) 

> [!NOTE]
> Azure Monitor biedt momenteel geen metrische gegevens op schijfniveau voor het tijdelijke tijdelijke station `(D:\)` . Het percentage verbruikte IOPS in de cache van de VM en het percentage verbruikte bandbreedte in de cache weerspiegelen IOPS en doorvoer van zowel het tijdelijke tijdelijke station als de `(D:\)` hostcache.


## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen in deze reeks voor meer informatie over best practices voor prestaties:
- [Snelle controlelijst](performance-guidelines-best-practices-checklist.md)
- [VM-grootte](performance-guidelines-best-practices-vm-size.md)
- [Basislijn verzamelen](performance-guidelines-best-practices-collect-baseline.md)

Zie Beveiligingsoverwegingen voor [beveiligingsoverwegingen voor SQL Server in Azure Virtual Machines.](security-considerations-best-practices.md)

Raadpleeg de blog OLTP SQL Server prestaties optimaliseren voor gedetailleerde tests van de prestaties van [](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)azure-VM's met TPC-E- en TPC_C-benchmarks.

Lees andere SQL Server virtual machine op SQL Server overzicht van [Azure Virtual Machines.](sql-server-on-azure-vm-iaas-what-is-overview.md) Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).
