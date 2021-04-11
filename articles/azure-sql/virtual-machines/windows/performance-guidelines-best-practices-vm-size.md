---
title: 'VM-grootte: Aanbevolen procedures voor prestaties & richt lijnen'
description: Voorziet in richt lijnen voor VM-grootte en aanbevolen procedures voor het optimaliseren van de prestaties van uw SQL Server op Azure virtual machine (VM).
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
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572408"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM-grootte: Aanbevolen procedures voor het SQL Server op virtuele machines in azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u een aantal aanbevolen procedures en richt lijnen voor de VM-grootte om de prestaties van uw SQL Server op Azure Virtual Machines (Vm's) te optimaliseren.

Er is doorgaans een afweging tussen het optimaliseren van kosten en het optimaliseren van de prestaties. Deze reeks best practices voor prestaties is gericht op het ophalen van de *beste* prestaties voor SQL Server op Azure virtual machines. Als uw werk belasting minder veeleisend is, is het mogelijk dat u niet elke aanbevolen optimalisatie nodig hebt. Houd rekening met de prestatie behoeften, kosten en werkbelasting patronen wanneer u deze aanbevelingen evalueert.


## <a name="checklist"></a>Controlelijst

Bekijk de volgende controle lijst voor een beknopt overzicht van de aanbevolen procedures voor VM-grootte die in de rest van het artikel in meer detail worden behandeld: 

- Gebruik VM-grootten met vier of meer vCPU zoals de [Standard_M8-4 MS](/../../virtual-machines/m-series), de [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)of de [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) of hoger. 
- De grootte van virtuele machines die zijn [geoptimaliseerd voor geheugen](../../../virtual-machines/sizes-memory.md) gebruiken voor de beste prestaties van SQL Server werk belastingen. 
- De [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) Series, de [M-](../../../virtual-machines/m-series.md)en de [Mv2-](../../../virtual-machines/mv2-series.md) series bieden de optimale geheugen-naar-vCore-verhouding die vereist is voor OLTP-workloads. Virtuele machines uit de M-serie bieden de hoogste geheugen-naar-vCore verhouding die is vereist voor essentiële workloads en die ook ideaal zijn voor Data Warehouse-workloads. 
- Overweeg een hogere geheugen-naar-vCore-verhouding voor bedrijfs kritieke en Data Warehouse-workloads. 
- Maak gebruik van de Azure virtual machine Marketplace-installatie kopieën als de SQL Server instellingen en opslag opties zijn geconfigureerd voor optimale prestaties van SQL Server. 
- Verzamel de prestatie kenmerken van de doel-workload en gebruik deze om de juiste VM-grootte voor uw bedrijf te bepalen.

Als u de controle lijst voor VM-grootte met anderen wilt vergelijken, raadpleegt u de [controle lijst uitgebreide prestaties best practices](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Overzicht

Wanneer u een SQL Server op Azure VM maakt, moet u het type werk belasting zorgvuldig overwegen. Als u een bestaande omgeving migreert, [verzamelt u een basis lijn voor prestaties](performance-guidelines-best-practices-collect-baseline.md) om uw SQL Server te bepalen voor Azure VM-vereisten. Als dit een nieuwe virtuele machine is, maakt u een nieuwe SQL Server VM op basis van de vereisten van uw leverancier. 

Als u een nieuwe SQL Server VM maakt met een nieuwe toepassing die is gemaakt voor de Cloud, kunt u uw SQL Server virtuele machine eenvoudig groter of kleiner maken naarmate uw gegevens en gebruiks vereisten worden gegroeid.
Start de ontwikkel omgevingen met de D-serie, de B-serie of de Av2-serie en breid uw omgeving uit gedurende een periode. 

Gebruik de SQL Server VM Marketplace-installatie kopieën met de opslag configuratie in de portal. Dit maakt het eenvoudiger om de benodigde opslag groepen te maken om de grootte, IOPS en door voer te verkrijgen die nodig zijn voor uw workloads. Het is belang rijk om SQL Server Vm's te kiezen die ondersteuning bieden voor Premium Storage en Premium Storage-caching. Zie het artikel over [opslag](performance-guidelines-best-practices-storage.md) voor meer informatie. 

Het aanbevolen minimum voor een productie-OLTP-omgeving is 4 vCore, 32 GB geheugen en een geheugen-naar-vCore-verhouding van 8. Voor nieuwe omgevingen begint u met 4 vCore machines en schaalt u op 8, 16, 32 vCores of meer wanneer uw gegevens-en reken vereisten veranderen. Voor OLTP-door Voer is het doel SQL Server Vm's met 5000 IOPS voor elke vCore. 

SQL Server Data Warehouse en bedrijfs kritieke omgevingen moeten vaak groter worden dan de 8-vCore verhouding. Voor middel grote omgevingen kunt u een geheugen-naar-vCore-verhouding van 16 kiezen en een geheugen-naar-vCore-verhouding van 32 voor grotere data warehouse omgevingen. 

SQL Server Data Warehouse-omgevingen profiteren vaak van de parallelle verwerking van grotere machines. Daarom zijn de M-serie en de Mv2-serie Strong opties voor grotere data warehouse omgevingen.

Gebruik de vCPU-en geheugen configuratie van uw bron machine als basis lijn voor het migreren van een huidige on-premises SQL Server Data Base naar SQL Server op virtuele machines van Azure. Breng uw kern licentie naar Azure om te profiteren van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) en bespaar op SQL Server licentie kosten.

