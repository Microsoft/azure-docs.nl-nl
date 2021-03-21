---
title: Opslag configuraties voor virtuele Azure-machines SAP HANA | Microsoft Docs
description: Opslag aanbevelingen voor de virtuele machine waarop SAP HANA geïmplementeerd.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, opslag met hoge schijf, Premium-opslag
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c0fbb1280fc2a7eaca1d97e7e016cf480873c8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101666583"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configuraties van SAP HANA in virtuele Azure-machineopslag

Azure biedt verschillende soorten opslag die geschikt zijn voor Azure-Vm's met SAP HANA. De **SAP Hana gecertificeerde Azure-opslag typen** die kunnen worden overwogen voor SAP Hana implementaties lijst zoals: 

- Azure Premium SSD-of Premium-opslag 
- [Ultraschijven](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Voor meer informatie over deze schijf typen raadpleegt u het artikel [Azure Storage typen voor SAP-werk belasting](./planning-guide-storage.md) en [selecteert u een schijf type](../../disks-types.md)

Azure biedt twee implementatie methoden voor Vhd's in azure Standard en Premium Storage. U wordt verwacht te profiteren van [Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) voor Azure Block Storage-implementaties. 

Raadpleeg de [Azure-documentatie voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor een lijst met opslag typen en de bijbehorende SLA'S in IOPS en opslag doorvoer.

> [!IMPORTANT]
> Onafhankelijk van het gekozen type Azure-opslag moet het bestands systeem dat wordt gebruikt op die opslag worden ondersteund door SAP voor het specifieke besturings systeem en DBMS. [SAP-ondersteunings opmerking #2972496](https://launchpad.support.sap.com/#/notes/2972496) een lijst met ondersteunde bestands systemen voor verschillende besturings systemen en data bases, waaronder SAP Hana. Dit geldt voor alle volumes SAP HANA mogelijk toegang heeft tot lees-en schrijf bewerkingen voor een wille keurige taak. Door NFS in azure te gebruiken voor SAP HANA, zijn extra beperkingen van NFS-versies van toepassing, zoals verderop in dit artikel wordt vermeld 


De minimale SAP HANA gecertificeerde voor waarden voor de verschillende opslag typen zijn: 

- Azure Premium Storage- **/Hana/log** moet worden ondersteund door Azure [Write Accelerator](../../how-to-enable-write-accelerator.md). Het **/Hana/data** -volume kan in Premium Storage worden geplaatst zonder Azure write Accelerator of op een ultra schijf
- Azure Ultra Disk ten minste voor het **/Hana/log** -volume. Het **/Hana/data** -volume kan worden geplaatst in Premium Storage zonder Azure write Accelerator of om sneller opnieuw opstarten te krijgen met een hoge schijf
- **NFS v 4.1** -volumes boven op Azure NetApp files voor **/Hana/log en/Hana/data**. Het volume van/Hana/Shared kan NFS v3 of NFS v 4.1-protocol gebruiken

Sommige opslag typen kunnen worden gecombineerd. Het is bijvoorbeeld mogelijk om **/Hana/data** in te stellen op Premium Storage en **/Hana/log** kunnen op ultra Disk Storage worden geplaatst om de vereiste lage latentie te verkrijgen. Als u een volume gebruikt op basis van ANF voor **/Hana/data**, moet  **/Hana/log** -volume ook worden gebaseerd op NFS op ANF. Het gebruik van NFS op ANF voor een van de volumes (zoals/Hana/data) en Azure Premium Storage of Ultra disk voor het andere volume (zoals **/Hana/log**) wordt **niet ondersteund**.

In de on-premises wereld moest u zelden aandacht houden met de I/O-subsystemen en de mogelijkheden ervan. Daarom moest de leverancier van het apparaat controleren of aan de minimale opslag vereisten wordt voldaan voor de SAP HANA. Wanneer u zelf de Azure-infra structuur bouwt, moet u rekening houden met een aantal van deze SAP-vereisten. Enkele van de minimale doorvoer kenmerken die door SAP worden aanbevolen, zijn:

- Lezen/schrijven op **/Hana/log** van 250 MB/sec. met 1 MB I/O-groottes
- Lees activiteit van ten minste 400 MB/sec voor **/Hana/data** voor 16 mb en 64 MB I/O-groottes
- Schrijf activiteit van ten minste 250 MB per seconde voor **/Hana/data** met 16 mb en 64 MB I/O-groottes

Gezien de minimale opslag latentie is essentieel voor de DBMS-systemen, zelfs als DBMS, zoals SAP HANA, behoud gegevens in het geheugen. Het kritieke pad in de opslag ruimte is doorgaans rond de schrijf bewerkingen van het transactie logboek van de DBMS-systemen. Maar ook bewerkingen zoals het schrijven van opslag punten of het laden van gegevens in het geheugen nadat het herstel is gelukt, kunnen kritiek zijn. Daarom is het **verplicht** om gebruik te maken van Azure Premium Storage, Ultra disk of ANF voor **/Hana/data** -en **/Hana/log** -volumes. 


Sommige richt lijnen voor het selecteren van de opslag configuratie voor HANA kunnen als volgt worden weer gegeven:

- Bepaal het type opslag op basis van [Azure Storage typen voor SAP-werk belasting](./planning-guide-storage.md) en [Selecteer een schijf type](../../disks-types.md)
- De totale I/O-door Voer van de virtuele machine en IOPS beperken bij het verg Roten/verkleinen of bepalen van een virtuele machine. De totale VM-opslag doorvoer wordt beschreven in de grootte van het door het artikel [geoptimaliseerde virtuele machines](../../sizes-memory.md)
- Bij het bepalen van de opslag configuratie kunt u de algemene door Voer van de virtuele machine blijven vervangen door de **/Hana/data** -volume configuratie. Het schrijven van opslag punten, SAP HANA kan agressief uitgeven I/O's zijn. Het is eenvoudig om te pushen naar de doorvoer limieten van uw **/Hana/data** -volume tijdens het schrijven van een opslag punt. Als uw schijven die het **/Hana/data** -volume bouwen een hogere door voer hebben dan uw virtuele machine toestaat, kunt u in situaties optreden waarbij de door Voer die wordt gebruikt door de schrijf bewerking voor het opslag punt, de doorvoer vereisten van de logboeken voor het opnieuw registreren van het opnieuw schrijven verstoort. Een situatie die de door Voer van de toepassing kan beïnvloeden


> [!IMPORTANT]
> De suggesties voor de opslag configuraties zijn bedoeld als instructies om te beginnen met. Bij het uitvoeren van de werk belasting en het analyseren van patronen voor opslag gebruik kunt u realiseren dat u niet alle beschik bare opslag bandbreedte of IOPS gebruikt. U kunt Overweeg in de opslag dan overwegen. Het is ook mogelijk dat uw workload meer opslag doorvoer vereist dan bij deze configuraties wordt voorgesteld. Als gevolg hiervan moet u mogelijk meer capaciteit, IOPS of door Voer implementeren. In het veld van de spanning tussen de vereiste opslag capaciteit, opslag latentie vereist, opslag doorvoer en IOPS vereist en geduurde configuratie, biedt Azure voldoende verschillende opslag typen met verschillende mogelijkheden en verschillende prijs punten voor het vinden en aanpassen van de juiste inbreuk voor u en uw HANA-workloads.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>Stripesets versus SAP HANA gegevens volume partitioneren
Met Azure Premium Storage kunt u de beste prijs-prestatie verhouding bereiken wanneer u het **/Hana/data** -en/of **/Hana/log** -volume op meerdere Azure-schijven verwijdert. In plaats van grotere schijf volumes te implementeren die de meer op IOPS of door voer nodig hebben. Tot dusver werd dit gedaan met LVM-en MDADM-volume managers die deel uitmaken van Linux. De methode voor het verwijderen van schijven is tien tallen oud en goed bekend. Net zoals die striped volumes moeten profiteren van de IOPS-of doorvoer mogelijkheden die u nodig hebt, worden er ingewikkelde opties toegevoegd om deze striped volumes te beheren. Met name in gevallen waarin de volumes een uitgebreide capaciteit moeten krijgen. Ten minste voor **/Hana/data** heeft SAP een alternatieve methode geïntroduceerd waarmee hetzelfde doel wordt gerealiseerd als over meerdere Azure-schijven. Sinds SAP HANA 2,0 SPS03 is de HANA-indexserver in staat om de I/O-activiteit over meerdere HANA-gegevens bestanden te verwijderen die zich op verschillende Azure-schijven bevinden. Het voor deel is dat u geen striped volume kunt maken en beheren op verschillende Azure-schijven. De SAP HANA functionaliteit van partitionering van gegevens volumes wordt gedetailleerd beschreven in:

- [De HANA-beheerders handleiding](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog over SAP HANA-gegevens volumes partitioneren](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP-notitie #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP-notitie #2700123](https://launchpad.support.sap.com/#/notes/2700123)

Door de details te lezen, is het duidelijk dat het gebruik van deze functionaliteit complexer maakt op basis van op volume manager gebaseerde stripesets. U kunt er ook voor zorgen dat de HANA-gegevens volume-partitionering niet alleen werkt voor Azure Block Storage, zoals Azure Premium Storage. U kunt deze functionaliteit gebruiken om over NFS-shares te verwijderen, voor het geval deze shares over IOPS of door Voer beperkingen hebben.  


## <a name="linux-io-scheduler-mode"></a>Scheduler-modus Linux I/O
Linux heeft verschillende I/O-plannings modi. Algemene aanbeveling via Linux-leveranciers en SAP is om de I/O scheduler-modus voor schijf volumes van de modus **MQ-deadline** of **kyber** opnieuw te configureren voor de **nooperation** (niet-multiwachtrij) of **geen** voor de modus voor meerdere wacht rijen als deze nog niet is uitgevoerd door de SLES saptune-profielen. Er wordt naar Details verwezen in: 

- [SAP-notitie #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-notitie #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [Probleem met de instelling nooperation in SLES 12 SP4](https://www.suse.com/support/kb/doc/?id=000019547)

Wijzig op Red Hat de instellingen die zijn ingesteld door de specifieke afstemmings profielen voor de verschillende SAP-toepassingen.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Oplossingen met Premium Storage en Azure Write Accelerator voor virtuele machines uit de Azure M-serie
Azure Write Accelerator is een functionaliteit die alleen beschikbaar is voor virtuele machines uit de M-serie van Azure. Als de naam statussen is het doel van de functionaliteit om de I/O-latentie van schrijf bewerkingen te verbeteren voor de Azure Premium-opslag. Voor SAP HANA moet Write Accelerator alleen worden gebruikt voor het **/Hana/log** -volume. Daarom zijn de **/Hana/data** en **/Hana/log** afzonderlijke volumes met Azure write Accelerator alleen het **/Hana/log** -volume ondersteunen. 

> [!IMPORTANT]
> Als Azure Premium Storage wordt gebruikt, is het gebruik van Azure [Write Accelerator](../../how-to-enable-write-accelerator.md) voor het **/Hana/log** -volume verplicht. Write Accelerator is alleen beschikbaar voor Premium-opslag en M-serie en Mv2-Series Vm's. Write Accelerator werkt niet in combi natie met andere Azure VM-families, zoals Esv3 of Edsv4.

In de onderstaande cache aanbevelingen voor Azure Premium-schijven worden de I/O-kenmerken voor SAP HANA deze lijst als volgt aangenomen:

- Er is nauwelijks Lees werk belasting voor de HANA-gegevens bestanden. Uitzonde ringen zijn grote I/O's na het opnieuw opstarten van het HANA-exemplaar of wanneer gegevens in HANA worden geladen. Een ander geval van grotere Lees-I/O's tegen gegevens bestanden kan HANA-data base-back-ups zijn. Als gevolg hiervan is het meestal niet zinvol om in de meeste gevallen alle volumes van het gegevens bestand volledig te lezen. 
- Het schrijven van de gegevens bestanden wordt uitgevoerd in bursts op basis van HANA opslag punten en HANA-crash herstel. Het schrijven van opslag punten is asynchroon en houdt geen gebruikers transacties in handen. Het schrijven van gegevens tijdens het crash herstel is van essentieel belang om het systeem weer snel te laten reageren. Crash herstel moet echter in plaats daarvan uitzonderlijke situaties
- Er zijn nauwelijks Lees bewerkingen van de HANA-bestanden opnieuw. Uitzonde ringen zijn grote I/O's bij het uitvoeren van back-ups van transactie logboeken, crash herstel of in de fase voor het opnieuw opstarten van een HANA-exemplaar.  
- Hoofd belasting op basis van het SAP HANA opnieuw uitvoeren van het logboek bestand wordt geschreven. Afhankelijk van de aard van de werk belasting, kunt u een I/O's-grootte hebben van Maxi maal 4 KB of in andere I/O-bewerkingen van 1 MB of meer. Schrijf latentie voor het SAP HANA opnieuw uitvoeren van het logboek is essentieel voor de prestaties.
- Alle schrijf bewerkingen moeten op een betrouw bare manier op de schijf worden bewaard

**Aanbeveling: als gevolg van deze waargenomen I/O-patronen door SAP HANA, moet de cache voor de verschillende volumes die Azure Premium Storage gebruiken, worden ingesteld als:**

- **/Hana/data** -geen caching of lees cache
- **/Hana/log** : geen caching-uitzonde ring voor M-en Mv2-Series Vm's waarvoor Azure write Accelerator moet worden ingeschakeld 
- **/Hana/Shared** -cache lezen
- **Besturingssysteem schijf** -de standaard cache die door Azure is ingesteld tijdens het maken van de VM, niet wijzigen


Als u LVM of mdadm gebruikt om stripesets te maken op verschillende Azure Premium-schijven, moet u Stripe-grootten definiëren. Deze grootten verschillen tussen **/Hana/data** en **/Hana/log**. **Aanbeveling: als het formaat van de Stripe moet worden gebruikt:**

- 256 KB voor **/Hana/data**
- 64 KB voor **/Hana/log**

> [!NOTE]
> De Stripe-grootte voor **/Hana/data** is gewijzigd ten opzichte van eerdere aanbevelingen die 64 kb of 128 kb aan 256 KB aanroepen op basis van ervaringen van klanten met recentere Linux-versies. De omvang van 256 KB levert een iets betere prestaties. We hebben ook de aanbeveling voor Stripe-groottes van **/Hana/log** van 32 KB gewijzigd in 64 KB om voldoende door voer te krijgen met grotere I/O-grootten.

> [!NOTE]
> U hoeft geen redundantie niveau te configureren met behulp van RAID-volumes omdat Azure Block Storage drie installatie kopieën van een VHD behoudt. Het gebruik van een Stripe-set met Azure Premium-schijven is uitsluitend het configureren van volumes die voldoende IOPS en/of I/O-door Voer bieden.

Het samen voegen van een aantal Azure-Vhd's onder een Stripe-set is cumulatief van een IOPS en opslag doorvoer zijde. Als u dus een Stripe-set op meer dan 3 x P30 Azure Premium-opslag schijven plaatst, moet u er drie maal de IOPS en drie keer de opslag doorvoer van één Azure Premium Storage P30-schijf geven.

> [!IMPORTANT]
> Als u LVM of mdadm als volume manager gebruikt om stripesets te maken op meerdere Azure Premium-schijven, mogen de drie SAP Hana-bestandssysteem/data,/log en/Shared niet worden geplaatst in een standaard volume groep. Het is raadzaam om de richt lijnen voor Linux-leveranciers te volgen, die meestal afzonderlijke volume groepen maken voor/Data,/log en/Shared.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure burst-functionaliteit voor Premium-opslag
Voor Azure Premium-opslag schijven die kleiner zijn dan of gelijk zijn aan 512 GiB in capaciteit, wordt burst-functionaliteit aangeboden. De exacte manier waarop schijf bursting werkt, wordt beschreven in het artikel [schijf bursting](../../disk-bursting.md). Wanneer u het artikel leest, begrijpt u het concept van het aantal beschik bare IOPS en door Voer in de tijden dat de I/O-werk belasting lager is dan de nominale IOPS en de door Voer van de schijven (Zie [prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over de nominale door Voer). U gaat de Delta van IOPS en door Voer samen voegen tussen het huidige gebruik en de nominale waarden van de schijf. De bursts zijn beperkt tot Maxi maal 30 minuten.

De ideale gevallen waarin deze burst-functionaliteit kan worden gepland in, zijn waarschijnlijk de volumes of schijven die gegevens bestanden voor de verschillende DBMS bevatten. De I/O-werk belasting verwacht op die volumes, vooral met kleine tot middel grote systemen, moet er als volgt uitzien:

- Laag tot gemiddeld Lees werk, omdat gegevens in de cache in het geheugen worden opgeslagen, of als in het geval van HANA volledig in het geheugen moet worden bewaard
- Triggers van schrijven die zijn geactiveerd door database controlepunten of opslag punten die regel matig worden uitgegeven
- Backup-werk belasting die in een doorlopende stroom leest in gevallen waarbij back-ups niet worden uitgevoerd via opslag momentopnamen
- Voor SAP HANA worden de gegevens in het geheugen geladen nadat een instantie opnieuw is opgestart

Met name op kleinere DBMS-systemen waarbij uw werk belasting enkele honderden trans acties per seconde verwerkt, kan een dergelijke burst-functionaliteit ook zinvol zijn voor de schijven of volumes die de trans actie opslaan of het logboek opnieuw uitvoeren. Verwachte werk belasting voor een dergelijke schijf of volumes ziet er als volgt uit:

- Er wordt regel matig naar de schijf geschreven die afhankelijk is van de werk belasting en de aard van de werk belasting, omdat elke door voering uitgegeven door de toepassing waarschijnlijk een I/O-bewerking veroorzaakt
- Hogere werk belasting bij de door Voer voor gevallen van operationele taken, zoals het maken of opnieuw samen stellen van indexen
- Bursts lezen bij het uitvoeren van een transactie logboek of het opnieuw uitvoeren van back-ups van Logboeken


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Productie Aanbevolen opslag oplossing op basis van Azure Premium Storage

> [!IMPORTANT]
> SAP HANA certificering voor virtuele machines uit de M-serie van Azure bestaat uitsluitend met Azure Write Accelerator voor het **/Hana/log** -volume. Als gevolg hiervan wordt het productie scenario SAP HANA implementaties op virtuele machines uit de d-serie van Azure M naar verwachting geconfigureerd met Azure Write Accelerator voor het **/Hana/log** -volume.  

> [!NOTE]
> In scenario's waarbij Azure Premium Storage betrokken is, implementeren we burst-mogelijkheden in de configuratie. Zorg ervoor dat u bij het gebruik van hulpprogram ma's voor opslag testen van ongeacht welke vorm of elk formulier gebruikmaakt van de manier waarop [Azure Premium schijf bursting werkt](../../disk-bursting.md) . Als u de opslag tests uitvoert die worden geleverd via het SAP HWCCT-of HCMT-hulp programma, zullen we niet verwachten dat alle tests aan de criteria voldoen omdat sommige tests de bursting-tegoed overschrijden die u kunt verzamelen. Met name wanneer alle tests opeenvolgend worden uitgevoerd zonder te onderbreken.


> [!NOTE]
> Controleer voor productie scenario's of een bepaald VM-type wordt ondersteund voor SAP HANA door SAP in de [SAP-documentatie voor IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Aanbeveling: de aanbevolen configuraties met Azure Premium Storage voor productie scenario's zien er als volgt uit:**

Configuratie voor het SAP **/Hana/data** -volume:

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /hana/data | Ingerichte doorvoer | Maximale burst-door Voer | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14.000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14.000 |
| M64ls | 512 GiB | 1.000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2.000 | 14.000 |
| M64s | 1.000 GiB | 1.000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4.400 | 14.000 |
| M64ms | 1.750 GiB | 1.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9.200 | 14.000 |  
| M128s | 2.000 GiB | 2.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9.200| 14.000 | 
| M128ms | 3.800 GiB | 2.000 MBps | 4 x P30 | 800 MBps | geen bursting | 20.000 | geen bursting | 
| M208s_v2 | 2.850 GiB | 1.000 MBps | 4 x P30 | 800 MBps | geen bursting | 20.000| geen bursting | 
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 4 x P40 | 1.000 MBps | geen bursting | 30.000 | geen bursting |
| M416s_v2 | 5.700 GiB | 2.000 MBps | 4 x P40 | 1.000 MBps | geen bursting | 30.000 | geen bursting |
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 4 x P50 | 2.000 MBps | geen bursting | 30.000 | geen bursting |


Voor het **/Hana/log** -volume. de configuratie zou er als volgt uitzien:

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | **/Hana/log** volume | Ingerichte doorvoer | Maximale burst-door Voer | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10.500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10.500 | 
| M64ls | 512 GiB | 1.000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10.500 | 
| M64s | 1.000 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 
| M64ms | 1.750 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M128s | 2.000 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500|  
| M128ms | 3.800 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 
| M208s_v2 | 2.850 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M416s_v2 | 5.700 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 


Voor de andere volumes ziet de configuratie er als volgt uit:

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1.000 GiB | 1.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1.750 GiB | 1.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2.000 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3.800 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2.850 GiB | 1.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5.700 GiB | 2.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


Controleer of de opslag doorvoer voor de verschillende voorgestelde volumes voldoet aan de werk belasting die u wilt uitvoeren. Als voor de werk belasting hogere volumes zijn vereist voor **/Hana/data** en **/Hana/log**, moet u het aantal Azure Premium-opslag-vhd's verhogen. Het formaat van een volume met meer Vhd's dan wordt weer gegeven, verhoogt de IOPS en I/O-door Voer binnen de limieten van het type virtuele machine van Azure.

Azure Write Accelerator werkt alleen in combi natie met [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Ten minste de Azure Premium-opslag schijven die het **/Hana/log** -volume vormen, moeten worden geïmplementeerd als managed disks. Meer gedetailleerde instructies en beperkingen van Azure Write Accelerator vindt u in het artikel [Write Accelerator](../../how-to-enable-write-accelerator.md).

Voor de HANA-gecertificeerde Vm's van de Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) -familie en de [EDSV4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)moet u ANF voor het **/Hana/data** -en **/Hana/log** -volume. U moet ook gebruikmaken van de ultra schijf opslag van Azure in plaats van Azure Premium Storage voor het **/Hana/log** -volume. Als gevolg hiervan kunnen de configuraties voor het **/Hana/data** -volume in azure Premium Storage er als volgt uitzien:

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /hana/data | Ingerichte doorvoer | Maximale burst-door Voer | IOPS | Burst IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1500 | 10.500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1500 | 10.500|
| E48ds_v4 | 384 GiB | 1.152 MBps | 3 x P15 |  375 MBps |510 MBps | 3.300  | 10.500 | 
| E64ds_v4 | 504 GiB | 1.200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3.300 | 10.500 | 
| E64s_v3 | 432 GiB | 1.200 MB/s | 3 x P15 |  375 MBps | 510 MBps | 3.300 | 10.500 | 

Voor de andere volumes, met inbegrip van **/Hana/log** op ultra disk, kan de configuratie er als volgt uitzien:

| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /Hana/log volume | I/O-door Voer van/Hana/log | /Hana/log IOPS | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1.152 MBps | 192 GB | 250 MBps | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1.200 MBps | 256 GB | 250 MBps | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1.200 MBps | 220 GB | 250 MBps | 1800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuratie van de Azure Ultra Disk-opslag voor SAP HANA
Een ander Azure-opslag type heet [Azure Ultra Disk](../../disks-types.md#ultra-disk). Het aanzienlijke verschil tussen Azure Storage dat tot nu toe wordt aangeboden en ultra disk is dat de schijf mogelijkheden niet meer zijn gebonden aan de schijf grootte. Als klant kunt u deze mogelijkheden voor Ultra Disk definiëren:

- De grootte van een schijf, variërend van 4 GiB tot 65.536 GiB
- IOPS variëren van 100 IOPS tot 160K IOPS (maximum is ook afhankelijk van VM-typen)
- Opslag doorvoer van 300 MB/sec tot 2.000 MB/sec.

Ultra Disk biedt u de mogelijkheid om één schijf te definiëren die voldoet aan uw grootte, IOPS en bereik voor schijf doorvoer. In plaats van logische volume managers zoals LVM of MDADM boven op Azure Premium Storage te gebruiken om volumes te maken die voldoen aan de vereisten voor IOPS en opslag doorvoer. U kunt een configuratie mix uitvoeren tussen ultra disk en Premium Storage. Als gevolg hiervan kunt u het gebruik van ultra Disk beperken tot de essentiële **/Hana/data** -en **/Hana/log** -volumes en de andere volumes bedekken met Azure Premium Storage

Andere voor delen van ultra Disk kunnen de betere lees latentie zijn in vergelijking met Premium Storage. De snellere lees latentie kan de voor delen hebben wanneer u de HANA-opstart tijden en de daaropvolgende belasting van de gegevens in het geheugen wilt reduceren. Voor delen van ultra Disk-opslag kunnen ook vilt zijn wanneer HANA opslag punten schrijft. 

> [!NOTE]
> Ultra disk is nog niet in alle Azure-regio's aanwezig en biedt nog geen ondersteuning voor alle typen VM'S die hieronder worden weer gegeven. Raadpleeg het artikel wat zijn de [schijf typen die beschikbaar zijn in azure?](../../disks-types.md#ultra-disk)voor meer informatie over hoe Ultra Disk beschikbaar is en welke VM-families worden ondersteund.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Productie Aanbevolen opslag oplossing met zuivere Ultra schijf configuratie
In deze configuratie behoudt u de **/Hana/data** -en **/Hana/log** -volumes afzonderlijk. De voorgestelde waarden worden afgeleid van de Kpi's die door SAP moeten worden gecertificeerd voor SAP HANA-en opslag configuraties zoals aanbevolen in het [Witboek SAP TDI-opslag](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

De aanbevelingen overschrijden vaak de minimum vereisten van SAP zoals eerder in dit artikel is beschreven. De vermelde aanbevelingen zijn een inbreuk tussen de grootte aanbevelingen van SAP en de maximale opslag doorvoer voor de verschillende typen VM'S.

> [!NOTE]
> Azure Ultra Disk dwingt ten minste 2 IOPS per Gigabyte capaciteit van een schijf af


| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /Hana/data volume | I/O-door Voer van/Hana/data | /Hana/data IOPS | /Hana/log volume | I/O-door Voer van/Hana/log | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MBps | 2500 | 80 GB | 250 MB | 1800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBps | 2500 | 128 GB | 250 MBps | 1800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MBps | 3000 | 192 GB | 250 MBps | 1800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MBps | 3500 |  256 GB | 250 MBps | 1800 |
| E64s_v3 | 432 GiB | 1.200 MB/s | 610 GB | 400 MBps | 3500 | 220 GB | 250 MB | 1800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 2500 | 96 GB | 250 MBps  | 1800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 2500 | 256 GB | 250 MBps  | 1800 |
| M64ls | 512 GiB | 1.000 MB/s | 620 GB | 400 MBps | 3500 | 256 GB | 250 MBps  | 1800 |
| M64s | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 5\.000 | 512 GB | 250 MBps  | 2500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 5\.000 | 512 GB | 250 MBps  | 2500 |
| M128s | 2.000 GiB | 2.000 MB/s |2.400 GB | 750 MBps | 7\.000 | 512 GB | 250 MBps  | 2500 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 750 MBps |9600 | 512 GB | 250 MBps  | 2500 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 750 MBps | 7\.000 | 512 GB | 250 MBps  | 2500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 750 MBps | 14.400 | 512 GB | 250 MBps  | 2500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.000 MBps | 14.400 | 512 GB | 400 MBps  | 4000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 MBps | 28.800 | 512 GB | 400 MBps  | 4000 |   

**De vermelde waarden zijn bedoeld als uitgangs punt en moeten worden geëvalueerd op basis van de werkelijke vereisten.** Het voor deel van Azure Ultra disk is dat de waarden voor IOPS en door Voer kunnen worden aangepast zonder de nood zaak om de virtuele machine af te sluiten of de werk belasting die op het systeem wordt toegepast, te onderbreken.   

> [!NOTE]
> Tot nu toe is opslag momentopnamen met ultra Disk Storage niet beschikbaar. Hiermee wordt het gebruik van VM-moment opnamen met Azure Backup services geblokkeerd


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS v 4.1-volumes op Azure NetApp Files
Lees voor meer informatie over ANF voor HANA het document [NFS v 4.1-volumes op Azure NetApp files voor SAP Hana](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Prijs bewuste oplossing met Azure Premium Storage
Tot nu toe zijn de Azure Premium Storage-oplossing die in dit document wordt beschreven in sectie [oplossingen met Premium Storage en azure write Accelerator voor virtuele machines uit de M-serie van Azure](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) , bedoeld voor het SAP Hana van productie-ondersteunde scenario's. Een van de kenmerken van productie configuraties die kunnen worden ondersteund, is de schei ding van de volumes voor SAP HANA gegevens en meld u opnieuw aan op twee verschillende volumes. De reden hiervoor is dat de kenmerken van de werk belasting op de volumes verschillend zijn. En dat met de voorgestelde productie configuraties, kan een ander type caching of zelfs verschillende typen Azure-blok opslag nodig zijn. Voor niet-productie scenario's is het mogelijk dat een aantal van de overwegingen voor productie systemen niet van toepassing is op meer niet-productie systemen met een lage eind tijd. Als gevolg hiervan kunnen de HANA-gegevens en het logboek volume worden gecombineerd. Uiteindelijk met een aantal culprits, zoals uiteindelijk geen bepaalde doorvoer-of latentie-Kpi's die vereist zijn voor productie systemen. Een ander aspect voor het verlagen van de kosten in dergelijke omgevingen kan het gebruik zijn van [Azure Standard-SSD Storage](./planning-guide-storage.md#azure-standard-ssd-storage). Denk eraan dat het kiezen van Standard-SSD of Standard-HDD Azure-opslag invloed heeft op uw enkele VM-Sla's zoals beschreven in het artikel  [Sla voor virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Een kostenbesparend alternatief voor dergelijke configuraties kan er als volgt uitzien:


| VM-SKU | RAM | Met maximaal VM-I/O<br /> Doorvoer | /Hana/data en/Hana/log<br /> Striped met LVM of MDADM | /hana/shared | /root volume | /usr/sap | aantekeningen |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Niet langer dan 1ms opslag latentie<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM-type is niet gecertificeerd voor HANA <br /> Niet langer dan 1ms opslag latentie<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 5.000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Niet langer dan 1ms opslag latentie<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM-type is niet gecertificeerd voor HANA <br /> Niet langer dan 1ms opslag latentie<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Niet langer dan 1ms opslag latentie<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 5.000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1.152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Niet langer dan 1ms opslag latentie<sup>1</sup> |
| E64v3 | 432 GiB | 1.200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Niet langer dan 1ms opslag latentie<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Niet langer dan 1ms opslag latentie<sup>1</sup> |
| M64ls | 512 GiB | 1.000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 10.000<sup>2</sup> |
| M64s | 1.000 GiB | 1.000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 10.000<sup>2</sup> |
| M64ms | 1.750 GiB | 1.000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 10.000<sup>2</sup> |
| M128s | 2.000 GiB | 2.000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 20.000<sup>2</sup> |
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 10.000<sup>2</sup> |
| M128ms | 3.800 GiB | 2.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 20.000<sup>2</sup> |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 10.000<sup>2</sup> |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 20.000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Als u Write Accelerator gebruikt voor gecombineerde gegevens en het logboek volume, wordt de IOPS-frequentie beperkt tot 20.000<sup>2</sup> |


<sup>1</sup> [Azure write Accelerator](../../how-to-enable-write-accelerator.md) kan niet worden gebruikt met de VM-families Ev4 en Ev4. Als gevolg van het gebruik van Azure Premium Storage is de I/O-latentie niet kleiner dan 1ms

<sup>2</sup> de VM-serie ondersteunt [Azure write Accelerator](../../how-to-enable-write-accelerator.md), maar er is een kans dat de limiet voor IOPS van de schrijf versnelling de schijf configuraties kan beperken de IOPS-mogelijkheden

In het geval van het combi neren van het gegevens-en logboek volume voor SAP HANA, mag de schijven waarop het striped volume wordt gebouwd geen lees-of lees-en schrijf cache hebben ingeschakeld.

Er worden VM-typen weer gegeven die niet zijn gecertificeerd met SAP en die niet worden vermeld in de zogenaamde [SAP Hana hardware-map](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Feedback van klanten was dat deze niet-vermelde VM-typen zijn gebruikt voor een aantal niet-productie taken.


## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie:

- [SAP Hana hand leiding voor maximale Beschik baarheid voor virtuele machines van Azure](./sap-hana-availability-overview.md).