---
title: 'VM-grootte: Best practices voor prestaties & richtlijnen'
description: Biedt richtlijnen voor VM-grootte en best practices voor het optimaliseren van de prestaties van uw SQL Server virtuele Azure-machine (VM).
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 88adef7ea50744f913780d99594ce3baadade84b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600893"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM-grootte: Best practices voor prestaties voor SQL Server azure-VM's
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u richtlijnen voor VM-grootte aan de hand van een reeks best practices en richtlijnen voor het optimaliseren van de prestaties voor uw SQL Server op Azure Virtual Machines (VM's).

Er is doorgaans een balans tussen optimaliseren voor kosten en optimaliseren voor prestaties. Deze reeks best practices voor prestaties is gericht op het verkrijgen van de *beste* prestaties voor SQL Server azure-Virtual Machines. Als uw workload minder veeleisende is, hebt u mogelijk niet elke aanbevolen optimalisatie nodig. Houd rekening met uw prestatiebehoeften, kosten en workloadpatronen wanneer u deze aanbevelingen evalueert.


## <a name="checklist"></a>Controlelijst

Bekijk de volgende controlelijst voor een kort overzicht van de best practices voor VM-grootte die in de rest van het artikel gedetailleerder worden behandeld: 

- Gebruik VM-grootten met 4 of meer vCPU's, zoals [de Standard_M8-4 ms,](/azure/virtual-machines/m-series)de [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)of de [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) of hoger. 
- Gebruik [voor geheugen geoptimaliseerde](../../../virtual-machines/sizes-memory.md) grootten van virtuele machines voor de beste prestaties van SQL Server workloads. 
- De [DSv2 11-15,](../../../virtual-machines/dv2-dsv2-series-memory.md) [Edsv4-serie,](../../../virtual-machines/edv4-edsv4-series.md) de [M-](/azure/virtual-machines/m-series)en [de Mv2-serie](../../../virtual-machines/mv2-series.md) bieden de optimale geheugen-naar-vCore-verhouding die is vereist voor OLTP-workloads. Beide VM's uit de M-serie bieden de hoogste geheugen-naar-vCore-verhouding die vereist is voor essentiële workloads en zijn ook ideaal voor datawarehouse-workloads. 
- Overweeg een hogere geheugen-naar-vCore-verhouding voor bedrijfskritieke workloads en datawarehouse-workloads. 
- Maak gebruik van de Azure Virtual Machine Marketplace-SQL Server instellingen en opslagopties zijn geconfigureerd voor optimale SQL Server prestaties. 
- Verzamel de prestatiekenmerken van de doelworkload en gebruik deze om de juiste VM-grootte voor uw bedrijf te bepalen.

Zie de uitgebreide controlelijst voor best practices voor prestaties als u de controlelijst voor VM-grootte met de andere [wilt vergelijken.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Overzicht

Wanneer u een virtuele azure SQL Server machine maakt, moet u zorgvuldig overwegen welk type workload nodig is. Als u een bestaande omgeving migreert, verzamelt u een [prestatiebasislijn](performance-guidelines-best-practices-collect-baseline.md) om te bepalen wat de vereisten SQL Server azure-VM's zijn. Als dit een nieuwe VM is, maakt u uw nieuwe virtuele SQL Server op basis van de vereisten van uw leverancier. 

Als u een nieuwe SQL Server-VM maakt met een nieuwe toepassing die is gebouwd voor de cloud, kunt u uw SQL Server-VM eenvoudig in grootte aanpassen naarmate uw gegevens- en gebruiksvereisten veranderen.
Start de ontwikkelomgevingen met de D-serie, B-serie of Av2-serie met een lagere laag en laat uw omgeving na een periode groeien. 

Gebruik de marketplace SQL Server's voor VM's met de opslagconfiguratie in de portal. Hierdoor kunt u gemakkelijker de opslaggroepen maken die nodig zijn om de grootte, IOPS en doorvoer te krijgen die nodig zijn voor uw workloads. Het is belangrijk om te kiezen SQL Server's die ondersteuning bieden voor Premium Storage en Premium Storage-caching. Zie het [opslagartikel](performance-guidelines-best-practices-storage.md) voor meer informatie. 

Het aanbevolen minimum voor een OLTP-productieomgeving is 4 vCores, 32 GB geheugen en een memory-to-vCore-verhouding van 8. Voor nieuwe omgevingen begint u met 4 vCore-machines en schaalt u naar 8, 16, 32 vCores of meer wanneer uw gegevens- en rekenvereisten veranderen. Voor OLTP-doorvoer moet u SQL Server VM's met 5000 IOPS voor elke vCore. 

SQL Server datawarehouse en bedrijfskritieke omgevingen moeten vaak verder worden geschaald dan de verhouding van 8 geheugen-naar-vCores. Voor middelgrote omgevingen kunt u een verhouding van 16 geheugen-naar-vCores en een verhouding van 32 geheugen-naar-vCore kiezen voor grotere datawarehouse-omgevingen. 

SQL Server datawarehouseomgevingen profiteren vaak van de parallelle verwerking van grotere machines. Daarom zijn de M-serie en de Mv2-serie sterke opties voor grotere datawarehouse-omgevingen.

Gebruik de vCPU- en geheugenconfiguratie van uw bronmachine als basislijn voor het migreren van een huidige on-premises SQL Server-database naar SQL Server virtuele Azure-machines. Breng uw basislicentie naar Azure om te profiteren van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) en te besparen op SQL Server licentiekosten.

## <a name="memory-optimized"></a>Geoptimaliseerd geheugen

De [voor geheugen geoptimaliseerde VM-grootten](../../../virtual-machines/sizes-memory.md) zijn een primair doel voor SQL Server virtuele machines en de aanbevolen keuze van Microsoft. De voor geheugen geoptimaliseerde virtuele machines bieden sterkere geheugen-tot-CPU-verhoudingen en middelgrote tot grote cacheopties. 

### <a name="m-mv2-and-mdsv2-series"></a>M-, Mv2- en Mdsv2-serie

De [M-serie biedt](/azure/virtual-machines/m-series) vCore-tellingen en geheugen voor een aantal van de SQL Server werkbelastingen.  

De [Mv2-serie](../../../virtual-machines/mv2-series.md) heeft het hoogste aantal vCores en het hoogste geheugen en wordt aanbevolen voor essentiële workloads en datawarehouse-workloads. Exemplaren uit de Mv2-serie zijn voor geheugen geoptimaliseerde VM-grootten die ongeëvenaarde rekenprestaties bieden ter ondersteuning van grote in-memory databases en workloads met een hoge geheugen-cpu-verhouding die perfect is voor relationele databaseservers, grote caches en in-memory analyses.

De [Standard_M64ms](/azure/virtual-machines/m-series) heeft bijvoorbeeld een verhouding van 28 geheugen-naar-vCores.

[Mdsv2 Medium Memory-serie](../../..//virtual-machines/msv2-mdsv2-series.md) is een nieuwe M-serie die momenteel als [preview-versie](https://aka.ms/Mv2MedMemoryPreview) beschikbaar is en die een reeks virtuele Azure-machines op M-serieniveau biedt met een aanbieding voor het geheugen van de gemiddelde laag. Deze computers zijn zeer geschikt voor SQL Server workloads met minimaal 10 geheugen-naar-vCore-ondersteuning tot 30.

Enkele van de functies van de M- en Mv2-serie die aantrekkelijk zijn voor SQL Server-prestaties zijn ondersteuning voor [Premium Storage](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage-caching,](../../../virtual-machines/premium-storage-performance.md#disk-caching) ondersteuning voor [ultraschijven](../../../virtual-machines/disks-enable-ultra-ssd.md) en [schrijfversnelling.](../../../virtual-machines/how-to-enable-write-accelerator.md)

### <a name="edsv4-series"></a>Edsv4-serie

De [Edsv4-serie](../../../virtual-machines/edv4-edsv4-series.md) is ontworpen voor geheugenintensieve toepassingen. Deze VM's hebben een grote SSD-capaciteit voor lokale opslag, sterke lokale schijf-IOPS, tot 504 GiB RAM-geheugen. Er is een bijna consistente 8 GiB aan geheugen per vCore voor de meeste van deze virtuele machines, wat ideaal is voor SQL Server standaardworkloads. 

Er is een nieuwe virtuele machine in deze groep met [de Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) die 80 vCores, 504 GB geheugen biedt, met een geheugen-naar-vCore-verhouding van 6. Deze virtuele machine is belangrijk omdat deze [is](../../../virtual-machines/isolation.md) geïsoleerd. Dit betekent dat deze gegarandeerd de enige virtuele machine is die op de host wordt uitgevoerd en daarom is geïsoleerd van andere workloads van klanten. Dit heeft een geheugen-naar-vCore-verhouding die lager is dan wat wordt aanbevolen voor SQL Server, dus deze moet alleen worden gebruikt als isolatie is vereist.

De virtuele machines uit de Edsv4-serie ondersteunen [Premium Storage](../../../virtual-machines/premium-storage-performance.md)en [Premium Storage-caching.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

### <a name="dsv2-series-11-15"></a>DSv2-serie 11-15

De [DSv2-serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) heeft dezelfde geheugen- en schijfconfiguraties als de vorige D-serie. Deze reeks heeft een consistente geheugen-tot-CPU-verhouding van 7 voor alle virtuele machines. Dit is de kleinste van de serie die is geoptimaliseerd voor geheugen en is een goede goedkope optie voor SQL Server workloads.

De [DSv2-serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ondersteunt [Premium Storage](../../../virtual-machines/premium-storage-performance.md) en Premium [Storage-caching,](../../../virtual-machines/premium-storage-performance.md#disk-caching)wat sterk wordt aanbevolen voor optimale prestaties.

## <a name="general-purpose"></a>Algemeen doel

De [grootten van virtuele machines](../../../virtual-machines/sizes-general.md) voor algemeen gebruik zijn ontworpen om evenwichtige geheugen-naar-vCore-verhoudingen te bieden voor kleinere werkbelastingen op invoerniveau, zoals ontwikkeling en testen, webservers en kleinere databaseservers. 

Vanwege de kleinere geheugen-naar-vCore-verhoudingen met de virtuele machines voor algemeen gebruik, is het belangrijk om de prestatiemeters op basis van het geheugen zorgvuldig te bewaken om ervoor te zorgen dat SQL Server het geheugen van de buffercache kan krijgen dat nodig is. Zie [basislijn voor geheugenprestaties](performance-guidelines-best-practices-collect-baseline.md#memory) voor meer informatie. 

Omdat de startaanbeveling voor productieworkloads een geheugen-naar-vCore-verhouding van 8 is, is de minimaal aanbevolen configuratie voor een VM voor algemeen gebruik met SQL Server 4 vCPU en 32 GB geheugen. 

### <a name="ddsv4-series"></a>Ddsv4-serie

De [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) biedt een goede combinatie van vCPU, geheugen en tijdelijke schijf, maar met kleinere geheugen-naar-vCore-ondersteuning. 

De Ddsv4-VM's bevatten een lagere latentie en lokale opslag met hogere snelheid.

Deze machines zijn ideaal voor side-by-side SQL- en app-implementaties waarvoor snelle toegang tot tijdelijke opslag en relationele databases voor afdelingen is vereist. Er is een standaardgeheugen-naar-vCore-verhouding van 4 voor alle virtuele machines in deze reeks. 

Daarom is het raadzaam om de D8ds_v4 te gebruiken als de starter virtuele machine in deze reeks, die 8 vCores en 32 GB geheugen heeft. De grootste machine is de D64ds_v4, met 64 vCores en 256 GB geheugen.

De [virtuele machines uit de Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) bieden ondersteuning voor Premium Storage [en](../../../virtual-machines/premium-storage-performance.md) [Premium Storage-caching.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE]
> De [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) heeft niet de geheugen-naar-vCore-verhouding van 8 die wordt aanbevolen voor SQL Server workloads. Als zodanig kunt u overwegen deze virtuele machines alleen te gebruiken voor kleinere toepassings- en ontwikkelingsworkloads.

### <a name="b-series"></a>B-serie

De grootten van virtuele machines uit de [B-serie](../../../virtual-machines/sizes-b-series-burstable.md) zijn ideaal voor workloads die geen consistente prestaties nodig hebben, zoals proof of concept en zeer kleine toepassings- en ontwikkelingsservers. 

De meeste [burstable grootten van](../../../virtual-machines/sizes-b-series-burstable.md) virtuele machines uit de B-serie hebben een geheugen-naar-vCore-verhouding van 4. De grootste van deze machines is [de Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) met 20 vCores en 80 GB geheugen.

Deze reeks is uniek omdat de apps de mogelijkheid hebben om te **bursten** tijdens bedrijfsuren met burstable tegoeden die variëren op basis van de grootte van de machine. 

Wanneer het tegoed is uitgeput, keert de VM terug naar de prestaties van de basislijnmachine.

Het voordeel van de B-serie is de rekenbesparing die u kunt behalen in vergelijking met de andere VM-grootten in andere reeksen, met name als u de verwerkingskracht gedurende de dag spaarzaam nodig hebt.

Deze serie biedt ondersteuning [voor Premium Storage,](../../../virtual-machines/premium-storage-performance.md)maar **biedt geen ondersteuning voor** premium [storage-caching.](../../../virtual-machines/premium-storage-performance.md#disk-caching)

> [!NOTE] 
> De [burstable B-serie](../../../virtual-machines/sizes-b-series-burstable.md) heeft niet de memory-to-vCore-verhouding van 8 die wordt aanbevolen voor SQL Server workloads. Als zodanig kunt u deze virtuele machines alleen gebruiken voor kleinere toepassingen, webservers en ontwikkelworkloads.

### <a name="av2-series"></a>Av2-serie

De [VM's](../../../virtual-machines/av2-series.md) van de Av2-serie zijn het meest geschikt voor workloads op invoerniveau, zoals ontwikkeling en testen, webservers met weinig verkeer, kleine tot middelgrote app-databases en proof-of-concepts.

Alleen de [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 vCores en 16 GB geheugen), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores en 32 GB geheugen) en de [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores en 64 GB geheugen) hebben een goede geheugen-naar-vCore-verhouding van 8 voor deze drie virtuele machines. 

Deze virtuele machines zijn beide goede opties voor kleinere ontwikkel- en SQL Server machines. 

De 8 [vCore Standard_A8m_v2](../../../virtual-machines/av2-series.md) zijn mogelijk ook een goede optie voor kleine toepassingen en webservers.

> [!NOTE] 
> De Av2-serie biedt geen ondersteuning voor Premium Storage en wordt daarom niet aanbevolen voor productie-SQL Server-workloads, zelfs niet voor virtuele machines met een geheugen-naar-vCore-verhouding van 8.

## <a name="storage-optimized"></a>Geoptimaliseerde opslag

De [voor opslag geoptimaliseerde VM-grootten](../../../virtual-machines/sizes-storage.md) zijn voor specifieke gebruiksgevallen. Deze virtuele machines zijn speciaal ontworpen met geoptimaliseerde schijfdoorvoer en IO. 

### <a name="lsv2-series"></a>Lsv2-serie

De [Lsv2-serie](../../../virtual-machines/lsv2-series.md) biedt een hoge doorvoer, lage latentie en lokale NVMe-opslag. De VM's uit de Lsv2-serie zijn geoptimaliseerd voor het gebruik van de lokale schijf op het knooppunt dat rechtstreeks aan de VM is gekoppeld, in plaats van duurzame gegevensschijven te gebruiken. 

Deze virtuele machines zijn sterke opties voor big data, datawarehouse, rapportage en ETL-workloads. De hoge doorvoer en IOPS van de lokale NVMe-opslag is een goede use-case voor het verwerken van bestanden die in uw database worden geladen en andere scenario's waarin de gegevens opnieuw kunnen worden gemaakt vanuit het bronsysteem of andere opslagplaatsen, zoals Azure Blob Storage of Azure Data Lake. [Lsv2-serie](../../../virtual-machines/lsv2-series.md) VM's kunnen ook hun schijfprestaties maximaal 30 minuten tegelijk bursten.

Deze virtuele machines hebben een grootte van 8 tot 80 vCPU's met 8 GiB geheugen per vCPU en voor elke 8 vCPU's is er 1,92 TB NVMe SSD. Dit betekent dat voor de grootste VM van deze serie, de [L80s_v2,](../../../virtual-machines/lsv2-series.md)80 vCPU en 640 BiB geheugen is met 10x1,92 TB NVMe-opslag.  Er is een consistente geheugen-naar-vCore-verhouding van 8 voor al deze virtuele machines.

De NVMe-opslag is kortstondig, wat betekent dat de gegevens op deze schijven verloren gaan als u de toewijzing van uw virtuele machine niet meer toekent of als deze voor serviceherstel naar een andere host wordt verplaatst.

De Lsv2- en Ls-serie bieden ondersteuning [voor Premium Storage,](../../../virtual-machines/premium-storage-performance.md)maar niet voor opslag in de opslag in de premium-opslag. Het maken van een lokale cache om IOPS te verhogen, wordt niet ondersteund. 

> [!WARNING]
> Het opslaan van uw gegevensbestanden in de tijdelijke NVMe-opslag kan leiden tot gegevensverlies wanneer de toewijzing van de VM wordt verwijderd. 

## <a name="constrained-vcores"></a>Beperkte vCores

Voor SQL Server werkbelastingen met hoge uitvoering zijn vaak grotere hoeveelheden geheugen, I/O en doorvoer nodig zonder het hogere aantal vCores. 

De meeste OLTP-workloads zijn toepassingsdatabases die worden aangestuurd door een groot aantal kleinere transacties. Met OLTP-workloads wordt slechts een kleine hoeveelheid gegevens gelezen of gewijzigd, maar de volumes van transacties die worden aangestuurd door het aantal gebruikers zijn veel hoger. Het is belangrijk dat het SQL Server beschikbaar is voor cacheplannen, recent opgeslagen gegevens voor betere prestaties en ervoor zorgen dat fysieke leesgegevens snel in het geheugen kunnen worden gelezen. 

Deze OLTP-omgevingen hebben meer geheugen, snelle opslag en de I/O-bandbreedte nodig om optimaal te presteren. 

Om dit prestatieniveau te behouden zonder de hogere SQL Server licentiekosten, biedt Azure VM-grootten met beperkte [vCPU-tellingen.](../../../virtual-machines/constrained-vcpu.md) 

Dit helpt de licentiekosten te beheersen door de beschikbare vCores te verlagen terwijl dezelfde geheugen-, opslag- en I/O-bandbreedte van de bovenliggende virtuele machine behouden blijft.

Het aantal vCPU's kan worden beperkt tot de helft tot een kwart van de oorspronkelijke VM-grootte. Door de vCores die beschikbaar zijn voor de virtuele machine te verminderen, worden hogere geheugen-naar-vCore-ratio's bereikt, maar blijven de rekenkosten gelijk.

Deze nieuwe VM-grootten hebben een achtervoegsel dat het aantal actieve vCCPUs aangeeft, zodat ze gemakkelijker te identificeren zijn. 

Voor [M64-32ms](../../../virtual-machines/constrained-vcpu.md) zijn bijvoorbeeld slechts 32 SQL Server vCores met het geheugen, de I/O en de doorvoer van de [M64ms](/azure/virtual-machines/m-series) vereist. Voor [de M64-16ms](../../../virtual-machines/constrained-vcpu.md) is alleen licentieverlening van 16 vCores vereist.  Hoewel de [M64-16ms](../../../virtual-machines/constrained-vcpu.md) een kwartaal van de SQL Server-licentiekosten van de M64ms heeft, zijn de rekenkosten van de virtuele machine hetzelfde.

> [!NOTE] 
> - Gemiddelde tot grote datawarehouse-workloads kunnen nog steeds profiteren van beperkte [vCore-VM's,](../../../virtual-machines/constrained-vcpu.md)maar datawarehouse-workloads worden doorgaans gekenmerkt door minder gebruikers en processen die grotere hoeveelheden gegevens verwerken via queryplannen die parallel worden uitgevoerd. 
> - De rekenkosten, waaronder licentieverlening voor het besturingssysteem, blijven gelijk aan die van de bovenliggende virtuele machine. 



## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen in deze reeks voor meer informatie:
- [Snelle controlelijst](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Basislijn verzamelen](performance-guidelines-best-practices-collect-baseline.md)

Zie Beveiligingsoverwegingen voor [beveiligingsoverwegingen voor SQL Server in Azure Virtual Machines.](security-considerations-best-practices.md)

Bekijk andere SQL Server virtual machine-artikelen op [SQL Server over Azure Virtual Machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md). 