## <a name="memory-optimized"></a>Geoptimaliseerd geheugen

De [grootte van virtuele machines](../../../virtual-machines/sizes-memory.md) die zijn geoptimaliseerd voor geheugen, is een primair doel voor SQL Server vm's en de aanbevolen keuze van micro soft. De virtuele machines die zijn geoptimaliseerd voor geheugen bieden betere geheugen-naar-CPU-verhoudingen en opties voor gemiddeld naar groot aantal caches. 

### <a name="m-mv2-and-mdsv2-series"></a>M-, Mv2-en Mdsv2-serie

De [M-serie](../../../virtual-machines/m-series.md) biedt vCore aantallen en geheugen voor een aantal van de grootste SQL Server workloads.  

De [Mv2-serie](../../../virtual-machines/mv2-series.md) heeft het hoogste aantal vCore en het geheugen en wordt aanbevolen voor bedrijfs kritieke en Data Warehouse-workloads. Instanties van de Mv2-serie zijn geoptimaliseerd voor geheugen die ongeëvenaarde reken prestaties bieden ter ondersteuning van grote in-memory data bases en workloads met een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, grote caches en analyses in het geheugen.

Het [Standard_M64ms](../../../virtual-machines/m-series.md) heeft bijvoorbeeld 28 geheugen-naar-vCore-verhouding.

