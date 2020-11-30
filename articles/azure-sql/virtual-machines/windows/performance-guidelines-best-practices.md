---
title: Richt lijnen voor prestaties voor SQL Server in azure | Microsoft Docs
description: Biedt richt lijnen voor het optimaliseren van SQL Server prestaties in Microsoft Azure Virtual Machines.
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
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6a6b39d540427b7c3400fded62431c914db23bb3
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327318"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Prestatierichtlijnen voor SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dit artikel bevat richt lijnen voor het optimaliseren van SQL Server prestaties in Microsoft Azure Virtual Machines.

## <a name="overview"></a>Overzicht

 Bij het uitvoeren van SQL Server op Azure Virtual Machines raden we u aan om dezelfde opties voor het afstemmen van de prestaties van de data base te blijven gebruiken die van toepassing zijn op SQL Server in on-premises server omgevingen. De prestaties van een relationele database in een openbare cloud zijn echter afhankelijk van vele factoren, zoals de grootte van een virtuele machine en de configuratie van de gegevensschijven.

[SQL Server installatie kopieën die zijn ingericht in de Azure Portal](sql-vm-create-portal-quickstart.md) algemene aanbevolen procedures voor opslag configuratie (Zie [opslag configuratie voor SQL Server virtuele machines (vm's)](storage-configuration.md)) voor meer informatie over het configureren van opslag. Na het inrichten kunt u overwegen om andere optimalisaties toe te passen die in dit artikel worden besproken. Baseer uw keuzes op uw werk belasting en controleer door testen.

> [!TIP]
> Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Dit artikel is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure virtual machines. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke optimalisatie nodig hebt die hieronder wordt vermeld. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.

## <a name="quick-checklist"></a>Snelle controle lijst

Hieronder vindt u een snelle controle lijst voor optimale prestaties van SQL Server op Azure Virtual Machines:

| Onderwerp | Optimalisaties |
| --- | --- |
| [VM-grootte](#vm-size-guidance) | -Gebruik VM-grootten met vier of meer vCPU, zoals [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) of hoger, of [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) of hoger.<br/><br/> - [Es-, EAS-, DS-en das-reeksen](../../../virtual-machines/sizes-general.md) bieden het optimale geheugen om de vCPU verhouding die is vereist voor de prestaties van de OLTP-werk belasting. <br/><br/> - De [M-serie](../../../virtual-machines/m-series.md) biedt het hoogste geheugen om de vCPU verhouding te bieden die nodig is voor essentiële prestaties en is ideaal voor Data Warehouse-workloads. <br/><br/> -Verzamel de [limieten](../../../virtual-machines/sizes-general.md) voor [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [door Voer](../../../virtual-machines/premium-storage-performance.md#throughput) en [latentie](../../../virtual-machines/premium-storage-performance.md#latency) van de doel belasting op piek tijden door de [controle lijst voor toepassings prestatie vereisten](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) te volgen en selecteer vervolgens de VM-grootte die kan worden geschaald naar de prestatie vereisten van uw werk belasting.|
| [Storage](#storage-guidance) | -Raadpleeg de blog [optimalisatie voor OLTP-prestaties](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)voor gedetailleerde tests van SQL Server prestaties op Azure virtual machines met TPC-E en TPC_C-benchmarks. <br/><br/> - [Premium ssd's](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) gebruiken voor de beste prijs-en prestatie voordelen. [Alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) configureren voor gegevens bestanden en geen cache voor het logboek bestand. <br/><br/> -Gebruik [Ultra schijven](../../../virtual-machines/disks-types.md#ultra-disk) als er minder dan 1 MS-opslag latenties zijn vereist voor de werk belasting. Zie [migreren naar ultra Disk](storage-migrate-to-ultradisk.md) voor meer informatie. <br/><br/> -Verzamel de vereisten voor de opslag latentie voor SQL Server gegevens, logboeken en tijdelijke DB-bestanden door [de toepassing te bewaken](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) voordat u het schijf type kiest. Als <1ms-opslag latenties zijn vereist, gebruikt u ultra disks, anders gebruikt u Premium SSD. Als lage latenties alleen vereist zijn voor het logboek bestand en niet voor gegevens bestanden, moet u [de ultra Disk](../../../virtual-machines/disks-enable-ultra-ssd.md) alleen voor het logboek bestand voorzien van de vereiste IOPS en doorvoer niveaus. <br/><br/> -  [Premium-bestands shares](failover-cluster-instance-premium-file-share-manually-configure.md) worden aanbevolen als gedeelde opslag voor een SQL Server failover-cluster exemplaar. Premium-bestands shares bieden geen ondersteuning voor caching en bieden beperkte prestaties ten opzichte van Premium SSD-schijven. Kies Premium SSD-Managed disks via Premium-bestands shares voor zelfstandige SQL-instanties; gebruik Premium-bestands shares voor de gedeelde opslag van het failover-cluster exemplaar voor een gemakkelijke onderhouds-en schaal baarheid. <br/><br/> -Standaard opslag wordt alleen aanbevolen voor ontwikkelings-en test doeleinden of voor back-upbestanden en mag niet worden gebruikt voor productie werkbelastingen. <br/><br/> -Houd het [opslag account](../../../storage/common/storage-account-create.md) en SQL Server virtuele machine in dezelfde regio.<br/><br/> -Schakel Azure [geo-redundante opslag](../../../storage/common/storage-redundancy.md) (geo-replicatie) uit voor het opslag account.  |
| [Disks](#disks-guidance) | -Gebruik Mini maal 2 [Premium SSD-schijven](../../../virtual-machines/disks-types.md#premium-ssd) (1 voor het logboek bestand en 1 voor gegevens bestanden). <br/><br/> -Voor werk belastingen die <1 MS IO-latenties vereisen, schakelt u schrijf versnelling voor M-serie in en overweegt u Ultra-SSD schijven te gebruiken voor es-en DS-serie. <br/><br/> - [Alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) inschakelen op de schijven die als host dienen voor de gegevens bestanden.<br/><br/> -Voeg een extra gratis IOPS/doorvoer capaciteit van 20% toe dan is vereist bij het [configureren van opslag voor SQL Server gegevens, logboeken en tempdb-bestanden](storage-configuration.md) <br/><br/> -Vermijd het gebruik van besturings systeem of tijdelijke schijven voor database opslag of logboek registratie.<br/><br/> -Schakel caching niet in op een of meer schijven die als host optreden voor het logboek bestand.  **Belang rijk**: Stop de SQL Server-service wanneer u de cache-instellingen voor een Azure virtual machines schijf wijzigt.<br/><br/> -Meerdere Azure-gegevens schijven Stripe om de opslag doorvoer te verg root.<br/><br/> -Indeling met gedocumenteerde toewijzings grootten. <br/><br/> -Sla TempDB op het lokale SSD- `D:\` station op voor essentiële SQL Server workloads (nadat u de juiste VM-grootte hebt gekozen). Als u de virtuele machine maakt op basis van de sjablonen Azure Portal of Azure Quick Start en [tijdelijke data base op de lokale schijf plaatst](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) , hoeft u geen verdere actie te ondernemen. voor alle andere gevallen volgt u de stappen in de blog voor het  [gebruik van ssd's voor het opslaan van tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) om fouten te voor komen na het opnieuw opstarten. Als de capaciteit van het lokale station niet voldoende is voor de grootte van uw tijdelijke data base, plaatst u de tijdelijke data base op een opslag groep die is [striped](../../../virtual-machines/premium-storage-performance.md) op Premium SSD-schijven met [alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching)opslag. |
| [I/O](#io-guidance) |-Database pagina compressie inschakelen.<br/><br/> -Schakel de initialisatie van Instant file in voor gegevens bestanden.<br/><br/> -Beperk de automatische groei van de data base.<br/><br/> -Autoverkleinen van de data base uitschakelen.<br/><br/> -Alle data bases verplaatsen naar gegevens schijven, inclusief systeem databases.<br/><br/> -Verplaats SQL Server fouten logboek en traceer bestands mappen naar gegevens schijven.<br/><br/> -Standaard back-ups en database bestands locaties configureren.<br/><br/> - [Vergrendelde pagina's in het geheugen inschakelen](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> -SQL Server prestatie verbeteringen Toep assen. |
| [Functie-specifiek](#feature-specific-guidance) | -Maak rechtstreeks een back-up naar Azure Blob-opslag.<br/><br/>- [Back-ups van bestands momentopnamen](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) gebruiken voor data bases die groter zijn dan 12 TB. <br/><br/>-Gebruik meerdere tijdelijke DB-bestanden, 1 bestand per kern, Maxi maal 8 bestanden.<br/><br/>-Stel het maximale server geheugen in op 90% of tot 50 GB voor het besturings systeem. <br/><br/>-Zacht NUMA inschakelen. |

Raadpleeg de details en richt lijnen in de volgende secties voor meer informatie over *hoe* en *Waarom* u deze optimalisaties wilt maken.

## <a name="vm-size-guidance"></a>Richt lijnen voor VM-grootte

Begin met het verzamelen van de vereisten voor CPU, geheugen en opslag doorvoer van de werk belasting op piek tijden. \LogicalDisk\Disk Lees bewerkingen per seconde en \LogicalDisk\Disk schrijf bewerkingen per seconde kunnen worden gebruikt voor het verzamelen van de vereisten voor lezen en schrijven IOPS en \LogicalDisk\Disk bytes per seconde kunnen worden gebruikt voor het verzamelen van [vereisten voor opslag doorvoer](../../../virtual-machines/premium-storage-performance.md#disk-caching) voor gegevens, logboeken en tijdelijke DB-bestanden. Nadat de vereisten voor IOPS en door Voer op pieken zijn gedefinieerd, evalueren VM-grootten deze capaciteit. Als voor uw werk belasting bijvoorbeeld 20 KB Lees-IOPS is vereist en het aantal IOPS op piek schrijven, kunt u E16s_v3 kiezen (met Maxi maal 32 KB in de cache opgeslagen en 25600 niet-opgeslagen IOPS) of M16_s (met Maxi maal 20 KB in cache opgeslagen en niet-opgeslagen IOPS) met 2 P30-schijven. Zorg ervoor dat u zowel de door Voer als de IOPS-vereisten van de werk belasting begrijpt, aangezien Vm's verschillende schaal limieten hebben voor IOPS en door voer.<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) en [Es_v3-serie](../../../virtual-machines/ev3-esv3-series.md) worden gehost op hardware voor algemeen gebruik met Intel Haswell-of Broadwell-processors. De [M-serie](../../../virtual-machines/m-series.md) biedt het hoogste aantal vCPU en geheugen voor de grootste SQL Server werk belastingen en wordt gehost op hardware die is geoptimaliseerd voor geheugen met Skylake-processor familie. Deze VM-reeks biedt ondersteuning voor Premium-opslag, die wordt aanbevolen voor de beste prestaties met de Lees cache op hostniveau. Zowel de Es_v3 als de M-serie zijn ook beschikbaar in [beperkte kern grootten](../../../virtual-machines/constrained-vcpu.md), waarmee geld wordt bespaard op workloads met een lagere reken kracht en hoge opslag capaciteit. 

## <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

Raadpleeg de blog [optimalisatie voor OLTP-prestaties](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)voor gedetailleerde tests van SQL Server prestaties op Azure virtual machines met TPC-E en TPC_C-benchmarks. 

Azure Blob-cache met Premium Ssd's wordt aanbevolen voor alle productie werkbelastingen. 

> [!WARNING]
> Standaard Hdd's en Ssd's hebben verschillende latenties en band breedte en worden alleen aanbevolen voor dev/test-workloads. Werk belastingen voor productie moeten premium-Ssd's gebruiken.

Daarnaast raden wij u aan uw Azure Storage-account te maken in hetzelfde Data Center als uw SQL Server virtuele machines om de overdrachts vertraging te verminderen. Wanneer u een opslag account maakt, moet u geo-replicatie uitschakelen als consistente schrijf volgorde op meerdere schijven niet gegarandeerd. In plaats daarvan kunt u overwegen een SQL Server nood herstel technologie te configureren tussen twee Azure-data centers. Zie voor meer informatie [High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Richt lijnen voor schijven

Er zijn drie hoofd typen schijven op virtuele machines van Azure:

* **Besturingssysteem schijf**: wanneer u een virtuele Azure-machine maakt, wordt er ten minste één schijf (gelabeld als station **C** ) aan de virtuele machine gekoppeld voor de schijf van het besturings systeem. Deze schijf is een VHD die is opgeslagen als een pagina-Blob in de opslag.
* **Tijdelijke schijf**: Azure virtual machines bevatten een andere schijf, de tijdelijke schijf (met het label **D**: station). Dit is een schijf op het knoop punt dat kan worden gebruikt voor Scratch ruimte.
* **Gegevens schijven**: u kunt ook extra schijven aan uw virtuele machine koppelen als gegevens schijven. deze worden opgeslagen als pagina-blobs.

In de volgende secties worden aanbevelingen beschreven voor het gebruik van deze verschillende schijven.

### <a name="operating-system-disk"></a>Besturingssysteemschijf

Een besturingssysteem schijf is een VHD die u kunt opstarten en koppelen als een actieve versie van een besturings systeem en wordt aangeduid als station **C** .

Het standaard beleid voor opslaan in cache op de besturingssysteem schijf is **lezen/schrijven**. Voor prestatie gevoelige toepassingen raden we u aan om gegevens schijven te gebruiken in plaats van de besturingssysteem schijf. Zie de sectie op gegevens schijven hieronder.

### <a name="temporary-disk"></a>Tijdelijke schijf

Het tijdelijke opslag station, aangeduid als het **D** -station, wordt niet persistent gemaakt in Azure Blob-opslag. Sla uw gebruikers database bestanden of gebruikers transactie logboek bestanden niet op in het station **D**:.

Plaats TempDB op het lokale SSD `D:\` -station voor essentiële SQL Server workloads (nadat u de juiste VM-grootte hebt gekozen). Als u de virtuele machine maakt op basis van de sjablonen Azure Portal of Azure Quick Start en [tijdelijke data base op de lokale schijf plaatst](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), hebt u geen verdere actie nodig. voor alle andere gevallen volgt u de stappen in de blog voor het  [gebruik van ssd's voor het opslaan van tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) om fouten te voor komen na het opnieuw opstarten. Als de capaciteit van het lokale station niet voldoende is voor de grootte van uw tijdelijke data base, plaatst u de tijdelijke data base op een opslag groep die is [striped](../../../virtual-machines/premium-storage-performance.md) op Premium SSD-schijven met [alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching)opslag.

Voor virtuele machines die Premium Ssd's ondersteunen, kunt u TempDB ook opslaan op een schijf die ondersteuning biedt voor Premium Ssd's met lees cache ingeschakeld.


### <a name="data-disks"></a>Gegevensschijven

* **Premium SSD-schijven gebruiken voor gegevens-en logboek bestanden**: als u schijf striping niet gebruikt, gebruikt u twee Premium SSD-schijven waarbij de ene schijf het logboek bestand bevat en de andere de gegevens bevat. Elk Premium SSD biedt een aantal IOPS en band breedte (MB/s), afhankelijk van de grootte, zoals in het artikel wordt weer gegeven, [selecteert u een schijf type](../../../virtual-machines/disks-types.md). Als u een schijf Stripe-techniek gebruikt, zoals opslag ruimten, verkrijgt u optimale prestaties door twee groepen te hebben, één voor de logboek bestanden en de andere voor de gegevens bestanden. Als u echter van plan bent SQL Server failover cluster instances (FCI) te gebruiken, moet u één pool configureren of gebruikmaken van [Premium-bestands shares](failover-cluster-instance-premium-file-share-manually-configure.md) .

   > [!TIP]
   > - Zie de volgende blog post: [richt lijnen voor opslag configuratie voor SQL Server op Azure virtual machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm)voor test resultaten op verschillende schijf-en werkbelasting configuraties.
   > - Voor essentiële prestaties voor SQL-servers die ~ 50.000 IOPS nodig hebben, kunt u overwegen om tien P30 schijven te vervangen door een Ultra-SSD. Zie voor meer informatie het volgende blog bericht: [bedrijfs kritieke prestaties met ultra-SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Wanneer u in de portal een SQL Server virtuele machine inricht, hebt u de mogelijkheid om uw opslag configuratie te bewerken. Afhankelijk van uw configuratie, configureert Azure een of meer schijven. Meerdere schijven worden gecombineerd tot één opslag groep met striping. De gegevens en logboek bestanden bevinden zich samen in deze configuratie. Zie [opslag configuratie voor SQL Server vm's](storage-configuration.md)voor meer informatie.

* **Schijf striping**: u kunt extra gegevens schijven toevoegen en schijf striping gebruiken voor meer door voer. Als u het aantal gegevens schijven wilt bepalen, moet u het aantal IOPS en band breedte analyseren dat vereist is voor uw logboek bestand (en) en voor uw gegevens en TempDB-bestand (en). U ziet dat verschillende VM-grootten verschillende limieten hebben voor het aantal IOPs en bandbreedte dat wordt ondersteund. Zie de tabellen op IOPS per [VM-grootte](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Volg de volgende richtlijnen:

  * Voor Windows 8/Windows Server 2012 of hoger gebruikt u [opslag ruimten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) met de volgende richt lijnen:

      1. Stel de Interleave (Stripe-grootte) in op 64 KB (65.536 bytes) voor OLTP-workloads en 256 KB (262.144 bytes) voor werk belastingen voor gegevens opslag om prestaties te voor komen door een onjuiste uitlijning van de partitie. Dit moet worden ingesteld met Power shell.
      2. Aantal kolommen instellen = aantal fysieke schijven. Gebruik Power shell bij het configureren van meer dan 8 schijven (niet Serverbeheer gebruikers interface). 

    Met de volgende Power shell wordt bijvoorbeeld een nieuwe opslag groep gemaakt met de Interleave-grootte tot 64 KB en het aantal kolommen gelijk aan de hoeveelheid fysieke schijf in de opslag groep:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Voor Windows 2008 R2 of eerder kunt u dynamische schijven (door het besturings systeem gestripde volumes) gebruiken en de Stripe-grootte altijd 64 KB. Deze optie is afgeschaft vanaf Windows 8/Windows Server 2012. Zie de ondersteunings verklaring op [Virtual Disk service overgang naar Windows Storage Management API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)(Engelstalig) voor meer informatie.

  * Als u [opslagruimten direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) gebruikt met [SQL Server failoverclusterknooppunten](failover-cluster-instance-storage-spaces-direct-manually-configure.md), moet u één groep configureren. Hoewel er verschillende volumes kunnen worden gemaakt op die ene groep, delen ze allemaal dezelfde kenmerken, zoals hetzelfde cache beleid.

  * Bepaal het aantal schijven dat is gekoppeld aan uw opslag groep op basis van de belasting verwachtingen. Houd er wel bij dat verschillende VM-grootten verschillende aantallen gekoppelde gegevens schijven toestaan. Zie [grootten voor virtuele machines](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

  * Als u geen Premium-Ssd's (dev/test-scenario's) gebruikt, is het raadzaam om het maximum aantal gegevens schijven toe te voegen dat wordt ondersteund door de [VM-grootte](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en schijf striping te gebruiken.

* **Cache beleid**: Houd rekening met de volgende aanbevelingen voor cache beleid, afhankelijk van uw opslag configuratie.

  * Als u afzonderlijke schijven voor gegevens-en logboek bestanden gebruikt, schakelt u de optie Lees cache opslaan in op de gegevens schijven die als host fungeren voor uw gegevens bestanden en TempDB-gegevens bestanden. Dit kan leiden tot een aanzienlijk prestatie voordeel. Schakel caching niet in op de schijf die het logboek bestand bewaart omdat dit een kleine afname van de prestaties veroorzaakt.

  * Als u schijf striping in één opslag groep gebruikt, kunnen de meeste werk belastingen gebruikmaken van de Lees cache. Als u afzonderlijke opslag groepen voor de logboek-en gegevens bestanden hebt, schakelt u alleen lees cache in voor de opslag groep voor de gegevens bestanden. In bepaalde zware schrijf workloads kunnen betere prestaties worden behaald zonder caching. Dit kan alleen worden bepaald door middel van testen.

  * De bovenstaande aanbevelingen zijn van toepassing op Premium-Ssd's. Als u geen gebruik maakt van Premium Ssd's, moet u geen caching inschakelen op gegevens schijven.

  * Raadpleeg de volgende artikelen voor instructies over het configureren van schijf cache gebruik. Zie voor het klassieke (ASM)-implementatie model: [set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) en [set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Zie voor het Azure Resource Manager-implementatie model: [set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) en [set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Stop de SQL Server-service bij het wijzigen van de cache-instelling van Azure Virtual Machines schijven om te voor komen dat de data base wordt beschadigd.

* **NTFS Allocation Unit Size**: bij het format teren van de gegevens schijf wordt aanbevolen dat u een 64-KB-Allocation Unit Size gebruikt voor gegevens-en logboek bestanden en Tempdb. Als TempDB op de tijdelijke schijf wordt geplaatst (D:\ station) de prestaties die worden verkregen door gebruik te maken van dit station, wegen de nood zaak van een 64K Allocation Unit Size. 

* **Best practices voor schijf beheer**: wanneer u een gegevens schijf verwijdert of het cache type wijzigt, stopt u de SQL Server-service tijdens de wijziging. Wanneer de cache-instellingen worden gewijzigd op de besturingssysteem schijf, stopt Azure de virtuele machine, wijzigt het cache type en start de VM opnieuw op. Wanneer de cache-instellingen van een gegevens schijf zijn gewijzigd, wordt de VM niet gestopt, maar wordt de gegevens schijf losgekoppeld van de virtuele machine tijdens de wijziging en vervolgens opnieuw gekoppeld.

  > [!WARNING]
  > Als de SQL Server-service niet kan worden gestopt tijdens deze bewerkingen, kan de data base beschadigd raken.


## <a name="io-guidance"></a>I/O-richt lijnen

* De beste resultaten met Premium-Ssd's worden behaald wanneer u uw toepassing en aanvragen parallelliseren. Premium-Ssd's zijn ontworpen voor scenario's waarbij de IO-wachtrij diepte groter is dan 1, zodat u weinig of geen prestatie verbeteringen zult zien voor seriële aanvragen met één thread (zelfs als ze intensief zijn voor opslag). Dit kan bijvoorbeeld van invloed zijn op de test resultaten met één thread van prestatie analyse Programma's, zoals SQLIO.

* U kunt [database pagina compressie](/sql/relational-databases/data-compression/data-compression) gebruiken om de prestaties van I/O-intensieve workloads te verbeteren. De gegevens compressie kan echter het CPU-verbruik op de database server verhogen.

* U kunt de initialisatie van het directe bestand inschakelen om de benodigde tijd voor de eerste bestands toewijzing te verminderen. Als u gebruik wilt maken van de initialisatie van expres bestanden, verleent u het SQL Server (MSSQLserver)-service account met SE_MANAGE_VOLUME_NAME en voegt u het toe aan het beveiligings beleid **volume onderhouds taken uitvoeren** . Als u een SQL Server platform installatie kopie voor Azure gebruikt, wordt het standaard service account (NT Service\MSSQLSERVER) niet toegevoegd aan het beveiligings beleid **volume onderhouds taken uitvoeren** . Met andere woorden, de initialisatie van chat bestanden is niet ingeschakeld in een SQL Server Azure-platform installatie kopie. Nadat u het SQL Server-service account hebt toegevoegd aan het beveiligings beleid **volume onderhouds taken uitvoeren** , start u de SQL Server-service opnieuw. Er kunnen beveiligings overwegingen zijn voor het gebruik van deze functie. Zie [initialisatie van database bestanden](/sql/relational-databases/databases/database-instant-file-initialization)voor meer informatie.

* Houd er rekening mee dat **autogrow** wordt beschouwd als een onvoorziene nood gevallen voor onverwachte groei. Beheer uw gegevens niet en meld de groei per dag aan met autogrow. Als autogrow wordt gebruikt, moet u het bestand vooraf verg Roten met de schakel optie grootte.

* Zorg ervoor dat **AUTOSHRINK** is uitgeschakeld om onnodige overhead te voor komen die de prestaties negatief kan beïnvloeden.

* Verplaats alle data bases naar gegevens schijven, inclusief systeem databases. Zie [systeem databases verplaatsen](/sql/relational-databases/databases/move-system-databases)voor meer informatie.

* Verplaats SQL Server fouten logboek en traceer bestands mappen naar gegevens schijven. U kunt dit doen in SQL Server Configuration Manager door met de rechter muisknop op uw SQL Server-exemplaar te klikken en eigenschappen te selecteren. De instellingen voor het fouten logboek en het tracerings bestand kunnen worden gewijzigd op het tabblad **opstart parameters** . De map dump is opgegeven op het tabblad **Geavanceerd** . In de volgende scherm afbeelding ziet u waar de opstart parameter van het fouten logboek moet worden gezocht.

    ![Scherm opname van SQL-fouten logboek](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Standaard back-up en bestands locaties voor de data base instellen. Gebruik de aanbevelingen in dit artikel en breng de wijzigingen aan in het venster Server eigenschappen. Zie [de standaard locaties voor gegevens en logboek bestanden weer geven of wijzigen (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files)voor instructies. De volgende scherm afbeelding laat zien hoe u deze wijzigingen aanbrengt.

    ![SQL-gegevens logboek en back-upbestanden](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Schakel vergrendelde pagina's in om de i/o en eventuele wissel activiteiten te verminderen. Zie voor meer informatie [de optie voor het vergren delen van pagina's in het geheugen (Windows) inschakelen](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* Als u SQL Server 2012 gebruikt, installeert u de cumulatieve update van Service Pack 1. Deze update bevat de oplossing voor slechte prestaties bij I/O wanneer u selecteren in een tijdelijke tabel instructie in SQL Server 2012 uitvoert. Zie dit [Knowledge Base-artikel](https://support.microsoft.com/kb/2958012)voor meer informatie.

* Overweeg het comprimeren van gegevens bestanden bij het overdragen van en naar Azure.

## <a name="feature-specific-guidance"></a>Functie-specifieke richt lijnen

Sommige implementaties kunnen extra prestatie voordelen bieden met behulp van geavanceerde configuratie technieken. De volgende lijst geeft een overzicht van enkele SQL Server functies die u kunnen helpen betere prestaties te behaalen:

### <a name="back-up-to-azure-storage"></a>Back-up naar Azure Storage
Bij het uitvoeren van back-ups voor SQL Server die worden uitgevoerd in azure Virtual Machines kunt u [SQL Server back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)gebruiken. Deze functie is beschikbaar vanaf SQL Server 2012 SP1 CU2 en wordt aanbevolen voor het maken van een back-up naar de gekoppelde gegevens schijven. Wanneer u een back-up maakt/herstelt naar/van Azure Storage, volgt u de aanbevelingen op [SQL Server back-up naar aanbevolen procedures voor URL en het oplossen van problemen en het herstellen van back-ups die zijn opgeslagen in azure Storage](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). U kunt deze back-ups ook automatiseren met behulp [van automatische back-up voor SQL Server op Azure virtual machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Vóór SQL Server 2012 kunt u [SQL Server back-up naar Azure-hulp programma](https://www.microsoft.com/download/details.aspx?id=40740)gebruiken. Met dit hulp programma kunt u de back-updoorvoer verhogen met meerdere back-upstripe-doelen.

### <a name="sql-server-data-files-in-azure"></a>SQL Server gegevens bestanden in azure

Deze nieuwe functie, [SQL Server gegevens bestanden in azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure), is beschikbaar vanaf SQL Server 2014. Als SQL Server met gegevens bestanden in azure wordt uitgevoerd, worden vergelijk bare prestatie kenmerken gedemonstreerd als met behulp van Azure-gegevens schijven.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Failover-cluster exemplaar en opslag ruimten

Als u opslag ruimten gebruikt, schakelt u bij het toevoegen van knoop punten aan het cluster op de **bevestigings** pagina het selectie vakje **alle in aanmerking komende opslag toevoegen aan het cluster** uit. 

![De selectie van de in aanmerking komende opslag uitschakelen](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Als u opslagruimten gebruikt en het selectievakje **Voeg alle in aanmerking komende opslag aan het cluster toe** niet uitschakelt, worden de virtuele schijven tijdens het clusterproces ontkoppelt. Als gevolg hiervan worden ze niet weer gegeven in schijf beheer of Explorer totdat de opslag ruimten uit het cluster worden verwijderd en opnieuw zijn gekoppeld met Power shell. Er worden meerdere schijven in opslaggroepen gegroepeerd. Zie [Opslagruimten](/windows-server/storage/storage-spaces/overview) voor meer informatie.

## <a name="multiple-instances"></a>Meerdere exemplaren 

Houd rekening met de volgende aanbevolen procedures wanneer u meerdere exemplaren van SQL Server op één virtuele machine implementeert: 

- Stel het maximale server geheugen voor elk SQL Server-exemplaar in, zodat er geheugen overblijft voor het besturings systeem. Zorg ervoor dat u de geheugen beperkingen voor de SQL Server-exemplaren bijwerkt als u de hoeveelheid geheugen die aan de virtuele machine wordt toegewezen wijzigt. 
- Hebben afzonderlijke Lun's voor gegevens, logboeken en TempDB, omdat ze allemaal verschillende werkbelasting patronen hebben en u niet wilt dat ze van elkaar verschillen. 
- Test uw omgeving grondig onder zware productie, zoals workloads, om ervoor te zorgen dat IT de capaciteit van de werk belasting kan verwerken binnen uw sollicitatie-Sla's. 

Tekenen van overbelaste systemen kunnen, maar zijn niet beperkt tot, uitputting van werk thread, trage reactie tijden en/of Stallion-verdeel systeem geheugen. 




## <a name="next-steps"></a>Volgende stappen

Zie [richt lijnen voor opslag configuratie voor SQL Server op Azure virtual machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm) voor meer informatie over opslag en prestaties.

Zie [beveiligings overwegingen voor SQL Server op Azure virtual machines](security-considerations-best-practices.md)voor aanbevolen procedures voor beveiliging.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).