---
title: Prestaties optimaliseren op virtuele machines uit de Azure Lsv2-serie
description: Meer informatie over het optimaliseren van de prestaties voor uw oplossing op de virtuele machines uit de Lsv2-serie met behulp van een Windows-voor beeld.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 8d38e3f58de0fc4cc1e963c18b002dc1e16852ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556006"
---
# <a name="optimize-performance-on-the-lsv2-series-windows-virtual-machines"></a>Optimaliseer de prestaties van de virtuele Windows-machines uit de Lsv2-serie

Virtuele machines uit de Lsv2-serie ondersteunen diverse werk belastingen die hoge I/O en door Voer op lokale opslag voor een breed scala aan toepassingen en branches nodig hebben.  De Lsv2-serie is ideaal voor Big Data-, SQL-, NoSQL-data bases, gegevens-en opslag databases, waaronder Cassandra, MongoDB, Cloudera en redis.

Het ontwerp van de Lsv2-serie Virtual Machines (Vm's) maximaliseert de AMD EPYC™ 7551-processor om de beste prestaties te bieden tussen de processor, het geheugen, de NVMe-apparaten en de Vm's. Naast het maximaliseren van de prestaties van de hardware, zijn virtuele machines uit de Lsv2-serie ontworpen om te werken met de behoeften van Windows-en Linux-besturings systemen voor betere prestaties met de hardware en de software.

Het afstemmen van de software en hardware heeft geresulteerd in de geoptimaliseerde versie van [Windows Server 2019 Data Center](https://www.microsoft.com/cloud-platform/windows-server-pricing), uitgebracht op begin december 2018 tot Azure Marketplace, die maximale prestaties ondersteunt op de NVMe-apparaten in virtuele machines uit de Lsv2-serie.

In dit artikel vindt u tips en suggesties om ervoor te zorgen dat uw werk belastingen en toepassingen de maximale prestaties voor de virtuele machines kunnen bewerkstelligen. De informatie op deze pagina wordt voortdurend bijgewerkt naarmate er meer Lsv2 geoptimaliseerde installatie kopieën worden toegevoegd aan de Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ chipset-architectuur

Vm's uit de Lsv2-serie gebruiken AMD EYPC™ server-processors op basis van de micro architectuur van Zen. AMD ontwikkelde oneindig infrastructuur (indien) voor EYPC™ als schaal bare Interconnect voor het NUMA-model dat kan worden gebruikt voor on-to-dobbel, on-package en multi-package-communicatie. Vergeleken met QPI-verbindingen (snelle paden) en UPI (Ultra-Path Interconnect) die worden gebruikt voor Intel moderne monolithische-dobbel krachtige processors, kunnen de kleine en grote NUMA-architectuur voor zowel prestaties als uitdagingen optreden. De daad werkelijke impact van de geheugen bandbreedte en latentie beperkingen kunnen variëren, afhankelijk van het type werk belastingen dat wordt uitgevoerd.

## <a name="tips-for-maximizing-performance"></a>Tips voor het optimaliseren van prestaties

* De hardware die de virtuele machines uit de Lsv2-serie doorstuurt, maakt gebruik van NVMe-apparaten met acht I/O-wachtrij paren (QP) s. Elke rij in de I/O-wachtrij van NVMe is in feite een paar: een verzend wachtrij en een voltooiings wachtrij. Het NVMe-stuur programma is zo ingesteld dat het gebruik van deze acht I/O-QPs wordt geoptimaliseerd door I/O te distribueren in een round robin schema. Als u maximale prestaties wilt krijgen, voert u acht taken per apparaat uit.

* Vermijd het mixen van NVMe-beheer opdrachten (bijvoorbeeld NVMe SMART info query, enzovoort) met NVMe I/O-opdrachten tijdens actieve werk belastingen. Lsv2 NVMe-apparaten worden ondersteund door een Hyper-V NVMe direct-technologie, waarmee wordt overgeschakeld naar ' langzame modus ' wanneer een NVMe-beheer opdracht in behandeling is. Lsv2-gebruikers kunnen een aanzienlijke prestaties van NVMe-I/O-prestaties zien als dat wel het geval is.

* Lsv2-gebruikers moeten niet vertrouwen op de NUMA-gegevens van het apparaat (alle 0) die in de virtuele machine zijn gerapporteerd voor gegevens stations om de NUMA-affiniteit voor hun apps te bepalen. De aanbevolen manier om de prestaties te verbeteren is als dat mogelijk is om werk belastingen over Cpu's te spreiden. 

* De Maxi maal ondersteunde wachtrij diepte per I/O-wachtrij paar voor Lsv2 VM NVMe-apparaat is 1024 (VS. Amazon i3 wachtrij diepte 32-limiet). Lsv2 gebruikers moeten de werk belasting van hun (synthetische) benchmarking beperken tot de wachtrij diepte 1024 of lager om te voor komen dat de wachtrij vol is voor waarden, waardoor de prestaties kunnen worden verminderd.

## <a name="utilizing-local-nvme-storage"></a>Lokale NVMe-opslag gebruiken

Lokale opslag op de NVMe-schijf van 1,92 TB op alle Lsv2-Vm's is tijdelijk. Tijdens een geslaagde standaard herstart van de VM blijven de gegevens op de lokale NVMe-schijf behouden. De gegevens blijven niet behouden op het NVMe als de virtuele machine opnieuw wordt geïmplementeerd, de toewijzing ervan wordt verwijderd of is gewist. De gegevens blijven niet behouden als een ander probleem ertoe leidt dat de virtuele machine of de hardware waarop de VM wordt uitgevoerd, een slechte status krijgt. Als dit gebeurt, worden alle gegevens op de oude host veilig gewist.

Er zijn ook gevallen waarin de virtuele machine moet worden verplaatst naar een andere hostcomputer, bijvoorbeeld tijdens een geplande onderhouds bewerking. Geplande onderhouds bewerkingen en bepaalde hardwarestoringen kunnen worden verwacht met [Scheduled Events](scheduled-events.md). Scheduled Events moet worden gebruikt om te blijven werken met voorspelde onderhouds-en herstel bewerkingen.

In het geval dat een geplande onderhouds gebeurtenis vereist dat de VM opnieuw wordt gemaakt op een nieuwe host met lege lokale schijven, moeten de gegevens opnieuw worden gesynchroniseerd (opnieuw, waarbij alle gegevens op de oude host veilig worden gewist). Dit gebeurt omdat virtuele machines uit de Lsv2-serie momenteel geen Live migratie ondersteunen op de lokale NVMe-schijf.

Er zijn twee modi voor gepland onderhoud.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standaard virtuele machine onderhoud door de klant beheerd

- Tijdens een periode van 30 dagen wordt de virtuele machine verplaatst naar een bijgewerkte host.
- Lsv2 lokale opslag gegevens kunnen verloren gaan. Daarom wordt het maken van een back-up van gegevens voorafgaand aan de gebeurtenis aanbevolen.

### <a name="automatic-maintenance"></a>Automatisch onderhoud

- Deze gebeurtenis treedt op als de klant geen onderhouds werkzaamheden van de klant uitvoert, of in het geval van nood procedures, zoals een gebeurtenis met een nul-dag-beveiliging.
- Bedoeld om klant gegevens te bewaren, maar er is een klein risico van het blok keren of opnieuw opstarten van een virtuele machine.
- Lsv2 lokale opslag gegevens kunnen verloren gaan. Daarom wordt het maken van een back-up van gegevens voorafgaand aan de gebeurtenis aanbevolen.

Voor toekomstige service gebeurtenissen gebruikt u het beheerde onderhouds proces om een tijd te selecteren die het handigst is voor de update. Vóór de gebeurtenis kunt u een back-up maken van uw gegevens in Premium Storage. Nadat de onderhouds gebeurtenis is voltooid, kunt u uw gegevens herstellen naar de vernieuwde Lsv2 Vm's lokale NVMe-opslag.

Scenario's voor het onderhouden van gegevens op lokale NVMe-schijven zijn onder andere:

- De virtuele machine wordt uitgevoerd en is in orde.
- De virtuele machine wordt opnieuw opgestart (door u of Azure).
- De virtuele machine is onderbroken (gestopt zonder de toewijzing).
- Het meren deel van de geplande onderhouds bewerkingen.

Scenario's voor het veilig wissen van gegevens om de klant te beschermen, zijn:

- De VM wordt opnieuw geïmplementeerd, gestopt (de toewijzing) of verwijderd (door u).
- De virtuele machine wordt niet meer in orde en moet op een ander knoop punt herstellen vanwege een hardwareprobleem.
- Een klein aantal geplande onderhouds bewerkingen voor onderhoud waarvoor de VM opnieuw moet worden toegewezen aan een andere host voor onderhoud.

Zie [back-up en herstel na nood gevallen voor Azure IaaS-schijven](../backup-and-disaster-recovery-for-azure-iaas-disks.md)voor meer informatie over de opties voor het maken van back-ups van gegevens in lokale opslag.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* **Hoe kan ik de implementatie van Vm's uit de Lsv2-serie starten?**  
   Net als bij elke andere virtuele machine kunt u de [Portal](quick-create-portal.md), [Azure cli](quick-create-cli.md)of [Power shell](quick-create-powershell.md) gebruiken om een virtuele machine te maken.

* **Leidt een enkele NVMe-schijf fout tot gevolg dat alle Vm's op de host mislukken?**  
   Als er een schijf fout op het hardware-knoop punt wordt gedetecteerd, heeft de hardware de status mislukt. Als dit gebeurt, worden alle Vm's op het knoop punt automatisch ongedaan gemaakt en verplaatst naar een gezond knoop punt. Voor virtuele machines uit de Lsv2-serie betekent dit dat de gegevens van de klant op het knoop punt waarvoor een storing optreedt, ook veilig worden gewist en opnieuw moeten worden gemaakt door de klant op het nieuwe knoop punt. Zoals genoteerd, worden de gegevens op het knoop punt waarvoor een storing optreedt proactief met de virtuele machines verplaatst wanneer ze worden overgebracht naar een ander knoop punt voordat Livemigratie op Lsv2 beschikbaar wordt.

* **Moet ik polling aanpassen in Windows in Windows Server 2012 of Windows Server 2016?**  
   NVMe-polling is alleen beschikbaar in Windows Server 2019 op Azure.  

* **Kan ik teruggaan naar een traditioneel ISR-model (Interrupt Service routine)?**  
   Vm's uit de Lsv2-serie zijn geoptimaliseerd voor NVMe-polling. Updates worden continu verschaft om polling prestaties te verbeteren.

* **Kan ik de polling-instellingen in Windows Server 2019 aanpassen?**  
   De polling-instellingen zijn niet aanpasbaar voor gebruikers.
   
## <a name="next-steps"></a>Volgende stappen

* Zie de specificaties voor alle [virtuele machines die zijn geoptimaliseerd voor opslag prestaties](../sizes-storage.md) op Azure