[Mdsv2 gemiddeld geheugen Series](../../..//virtual-machines/msv2-mdsv2-series.md) is een nieuwe M-serie die momenteel als [Preview-versie](https://aka.ms/Mv2MedMemoryPreview) beschikbaar is en die een groot aantal virtuele machines van de m-reeks biedt met een het midtier-geheugen aanbod. Deze computers zijn goed geschikt voor SQL Server workloads met een minimum van 10 geheugen-naar-vCore-ondersteuning van Maxi maal 30.

Enkele van de functies van de M-en Mv2-serie voor SQL Server prestaties zijn onder andere [Premium Storage](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching) -ondersteuning voor opslag, ondersteuning voor [hoge schijven](../../../virtual-machines/disks-enable-ultra-ssd.md) en [Schrijf versnelling](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Edsv4-serie

De [Edsv4-serie](../../../virtual-machines/edv4-edsv4-series.md) is ontworpen voor geheugenintensieve toepassingen. Deze Vm's hebben een grote lokale opslag van SSD-capaciteit, een hoge IOPS van de lokale schijf, Maxi maal 504 GiB aan RAM-geheugen. Er is een bijna consistent 8-GiB per vCore van de meeste virtuele machines. Dit is ideaal voor standaard SQL Server workloads. 

Er is een nieuwe virtuele machine in deze groep met de [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) die 80 vCores, 504 GB geheugen biedt, met een geheugen-naar-vCore-verhouding van 6. Deze virtuele machine is onvindbaar omdat deze is [geïsoleerd](../../../virtual-machines/isolation.md) , wat betekent dat de virtuele machine gegarandeerd wordt uitgevoerd op de host en daarom is geïsoleerd van andere werk belastingen van de klant. Dit heeft een geheugen-naar-vCore-verhouding die lager is dan de aanbevolen waarde voor SQL Server, zodat deze alleen kan worden gebruikt als isolatie is vereist.

De virtuele machines uit de Edsv4-serie ondersteunen [Premium-opslag](../../../virtual-machines/premium-storage-performance.md)en [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2-serie 11-15

De [DSv2-serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) heeft dezelfde geheugen-en schijf configuraties als de vorige D-serie. Deze reeks heeft een consistent geheugen-naar-CPU-verhouding van 7 op alle virtuele machines. Dit is de kleinste van de door het geheugen geoptimaliseerde reeks en is een goede optie voor goedkope lage kosten voor SQL Server workloads op instap niveau.

De [DSv2-serie 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) biedt ondersteuning voor [Premium Storage](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching). dit wordt ten zeerste aanbevolen voor optimale prestaties.

## <a name="general-purpose"></a>Algemeen doel

De grootten van [virtuele machines voor algemeen gebruik](../../../virtual-machines/sizes-general.md) zijn ontworpen om evenwichtige geheugen-naar-vCore-ratio's te bieden voor kleinere workloads op instap niveau, zoals ontwikkelen en testen, webservers en kleinere database servers. 

Vanwege de kleinere geheugen-naar-vCore-verhoudingen met de virtuele machines voor algemene doel einden, is het belang rijk om zorgvuldig op geheugen gebaseerde prestatie meter items te controleren om ervoor te zorgen dat SQL Server de benodigde buffer cache kunt ophalen. Zie de [basis lijn van geheugen prestaties](performance-guidelines-best-practices-collect-baseline.md#memory) voor meer informatie. 

Omdat de eerste aanbeveling voor productie werkbelastingen een geheugen-naar-vCore-verhouding van 8 is, is de mini maal aanbevolen configuratie voor een virtuele machine voor algemeen gebruik met SQL Server 4 vCPU en 32 GB geheugen. 

### <a name="ddsv4-series"></a>Ddsv4-serie

De [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) biedt een billijke combi natie van vCPU, geheugen en tijdelijke schijven, maar met een kleinere ondersteuning voor geheugen-naar-vCore. 

De Ddsv4 Vm's bevatten een lagere latentie en een hogere snelheid voor lokale opslag.

Deze computers zijn ideaal voor gelijktijdige SQL-en app-implementaties waarvoor snelle toegang tot tijdelijke opslag en relationele data bases is vereist. Er is een standaard geheugen-naar-vCore-verhouding van 4 voor alle virtuele machines in deze serie. 

Daarom is het raadzaam om gebruik te maken van de D8ds_v4 als de eerste virtuele machine in deze serie, met 8 vCores en 32 GB aan geheugen. De grootste computer is de D64ds_v4, met 64 vCores en 256 GB geheugen.

De virtuele machines uit de [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) ondersteunen [Premium-opslag](../../../virtual-machines/premium-storage-performance.md) en [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> De [Ddsv4-serie](../../../virtual-machines/ddv4-ddsv4-series.md) heeft niet de geheugen-naar-vCore-verhouding van 8 die wordt aanbevolen voor SQL Server workloads. Zo kunt u overwegen om deze virtuele machines alleen te gebruiken voor kleinere toepassingen en werk belastingen.

### <a name="b-series"></a>B-serie

De grootten van de virtuele machines van de [B-serie](../../../virtual-machines/sizes-b-series-burstable.md) zijn ideaal voor werk belastingen die geen consistente prestaties nodig hebben, zoals het testen van concepten en zeer kleine toepassings-en ontwikkelings servers. 

De meeste van de grote machine grootten van de [B-Series](../../../virtual-machines/sizes-b-series-burstable.md) hebben een geheugen-naar-vCore-verhouding van 4. De grootste van deze computers is het [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) met 20 vCores en 80 GB geheugen.

Deze serie is uniek omdat de apps tijdens kantoor uren kunnen worden **gebursteerd** met bursteer bare tegoeden die variëren op basis van de grootte van de machine. 

Wanneer de tegoeden zijn uitgeput, keert de VM terug naar de prestaties van de basislijn machine.

Het voor deel van de B-serie is de besparingen die u kunt realiseren in vergelijking met de andere VM-grootten in andere reeksen, met name als u de verwerkings kracht spaarzaam nodig hebt.

Deze reeks biedt ondersteuning voor [Premium-opslag](../../../virtual-machines/premium-storage-performance.md), maar **biedt geen ondersteuning** voor [Premium Storage-caching](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> De [bursted B-Series](../../../virtual-machines/sizes-b-series-burstable.md) heeft niet de geheugen-naar-vCore-verhouding van 8 die wordt aanbevolen voor SQL Server workloads. Overweeg het gebruik van deze virtuele machines voor kleinere toepassingen, webservers en ontwikkel werkbelastingen.

### <a name="av2-series"></a>Av2-serie

De virtuele machines uit de [Av2-serie](../../../virtual-machines/av2-series.md) zijn het meest geschikt voor werk belastingen op instap niveau, zoals ontwikkelen en testen, webservers met weinig verkeer, kleine tot middel grote app-data bases en proof-of-concepten.

Alleen de [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 VCores en 16GBs van het geheugen), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCores en 32GBs van het geheugen) en de [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCores en 64GBs aan geheugen) hebben een goede geheugen-naar-vCore-verhouding van 8 voor deze belangrijkste drie virtuele machines. 

Deze virtuele machines zijn goede opties voor kleinere ontwikkel-en test SQL Server machines. 

De 8 vCore- [Standard_A8m_v2](../../../virtual-machines/av2-series.md) is mogelijk ook een goede optie voor kleine toepassingen en webservers.

> [!NOTE] 
> De Av2-serie biedt geen ondersteuning voor Premium-opslag en is daarom niet aanbevolen voor productie SQL Server workloads, zelfs met de virtuele machines met een geheugen-naar-vCore-verhouding van 8.

## <a name="storage-optimized"></a>Geoptimaliseerde opslag

De [grootten voor opslag geoptimaliseerde vm's](../../../virtual-machines/sizes-storage.md) zijn bedoeld voor specifieke use cases. Deze virtuele machines zijn specifiek ontworpen met geoptimaliseerde schijf doorvoer en IO. 

### <a name="lsv2-series"></a>Lsv2-serie

De [Lsv2-serie](../../../virtual-machines/lsv2-series.md) biedt een hoge door Voer, lage latentie en lokale NVMe-opslag. De virtuele machines uit de Lsv2-serie zijn geoptimaliseerd voor het gebruik van de lokale schijf op het knoop punt dat rechtstreeks aan de virtuele machine is gekoppeld, in plaats van duurzame gegevens schijven te gebruiken. 

Deze virtuele machines zijn sterke opties voor big data-, Data Warehouse-, rapportage-en ETL-workloads. De hoge door Voer en IOPS van de lokale NVMe-opslag is een goede use-case voor het verwerken van bestanden die worden geladen in uw data base en andere scenario's waarin de gegevens opnieuw kunnen worden gemaakt op basis van het bron systeem of andere opslag plaatsen, zoals Azure Blob Storage of Azure Data Lake. [Lsv2-serie](../../../virtual-machines/lsv2-series.md) Vm's kunnen de schijf prestaties ook Maxi maal 30 minuten per keer opruimen.

De grootte van deze virtuele machines van 8 tot 80 vCPU met 8 GiB geheugen per vCPU en voor elke 8 Vcpu's is 1,92 TB van NVMe SSD. Dit betekent dat er voor de grootste virtuele machine van deze serie een [L80s_v2](../../../virtual-machines/lsv2-series.md)is. er is 80 vCPU-en 640-geheugenpen met 10X 1.92 TB NVMe-opslag.  Er is een consistent geheugen-naar-vCore-verhouding van 8 op al deze virtuele machines.

De NVMe-opslag is een tijdelijke waarde dat gegevens op deze schijven verloren gaan als u de toewijzing van de virtuele machine ongedaan maakt of als deze is verplaatst naar een andere host voor service retoucheren.

De Lsv2-en LS-reeks bieden ondersteuning voor [Premium-opslag](../../../virtual-machines/premium-storage-performance.md), maar niet voor Premium Storage-caching. Het maken van een lokale cache om IOPs te verhogen, wordt niet ondersteund. 

> [!WARNING]
> Het opslaan van uw gegevens bestanden op de tijdelijke NVMe-opslag kan leiden tot verlies van gegevens wanneer de toewijzing van de virtuele machine ongedaan wordt gemaakt. 

## <a name="constrained-vcores"></a>Beperkte vCores

Hoge prestaties SQL Server workloads hebben vaak grotere hoeveel heden geheugen, I/O en door voer zonder het hogere aantal vCore. 

De meeste OLTP-workloads zijn toepassings databases die worden aangedreven door grote aantallen kleinere trans acties. Met OLTP-workloads wordt slechts een kleine hoeveelheid gegevens gelezen of gewijzigd, maar de volumes van trans acties die door het aantal gebruikers worden aangestuurd, zijn veel hoger. Het is belang rijk om de SQL Server geheugen beschikbaar te stellen voor het plannen van de cache, recent gebruikte gegevens op te slaan voor prestaties en ervoor te zorgen dat fysieke Lees bewerkingen snel in het geheugen kunnen worden gelezen. 

Deze OLTP-omgevingen hebben een grotere hoeveelheid geheugen, snelle opslag en de I/O-band breedte nodig om optimaal te kunnen werken. 

Azure biedt VM-grootten met [beperkte vCPU-aantallen](../../../virtual-machines/constrained-vcpu.md), zodat dit prestatie niveau kan worden gehandhaafd zonder de hogere SQL Server licentie kosten. 

Dit helpt de licentie kosten te beheren door de beschik bare vCores te verminderen en tegelijkertijd hetzelfde geheugen, dezelfde opslag en I/O-band breedte van de bovenliggende virtuele machine te behouden.

Het aantal vCPU kan worden beperkt tot een half tot één kwar taal van de oorspronkelijke VM-grootte. Het verminderen van de vCores die beschikbaar is voor de virtuele machine leidt tot hogere geheugen-naar-vCore-verhoudingen, maar de reken kosten blijven hetzelfde.

Deze nieuwe VM-grootten hebben een achtervoegsel dat het aantal actieve Vcpu's opgeeft, zodat het gemakkelijker te identificeren is. 

De [M64-32MS](../../../virtual-machines/constrained-vcpu.md) vereist bijvoorbeeld alleen een licentie voor 32 SQL Server vCores met het geheugen, I/O en de door Voer van de [M64ms](../../../virtual-machines/m-series.md) en de [M64-16 MS](../../../virtual-machines/constrained-vcpu.md) vereist een licentie van 16 vCores.  Hoewel de [M64-16 MS](../../../virtual-machines/constrained-vcpu.md) een kwart van de SQL Server licentie kosten van de M64ms heeft, zijn de reken kosten van de virtuele machine hetzelfde.

> [!NOTE] 
> - Gemiddeld tot grote data warehouse-werk belastingen kunnen nog steeds profiteren van [beperkte vCore-vm's](../../../virtual-machines/constrained-vcpu.md), maar data warehouse-workloads worden vaak gekenmerkt door minder gebruikers en processen die grotere hoeveel heden gegevens adresseren via query abonnementen die parallel worden uitgevoerd. 
> - De reken kosten, met inbegrip van de licentie voor het besturings systeem, blijven hetzelfde als de bovenliggende virtuele machine. 



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie de andere artikelen in deze serie:
- [Snelle controle lijst](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Basis lijn verzamelen](performance-guidelines-best-practices-collect-baseline.md)

Zie [beveiligings overwegingen voor SQL Server op Azure virtual machines](security-considerations-best-practices.md)voor aanbevolen procedures voor beveiliging.

Bekijk andere artikelen over SQL Server virtuele machines op [SQL Server in Azure virtual machines overzicht](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).
