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
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cff67dde7cfe9e015cd25b26811410ce6e686e9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462535"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Prestatierichtlijnen voor SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dit artikel bevat richt lijnen voor het optimaliseren van SQL Server prestaties in Microsoft Azure Virtual Machines.

## <a name="overview"></a>Overzicht

Bij het uitvoeren van SQL Server op Azure Virtual Machines raden we u aan om dezelfde opties voor het afstemmen van de prestaties van de data base te blijven gebruiken die van toepassing zijn op SQL Server in on-premises server omgevingen. De prestaties van een relationele database in een openbare cloud zijn echter afhankelijk van vele factoren, zoals de grootte van een virtuele machine en de configuratie van de gegevensschijven.

[SQL Server installatie kopieën die zijn ingericht in de Azure Portal](sql-vm-create-portal-quickstart.md) algemene [Aanbevolen procedures](storage-configuration.md)voor opslag configuratie volgen. Na het inrichten kunt u overwegen om andere optimalisaties toe te passen die in dit artikel worden besproken. Baseer uw keuzes op uw werk belasting en controleer door testen.

> [!TIP]
> Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Dit artikel is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure virtual machines. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke optimalisatie nodig hebt die hieronder wordt vermeld. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.

## <a name="quick-checklist"></a>Snelle controle lijst

Hieronder vindt u een snelle controle lijst voor optimale prestaties van SQL Server op Azure Virtual Machines:

| Gebied | Optimalisaties |
| --- | --- |
| [VM-grootte](#vm-size-guidance) | -Gebruik VM-grootten met vier of meer vCPU zoals de [Standard_M8-4 MS](/../../virtual-machines/m-series), de [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)of de [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) of hoger. <br/><br/> -De grootte van virtuele machines die zijn [geoptimaliseerd voor geheugen](../../../virtual-machines/sizes-memory.md) gebruiken voor de beste prestaties van SQL Server werk belastingen. <br/><br/> -De [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), de [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) -serie, de [M-](../../../virtual-machines/m-series.md)en de [Mv2-](../../../virtual-machines/mv2-series.md) serie bieden de optimale geheugen-naar-vCore-verhouding die vereist is voor OLTP-workloads. Virtuele machines uit de M-serie bieden de hoogste geheugen-naar-vCore verhouding die is vereist voor essentiële workloads en is ook ideaal voor Data Warehouse-workloads. <br/><br/> -Er is mogelijk een hogere geheugen-naar-vCore-verhouding vereist voor bedrijfs kritieke en Data Warehouse-workloads. <br/><br/> -Maak gebruik van de Azure virtual machine Marketplace-installatie kopieën als de SQL Server instellingen en opslag opties zijn geconfigureerd voor optimale prestaties van SQL Server. <br/><br/> -De prestatie kenmerken van de doel-workload verzamelen en deze gebruiken om de juiste VM-grootte voor uw bedrijf te bepalen.|
| [Storage](#storage-guidance) | -Raadpleeg de blog [optimalisatie voor OLTP-prestaties](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)voor gedetailleerde tests van SQL Server prestaties op Azure virtual machines met TPC-E en TPC_C-benchmarks. <br/><br/> - [Premium ssd's](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) gebruiken voor de beste prijs-en prestatie voordelen. [Alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) configureren voor gegevens bestanden en geen cache voor het logboek bestand. <br/><br/> -Gebruik [Ultra schijven](../../../virtual-machines/disks-types.md#ultra-disk) als er minder dan 1 MS-geheugen latentie vereist is voor de werk belasting. Zie [migreren naar ultra Disk](storage-migrate-to-ultradisk.md) voor meer informatie. <br/><br/> -Verzamel de vereisten voor de opslag latentie voor SQL Server gegevens, logboeken en tijdelijke DB-bestanden door [de toepassing te bewaken](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) voordat u het schijf type kiest. Als < 1-MS-geheugen latenties vereist zijn, gebruikt u ultra schijven, anders gebruikt u Premium SSD. Als lage latenties alleen vereist zijn voor het logboek bestand en niet voor gegevens bestanden, moet u [de ultra Disk](../../../virtual-machines/disks-enable-ultra-ssd.md) alleen voor het logboek bestand voorzien van de vereiste IOPS en doorvoer niveaus. <br/><br/>  -Standaard opslag wordt alleen aanbevolen voor ontwikkelings-en test doeleinden of voor back-upbestanden en mag niet worden gebruikt voor productie werkbelastingen. <br/><br/> -Houd het [opslag account](../../../storage/common/storage-account-create.md) en SQL Server virtuele machine in dezelfde regio.<br/><br/> -Schakel Azure [geo-redundante opslag](../../../storage/common/storage-redundancy.md) (geo-replicatie) uit voor het opslag account.  |
| [Disks](#disks-guidance) | -Gebruik Mini maal 2 [Premium SSD-schijven](../../../virtual-machines/disks-types.md#premium-ssd) (1 voor het logboek bestand en 1 voor gegevens bestanden). <br/><br/> -Voor werk belastingen die < 1 MS IO-latentie vereisen, schakelt u schrijf versnelling voor M-serie in en overweegt u Ultra-SSD schijven voor es-en DS-serie te gebruiken. <br/><br/> - [Alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) inschakelen op de schijven die als host dienen voor de gegevens bestanden.<br/><br/> -Voeg een extra gratis IOPS/doorvoer capaciteit van 20% toe dan is vereist bij het [configureren van opslag voor SQL Server gegevens, logboeken en tempdb-bestanden](storage-configuration.md) <br/><br/> -Vermijd het gebruik van besturings systeem of tijdelijke schijven voor database opslag of logboek registratie.<br/><br/> -Schakel caching niet in op een of meer schijven die als host optreden voor het logboek bestand.  **Belang rijk**: Stop de SQL Server-service wanneer u de cache-instellingen voor een Azure virtual machines schijf wijzigt.<br/><br/> -Meerdere Azure-gegevens schijven Stripe om de opslag doorvoer te verg root.<br/><br/> -Indeling met gedocumenteerde toewijzings grootten. <br/><br/> -Sla TempDB op het lokale SSD- `D:\` station op voor essentiële SQL Server workloads (nadat u de juiste VM-grootte hebt gekozen). Als u de virtuele machine maakt op basis van de sjablonen Azure Portal of Azure Quick Start en [tijdelijke data base op de lokale schijf plaatst](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), hebt u geen verdere actie nodig. voor alle andere gevallen volgt u de stappen in de blog voor het  [gebruik van ssd's voor het opslaan van tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) om fouten te voor komen na het opnieuw opstarten. Als de capaciteit van het lokale station niet voldoende is voor de grootte van uw tijdelijke data base, plaatst u de tijdelijke data base op een opslag groep die is [striped](../../../virtual-machines/premium-storage-performance.md) op Premium SSD-schijven met [alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching)opslag. |
| [I/O](#io-guidance) |-Database pagina compressie inschakelen.<br/><br/> -Schakel de initialisatie van Instant file in voor gegevens bestanden.<br/><br/> -Beperk de automatische groei van de data base.<br/><br/> -Autoverkleinen van de data base uitschakelen.<br/><br/> -Alle data bases verplaatsen naar gegevens schijven, inclusief systeem databases.<br/><br/> -Verplaats SQL Server fouten logboek en traceer bestands mappen naar gegevens schijven.<br/><br/> -Standaard back-ups en database bestands locaties configureren.<br/><br/> - [Vergrendelde pagina's in het geheugen inschakelen](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> -Evalueer en pas de [meest recente cumulatieve updates](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) toe op de geïnstalleerde versie van SQL Server. |
| [Functie-specifiek](#feature-specific-guidance) | -Maak rechtstreeks een back-up naar Azure Blob-opslag.<br/><br/>- [Back-ups van bestands momentopnamen](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) gebruiken voor data bases die groter zijn dan 12 TB. <br/><br/>-Gebruik meerdere tijdelijke DB-bestanden, 1 bestand per kern, Maxi maal 8 bestanden.<br/><br/>-Stel het maximale server geheugen in op 90% of tot 50 GB voor het besturings systeem. <br/><br/>-Zacht NUMA inschakelen. |


<br/>
Raadpleeg de details en richt lijnen in de volgende secties voor meer informatie over *hoe* en *Waarom* u deze optimalisaties wilt maken.
<br/><br/>

## <a name="getting-started"></a>Aan de slag

Als u een nieuw SQL Server maakt op de virtuele machine van Azure en niet migreert naar een huidig bron systeem, maakt u uw nieuwe SQL Server VM op basis van de vereisten van uw leverancier.  De leveranciers vereisten voor een SQL Server virtuele machine zijn hetzelfde als wat u on-premises implementeert. 

Als u een nieuwe SQL Server VM maakt met een nieuwe toepassing die is gemaakt voor de Cloud, kunt u uw SQL Server virtuele machine eenvoudig groter of kleiner maken naarmate uw gegevens en gebruiks vereisten worden gegroeid.
Start de ontwikkel omgevingen met de D-serie, de B-serie of de Av2-serie en breid uw omgeving uit gedurende een periode. 

Het aanbevolen minimum voor een productie-OLTP-omgeving is 4 vCore, 32 GB geheugen en een geheugen-naar-vCore-verhouding van 8. Voor nieuwe omgevingen begint u met 4 vCore machines en schaalt u op 8, 16, 32 vCores of meer wanneer uw gegevens-en reken vereisten veranderen. Voor OLTP-door Voer is het doel SQL Server Vm's met 5000 IOPS voor elke vCore. 

Gebruik de SQL Server VM Marketplace-installatie kopieën met de opslag configuratie in de portal. Dit maakt het eenvoudiger om de benodigde opslag groepen te maken om de grootte, IOPS en door voer te verkrijgen die nodig zijn voor uw workloads. Het is belang rijk om SQL Server Vm's te kiezen die ondersteuning bieden voor Premium Storage en Premium Storage-caching. Zie de sectie [opslag](#storage-guidance) voor meer informatie. 

SQL Server Data Warehouse en bedrijfs kritieke omgevingen moeten vaak groter worden dan de 8-vCore verhouding. Voor middel grote omgevingen kunt u kiezen voor een verhouding van 16 kern geheugens en een verhouding van 32-tot-geheugen voor grotere data warehouse omgevingen. 

SQL Server Data Warehouse-omgevingen profiteren vaak van de parallelle verwerking van grotere machines. Daarom zijn de M-serie en de Mv2-serie Strong opties voor grotere data warehouse omgevingen.

## <a name="vm-size-guidance"></a>Richt lijnen voor VM-grootte

Gebruik de vCPU-en geheugen configuratie van uw bron machine als basis lijn voor het migreren van een huidige on-premises SQL Server Data Base naar SQL Server op virtuele machines van Azure. Breng uw kern licentie naar Azure om te profiteren van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) en bespaar op SQL Server licentie kosten.

**Micro soft raadt een geheugen-naar-vCore-verhouding van 8 aan als uitgangs punt voor productie-SQL Server workloads.** Kleinere verhoudingen zijn acceptabel voor niet-productiewerk belastingen. 

Kies een [geoptimaliseerd voor geheugen](../../../virtual-machines/sizes-memory.md), [algemeen gebruik](../../../virtual-machines/sizes-general.md), [opslag geoptimaliseerd](../../../virtual-machines/sizes-storage.md), of beperkte grootte van [vCore](../../../virtual-machines/constrained-vcpu.md) virtuele machine die optimaal is voor SQL Server prestaties op basis van uw workload (OLTP of Data Warehouse). 

### <a name="memory-optimized"></a>Geoptimaliseerd geheugen

De [grootte van virtuele machines](../../../virtual-machines/sizes-memory.md) die zijn geoptimaliseerd voor geheugen, is een primair doel voor SQL Server vm's en de aanbevolen keuze van micro soft. De virtuele machines die zijn geoptimaliseerd voor geheugen bieden betere geheugen-naar-CPU-verhoudingen en opties voor gemiddeld naar groot aantal caches. 

#### <a name="m-and-mv2-series"></a>M-en Mv2-serie

De [M-serie](../../../virtual-machines/m-series.md) biedt vCore aantallen en geheugen voor een aantal van de grootste SQL Server workloads.  

De [Mv2-serie](../../../virtual-machines/mv2-series.md) heeft het hoogste aantal vCore en het geheugen en wordt aanbevolen voor bedrijfs kritieke en Data Warehouse-workloads. Instanties van de Mv2-serie zijn geoptimaliseerd voor geheugen die ongeëvenaarde reken prestaties bieden ter ondersteuning van grote in-memory data bases en workloads met een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, grote caches en analyses in het geheugen.

Het [Standard_M64ms](../../../virtual-machines/m-series.md) heeft bijvoorbeeld 28 geheugen-naar-vCore-verhouding.

Enkele van de functies van de M-en Mv2-serie voor SQL Server prestaties zijn onder andere [Premium Storage](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching) -ondersteuning voor opslag, ondersteuning voor [hoge schijven](../../../virtual-machines/disks-enable-ultra-ssd.md) en [Schrijf versnelling](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Edsv4-serie

De [Edsv4-serie](../../../virtual-machines/edv4-edsv4-series.md) is ontworpen voor geheugenintensieve toepassingen. Deze Vm's hebben een grote lokale opslag capaciteit, een hoge IOPS van lokale schijven, Maxi maal 504 GiB aan RAM-geheugen en een verbeterde reken kracht vergeleken met de vorige Ev3/Esv3-grootten met Gen2-Vm's. Er is een bijna consistent geheugen-naar-vCore-verhouding van 8 op deze virtuele machines, wat ideaal is voor standaard SQL Server workloads. 

Deze VM-serie is ideaal voor geheugenintensieve bedrijfs toepassingen en-toepassingen die profiteren van een lage latentie en een hoge snelheid voor lokale opslag.

De virtuele machines uit de Edsv4-serie ondersteunen [Premium-opslag](../../../virtual-machines/premium-storage-performance.md)en [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>DSv2-serie 11-15

De [DSv2-serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) heeft dezelfde geheugen-en schijf configuraties als de vorige D-serie. Deze reeks heeft een consistent geheugen-naar-CPU-verhouding van 7 op alle virtuele machines. 

De [DSv2-serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) biedt ondersteuning voor [Premium Storage](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching). dit wordt ten zeerste aanbevolen voor optimale prestaties.

### <a name="general-purpose"></a>Algemeen gebruik

De grootten van [virtuele machines voor algemeen gebruik](../../../virtual-machines/sizes-general.md) zijn ontworpen om evenwichtige geheugen-naar-vCore-ratio's te bieden voor kleinere workloads op instap niveau, zoals ontwikkelen en testen, webservers en kleinere database servers. 

Vanwege de kleinere geheugen-naar-vCore-verhoudingen met de virtuele machines voor algemene doel einden, is het belang rijk om zorgvuldig op geheugen gebaseerde prestatie meter items te controleren om ervoor te zorgen dat SQL Server de benodigde buffer cache kunt ophalen. Zie de [basis lijn van geheugen prestaties](#memory) voor meer informatie. 

Omdat de eerste aanbeveling voor productie werkbelastingen een geheugen-naar-vCore-verhouding van 8 is, is de mini maal aanbevolen configuratie voor een virtuele machine voor algemeen gebruik met SQL Server 4 vCPU en 32 GB geheugen. 

#### <a name="ddsv4-series"></a>Ddsv4-serie

De [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) biedt een billijke combi natie van vCPU, geheugen en tijdelijke schijven, maar met een kleinere ondersteuning voor geheugen-naar-vCore. 

De Ddsv4 Vm's bevatten een lagere latentie en een hogere snelheid voor lokale opslag.

Deze computers zijn ideaal voor gelijktijdige SQL-en app-implementaties waarvoor snelle toegang tot tijdelijke opslag en relationele data bases is vereist. Er is een standaard geheugen-naar-vCore-verhouding van 4 voor alle virtuele machines in deze serie. 

Daarom is het raadzaam om gebruik te maken van de D8ds_v4 als de eerste virtuele machine in deze serie, met 8 vCores en 32 GB aan geheugen. De grootste computer is de D64ds_v4, met 64 vCores en 256 GB geheugen.

De virtuele machines uit de [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) ondersteunen [Premium-opslag](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> De [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) heeft niet de geheugen-naar-vCore-verhouding van 8 die wordt aanbevolen voor SQL Server workloads. Zo kunt u overwegen om deze virtuele machines alleen te gebruiken voor kleinere toepassingen en werk belastingen.

#### <a name="b-series"></a>B-serie

De grootten van de virtuele machines van de [B-serie](../../../virtual-machines/sizes-b-series-burstable.md) zijn ideaal voor werk belastingen die geen consistente prestaties nodig hebben, zoals het testen van concepten en zeer kleine toepassings-en ontwikkelings servers. 

De meeste van de grote machine grootten van de [B-Series](../../../virtual-machines/sizes-b-series-burstable.md) hebben een geheugen-naar-vCore-verhouding van 4. De grootste van deze computers is het [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) met 20 vCores en 80 GB geheugen.

Deze serie is uniek omdat de apps tijdens kantoor uren kunnen worden **gebursteerd** met bursteer bare tegoeden die variëren op basis van de grootte van de machine. 

Wanneer de tegoeden zijn uitgeput, keert de VM terug naar de prestaties van de basislijn machine.

Het voor deel van de B-serie is de besparingen die u kunt realiseren in vergelijking met de andere VM-grootten in andere reeksen, met name als u de verwerkings kracht spaarzaam nodig hebt.

Deze reeks biedt ondersteuning voor [Premium-opslag](../../../virtual-machines/premium-storage-performance.md), maar **biedt geen ondersteuning** voor [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> De [bursted B-Series](../../../virtual-machines/sizes-b-series-burstable.md) heeft niet de geheugen-naar-vCore-verhouding van 8 die wordt aanbevolen voor SQL Server workloads. Overweeg het gebruik van deze virtuele machines voor kleinere toepassingen, webservers en ontwikkel werkbelastingen.

#### <a name="av2-series"></a>Av2-serie

De virtuele machines uit de [Av2-serie](../../../virtual-machines/av2-series.md) zijn het meest geschikt voor werk belastingen op instap niveau, zoals ontwikkelen en testen, webservers met weinig verkeer, kleine tot middel grote app-data bases en proof-of-concepten.

Alleen de [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 VCores en 16GBs van het geheugen), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores en 32GBs van het geheugen) en de [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores en 64GBs aan geheugen) hebben een goede geheugen-naar-vCore-verhouding van 8 voor deze belangrijkste drie virtuele machines. 

Deze virtuele machines zijn goede opties voor kleinere ontwikkel-en test SQL Server machines. 

De 8 vCore- [Standard_A8m_v2](../../../virtual-machines/av2-series.md) is mogelijk ook een goede optie voor kleine toepassingen en webservers.

> [!NOTE] 
> De Av2-serie biedt geen ondersteuning voor Premium-opslag en is daarom niet aanbevolen voor productie SQL Server workloads, zelfs met de virtuele machines met een geheugen-naar-vCore-verhouding van 8.

### <a name="storage-optimized"></a>Geoptimaliseerde opslag

De [grootten voor opslag geoptimaliseerde vm's](../../../virtual-machines/sizes-storage.md) zijn bedoeld voor specifieke use cases. Deze virtuele machines zijn specifiek ontworpen met geoptimaliseerde schijf doorvoer en IO. Deze serie van virtuele machines is bedoeld voor big data scenario's, gegevens opslag en grote transactionele data bases. 

#### <a name="lsv2-series"></a>Lsv2-serie

De [Lsv2-serie](../../../virtual-machines/lsv2-series.md) biedt een hoge door Voer, lage latentie en lokale NVMe-opslag. De virtuele machines uit de Lsv2-serie zijn geoptimaliseerd voor het gebruik van de lokale schijf op het knoop punt dat rechtstreeks aan de virtuele machine is gekoppeld, in plaats van duurzame gegevens schijven te gebruiken. 

Deze virtuele machines zijn sterke opties voor big data-, Data Warehouse-, rapportage-en ETL-workloads. De hoge door Voer en IOPs van de lokale NVMe-opslag is een goede use-case voor het verwerken van bestanden die worden geladen in uw data base en andere scenario's waarin de bron gegevens opnieuw kunnen worden gemaakt op basis van het bron systeem of andere opslag plaatsen, zoals Azure Blob Storage of Azure Data Lake. [Lsv2-serie](../../../virtual-machines/lsv2-series.md) Vm's kunnen de schijf prestaties ook Maxi maal 30 minuten per keer opruimen.

De grootte van deze virtuele machines van 8 tot 80 vCPU met 8 GiB geheugen per vCPU en voor elke 8 Vcpu's is 1,92 TB van NVMe SSD. Dit betekent dat er voor de grootste virtuele machine van deze serie een [L80s_v2](../../../virtual-machines/lsv2-series.md)is. er is 80 vCPU-en 640-geheugenpen met 10X 1.92 TB NVMe-opslag.  Er is een consistent geheugen-naar-vCore-verhouding van 8 op al deze virtuele machines.

De NVMe-opslag is tijdelijk wat betekent dat gegevens op deze schijven verloren gaan als u de virtuele machine opnieuw opstart.

De Lsv2-en LS-reeks bieden ondersteuning voor [Premium-opslag](../../../virtual-machines/premium-storage-performance.md), maar niet voor Premium Storage-caching. Het maken van een lokale cache om IOPs te verhogen, wordt niet ondersteund. 

> [!WARNING]
> Het opslaan van uw gegevens bestanden op de tijdelijke NVMe-opslag kan leiden tot verlies van gegevens wanneer de toewijzing van de virtuele machine ongedaan wordt gemaakt. 

### <a name="constrained-vcores"></a>Beperkte vCores

Hoge prestaties van SQL Server werk belastingen hebben vaak een grotere hoeveelheid geheugen, i/o en door voer zonder het hogere aantal vCore nodig. 

De meeste OLTP-workloads zijn toepassings databases die worden aangedreven door grote aantallen kleinere trans acties. Met OLTP-workloads wordt slechts een kleine hoeveelheid gegevens gelezen of gewijzigd, maar de volumes van trans acties die door het aantal gebruikers worden aangestuurd, zijn veel hoger. Het is belang rijk om de SQL Server geheugen beschikbaar te stellen voor het plannen van de cache, recent gebruikte gegevens op te slaan voor prestaties en ervoor te zorgen dat fysieke Lees bewerkingen snel in het geheugen kunnen worden gelezen. 

Deze OLTP-omgevingen hebben een grotere hoeveelheid geheugen, snelle opslag en de I/O-band breedte nodig om optimaal te kunnen werken. 

Azure biedt VM-grootten met [beperkte vCPU-aantallen](../../../virtual-machines/constrained-vcpu.md), zodat dit prestatie niveau kan worden gehandhaafd zonder de hogere SQL Server licentie kosten. 

Dit helpt de licentie kosten te beheren door de beschik bare vCores te verminderen en tegelijkertijd hetzelfde geheugen, dezelfde opslag en I/O-band breedte van de bovenliggende virtuele machine te behouden.

Het aantal vCPU kan worden beperkt tot een half tot één kwar taal van de oorspronkelijke VM-grootte. Het verminderen van de vCores die beschikbaar is voor de virtuele machine, leidt tot hogere geheugen-naar-vCore-verhoudingen.

Deze nieuwe VM-grootten hebben een achtervoegsel dat het aantal actieve Vcpu's opgeeft, zodat het gemakkelijker te identificeren is. 

De [M64-32MS](../../../virtual-machines/constrained-vcpu.md) vereist bijvoorbeeld alleen licenties van 32 SQL Server vCores met het geheugen, de io en de door Voer van de [M64ms](../../../virtual-machines/m-series.md) en de [M64-16 MS](../../../virtual-machines/constrained-vcpu.md) vereist een licentie van 16 vCores.  Hoewel de [M64-16 MS](../../../virtual-machines/constrained-vcpu.md) een kwart van de SQL Server licentie kosten van de M64ms heeft, zijn de reken kosten van de virtuele machine hetzelfde.

> [!NOTE] 
> - Gemiddeld tot grote data warehouse-werk belastingen kunnen nog steeds profiteren van [beperkte vCore-vm's](../../../virtual-machines/constrained-vcpu.md), maar data warehouse-workloads worden vaak gekenmerkt door minder gebruikers en processen die grotere hoeveel heden gegevens adresseren via query abonnementen die parallel worden uitgevoerd. 
> - De reken kosten, met inbegrip van de licentie voor het besturings systeem, blijven hetzelfde als de bovenliggende virtuele machine. 

## <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

Raadpleeg de blog [optimalisatie OLTP-prestaties](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)voor gedetailleerde tests van SQL Server prestaties op Azure virtual machines met TPC-E en TPC-C-benchmarks. 

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

Plaats TempDB op het lokale SSD `D:\` -station voor essentiële SQL Server workloads (nadat u de juiste VM-grootte hebt gekozen). Als u de virtuele machine maakt op basis van de sjablonen Azure Portal of Azure Quick Start en [tijdelijke data base op de lokale schijf plaatst](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), hebt u geen verdere actie nodig. voor alle andere gevallen volgt u de stappen in de blog voor het  [gebruik van ssd's voor het opslaan van tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) om fouten te voor komen na het opnieuw opstarten. Als de capaciteit van het lokale station niet voldoende is voor de grootte van uw tijdelijke data base, plaatst u de tijdelijke data base op een opslag groep die is [striped](../../../virtual-machines/premium-storage-performance.md) op Premium SSD-schijven met [alleen-lezen cache](../../../virtual-machines/premium-storage-performance.md#disk-caching)opslag.

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

* **NTFS Allocation Unit Size**: bij het format teren van de gegevens schijf wordt aanbevolen dat u een 64-KB-Allocation Unit Size gebruikt voor gegevens-en logboek bestanden en Tempdb. Als TempDB op de tijdelijke schijf wordt geplaatst (D:\ station) de prestaties die worden verkregen door gebruik te maken van dit station, wegen de nood zaak van een Allocation Unit Size van 64 KB. 

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



## <a name="collect-performance-baseline"></a>Prestaties van basis lijn verzamelen

Voor een meer prescriptieve aanpak verzamelt u prestatie meter items met PerfMon/LogMan en Capture SQL Server wacht statistieken om de algemene belasting en mogelijke knel punten van de bron omgeving beter te begrijpen. 

Begin met het verzamelen van de CPU, het geheugen, de [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), de [door Voer](../../../virtual-machines/premium-storage-performance.md#throughput)en [latentie](../../../virtual-machines/premium-storage-performance.md#latency) van de bron-workload op piek tijden na de [controle lijst](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)voor de prestaties van de toepassing. 

Verzamel gegevens tijdens piek uren, zoals werk belastingen tijdens uw dagelijkse werk belasting, maar ook andere High Load-processen, zoals het beëindigen van de dagelijkse verwerking en weekend ETL-workloads. Overweeg om uw resources te verg Roten voor ongewoon veel werk belastingen, zoals het beëindigen van een kwart verwerking, en pas de schaal aan nadat de werk belasting is voltooid. 

Gebruik de analyse van prestaties om de [VM-grootte](../../../virtual-machines/sizes-memory.md) te selecteren die kan worden geschaald naar de prestatie vereisten van uw werk belasting.


### <a name="iops-and-throughput"></a>IOPS en door Voer

SQL Server prestaties zijn sterk afhankelijk van het I/O-subsysteem. Tenzij uw data base in het fysieke geheugen past, brengt SQL Server voortdurend database pagina's uit en uit de buffer groep. De gegevens bestanden voor SQL Server moeten anders worden behandeld. Toegang tot logboek bestanden is opeenvolgend, behalve wanneer een trans actie moet worden teruggedraaid wanneer gegevens bestanden, inclusief TempDB, wille keurig worden geopend. Als u een langzaam I/O-subsysteem hebt, kunnen uw gebruikers prestatie problemen ondervinden, zoals trage reactie tijden en taken die niet worden voltooid als gevolg van time-outs. 

De virtuele machines van Azure Marketplace hebben logboek bestanden op een fysieke schijf die standaard gescheiden is van de gegevens bestanden. Het aantal TempDB-gegevens bestanden en de grootte voldoen aan aanbevolen procedures en zijn gericht op de tijdelijke D:/ station.. 

De volgende prestatie meter items kunnen helpen bij het valideren van de i/o-door Voer die vereist is voor uw SQL Server: 
* **\LogicalDisk\Disk Lees bewerkingen per seconde** (IOPS lezen en schrijven)
* **\LogicalDisk\Disk schrijf bewerkingen per seconde** (IOPS lezen en schrijven) 
* **\LogicalDisk\Disk bytes per seconde** (doorvoer vereisten voor de gegevens, het logboek en tempdb-bestanden)

Met behulp van IOPS-en doorvoer vereisten op piek niveaus kunt u de VM-grootten evalueren die overeenkomen met de capaciteit van uw metingen. 

Als voor uw werk belasting 20 K Lees-IOPS en 10K-IOPS zijn vereist, kunt u E16s_v3 kiezen (met Maxi maal 32 KB in cache opgeslagen en 25600 niet-opgeslagen IOPS) of M16_s (met Maxi maal 20 KB in cache opgeslagen en niet-opgeslagen IOPS) met 2 P30 schijven die zijn gestripd met opslag ruimten. 

Zorg ervoor dat u zowel de door Voer als de IOPS-vereisten van de werk belasting begrijpt, aangezien Vm's verschillende schaal limieten hebben voor IOPS en door voer.

### <a name="memory"></a>Geheugen

Volg zowel het externe geheugen dat door het besturings systeem wordt gebruikt als het geheugen dat intern door SQL Server wordt gebruikt. Het identificeren van de belasting van een van beide onderdelen helpt bij het formaat van virtuele machines en het identificeren van kansen voor het afstemmen. 

De volgende prestatie meter items kunnen helpen bij het valideren van de geheugen status van een SQL Server virtuele machine: 
* [\Memory\Available mega bytes](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: geheugen Manager\Target server geheugen (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: geheugen Manager\Total server geheugen (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy schrijf bewerkingen per seconde](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Bufferbeheer\gelezen Life verwachting](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Berekenen/verwerken

Compute in azure wordt anders beheerd dan on-premises. On-premises servers zijn voor het laatst enkele jaren gebouwd zonder een upgrade vanwege de overhead van het management en de kosten voor het verkrijgen van nieuwe hardware. Door virtualisatie worden enkele van deze problemen verholpen, maar toepassingen zijn geoptimaliseerd voor het meest voor deel van de onderliggende hardware, wat betekent dat een belang rijke wijziging in het Resource verbruik een herverdeling van de gehele fysieke omgeving vereist. 

Dit is geen uitdaging in azure waarbij een nieuwe virtuele machine in een andere reeks hardware en zelfs in een andere regio gemakkelijk te vinden is. 

In azure wilt u zo veel mogelijk gebruikmaken van de resources van de virtuele machines, daarom moeten virtuele machines van Azure zodanig worden geconfigureerd dat de gemiddelde CPU zo hoog mogelijk wordt, zonder dat dit van invloed is op de werk belasting. 

De volgende prestatie meter items kunnen helpen bij het valideren van de reken status van een SQL Server virtuele machine:
* **\Processor Information (_Total) \% processor tijd**
* **\Process (SQLSERVR) \% processor tijd**

> [!NOTE] 
> In het ideale geval kunt u zich richten op het gebruik van 80% van uw reken kracht, met pieken boven 90%, maar niet meer dan 100% gedurende langere tijd. Het is belang rijk dat u alleen de berekenings behoeften van de toepassing inricht en vervolgens wilt schalen of verlagen wanneer het bedrijf dit vereist. 

## <a name="next-steps"></a>Volgende stappen

Zie [beveiligings overwegingen voor SQL Server op Azure virtual machines](security-considerations-best-practices.md)voor aanbevolen procedures voor beveiliging.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).
