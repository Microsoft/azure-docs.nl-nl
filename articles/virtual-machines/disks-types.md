---
title: Selecteer een schijf type voor Azure IaaS Vm's-beheerde schijven
description: Meer informatie over de beschik bare Azure-schijf typen voor virtuele machines, waaronder Ultra schijven, Premium Ssd's, Standard Ssd's en Standard Hdd's.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500764"
---
# <a name="what-disk-types-are-available-in-azure"></a>Welke schijftypen zijn beschikbaar in Azure?

Azure Managed disks biedt momenteel vier schijf typen, elk type is gericht op specifieke klant scenario's.

## <a name="disk-comparison"></a>Vergelijking van schijven

De volgende tabel bevat een vergelijking van ultra disks, Premium-schijven (Solid-state drives), standaard SSD en standaard harde schijven (HDD) voor beheerde schijven om u te helpen bepalen wat u wilt gebruiken.

| Detail | Ultraschijven | Premium SSD | Standard SSD | Standard HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Schijftype   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |I/o-intensieve workloads, zoals [SAP Hana](workloads/sap/hana-vm-operations-storage.md), data bases in de bovenste laag (bijvoorbeeld SQL, Oracle) en andere trans acties met een zware werk belasting.   |Productie- en prestatiegevoelige workloads   |Webservers, bedrijfstoepassingen die weinig wordt gebruikt en dev/test   |Back-up, niet-kritiek, incidentele toegang   |
|Maximale schijfgrootte   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Maximale doorvoer   |2.000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Max. IOPS   |160.000    |20.000   |6.000   |2.000   |

## <a name="ultra-disk"></a>Ultraschijven

Azure-ultraschijven leveren een hoge doorvoer, hoge IOPS en een consistente schijfopslag met lage latentie voor Azure IaaS-VM's. Enkele extra voor delen van ultra schijven zijn de mogelijkheid om de prestaties van de schijf en uw workloads dynamisch te wijzigen zonder dat u uw virtuele machines (VM) opnieuw hoeft op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, databases in de bovenste laag en workloads met veel transacties. Ultraschijven kunnen uitsluitend als gegevensschijven worden gebruikt. U wordt aangeraden Premium SSD-schijven als besturingssysteemschijven te gebruiken.

### <a name="performance"></a>Prestaties

Wanneer u een ultra schijf inricht, kunt u onafhankelijk de capaciteit en de prestaties van de schijf configureren. Ultra disks zijn beschikbaar in verschillende vaste formaten, variërend van 4 GiB tot 64 TiB en bieden een flexibel prestatie configuratie model waarmee u IOPS en door Voer onafhankelijk kunt configureren.

Enkele belang rijke mogelijkheden van ultra disk zijn:

- Schijf capaciteit: schijven met een capaciteit van Maxi maal 4 GiB tot 64 TiB.
- Schijf-IOPS: Ultra schijven ondersteunen IOPS-limieten van 300 IOPS/GiB, Maxi maal 160 K IOPS per schijf. Zorg ervoor dat de geselecteerde schijf-IOPS kleiner is dan de limiet van de VM-IOPS om de IOPS die u hebt ingericht, te bewerkstelligen. De minimale gegarandeerde IOPS per schijf is 2 IOPS/GiB, met een totaal van de basis lijn mini maal 100 IOPS. Als u bijvoorbeeld een ultra schijf van 4 GiB hebt, hebt u Mini maal 100 IOPS, in plaats van acht IOPS.
- Schijf doorvoer: met ultra disks is de doorvoer limiet van één schijf 256 KiB/s voor elke ingerichte IOPS, tot een maximum van 2000 MBps per schijf (waarbij MBps = 10 ^ 6 bytes per seconde). De minimale gegarandeerde door Voer per schijf is 4KiB/s voor elke ingerichte IOPS, met een totaal van Mini maal 1 MBps voor de basis lijn.
- Ultra disks ondersteunen het aanpassen van de schijf prestatie kenmerken (IOPS en door Voer) tijdens runtime zonder dat de schijf wordt losgekoppeld van de virtuele machine. Zodra de bewerking voor het wijzigen van de schijf prestaties op een schijf is uitgevoerd, kan het tot een uur duren voordat de wijzigingen van kracht worden. Er geldt een limiet van vier bewerkingen voor de grootte van de prestaties tijdens een periode van 24 uur. Het is mogelijk dat de bewerking voor het wijzigen van de prestaties mislukt als gevolg van een gebrek aan prestatie bandbreedte capaciteit.

### <a name="disk-size"></a>Schijfgrootte

|Schijf grootte (GiB)  |IOPS Cap  |Doorvoer limiet (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2.400         |600         |
|16     |4800         |1200         |
|32     |9600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |153.600         |2.000         |
|1024-65536 (grootten in dit bereik worden verhoogd in stappen van 1 TiB)     |160.000         |2.000         |

Ultra disks zijn ontworpen om latentie van submilliseconden en doel-IOPS en-door Voer beschreven in de voor gaande tabel 99,99% van de tijd te geven.

### <a name="ga-scope-and-limitations"></a>GA bereik en beperkingen

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Als u ultra disks wilt gaan gebruiken, raadpleegt u ons artikel over het onderwerp: [Azure Ultra Disk gebruiken](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>Premium SSD

Azure Premium Ssd's bieden ondersteuning voor hoge prestaties en lage latentie voor virtuele machines (Vm's) met invoer/uitvoer-intensieve workloads. Als u gebruik wilt maken van de snelheid en prestaties van Premium-opslag schijven, kunt u bestaande VM-schijven migreren naar Premium-Ssd's. Premium-Ssd's zijn geschikt voor essentiële productie toepassingen. Premium-Ssd's kan alleen worden gebruikt in combi natie met een VM-reeks die compatibel is met Premium Storage.

Zie [grootten voor virtuele machines in azure](sizes.md)voor meer informatie over de verschillende VM-typen en-grootten in azure voor Windows of Linux, waaronder de grootte van Premium Storage-compatibel. In dit artikel moet u elk artikel van de afzonderlijke VM-grootte controleren om te bepalen of het een Premium-opslag compatibel is.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Wanneer u een Premium Storage-schijf inricht, bent u, in tegenstelling tot standaard opslag, verzekerd van de capaciteit, IOPS en doorvoer van die schijf. Als u bijvoorbeeld een P50-schijf maakt, richt Azure een schijf in met een opslagcapaciteit van 4.095 GB, 7.500 IOPS en een doorvoer van 250 MB/s. Uw toepassing kan alle of een deel van de capaciteit en prestaties gebruiken. Premium SSD-schijven zijn zodanig ontworpen dat er in de voorgaande tabel 99,9% van de tijd minder dan één milliseconde latentie en doel-IOPS en -doorvoer wordt beschreven.

## <a name="bursting"></a>Toepassingen

Premium-SSD grootten die kleiner zijn dan P30 bieden nu schijf bursting en kunnen de IOPS per schijf tot 3.500 en de band breedte van Maxi maal 170 MB/s. Bursting wordt geautomatiseerd en werkt op basis van een tegoed systeem. Tegoeden worden automatisch opgeteld in een burst-Bucket wanneer het schijf verkeer lager is dan het ingerichte prestatie doel en de tegoeden automatisch worden gebruikt wanneer het verkeer groter wordt dan het doel, tot de maximale burst-limiet. De maximale burst-limiet bepaalt het maximum van schijf-IOPS & band breedte, zelfs als u burst-tegoeden hebt om te gebruiken. Schijf bursting biedt betere tolerantie voor onvoorspelbare wijzigingen van i/o-patronen. U kunt dit het beste gebruiken voor het opstarten van de besturingssysteem schijf en toepassingen met piekige-verkeer.    

Schijven die ondersteuning bieden voor bursting, worden standaard ingeschakeld voor nieuwe implementaties van toepasselijke schijf grootten, zonder dat de gebruiker actie hoeft te ondernemen. Voor bestaande schijven van de toepasselijke grootten kunt u bursting met een van de volgende twee opties inschakelen: Ontkoppel en koppel de schijf opnieuw of stop de gekoppelde VM. Alle burst-toepasselijke schijf grootten beginnen met een volledige burst-credit Bucket wanneer de schijf is gekoppeld aan een virtuele machine die een maximale duur van Maxi maal 30 minuten ondersteunt. Zie [Premium-SSD bursting](./disk-bursting.md)voor meer informatie over hoe bursting werkt op Azure-schijven. 

### <a name="transactions"></a>Transacties

Voor Premium-Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB.

## <a name="standard-ssd"></a>Standard SSD

Azure Standard Ssd's is een voordelige opslag optie die is geoptimaliseerd voor workloads die consistente prestaties op lagere IOPS-niveaus nodig hebben. Standard-SSD biedt een goede ervaring op instap niveau voor degenen die willen overstappen op de Cloud, met name als u problemen ondervindt met de variantie van werk belastingen die op uw HDD-oplossingen op locatie worden uitgevoerd. In vergelijking met de standaard-Hdd's bieden de standaard-Ssd's betere Beschik baarheid, consistentie, betrouw baarheid en latentie. Standaard Ssd's zijn geschikt voor webservers, lage IOPS-toepassings servers, intensief gebruikte zakelijke toepassingen en werk belastingen voor ontwikkelen en testen. Net als standaard Hdd's zijn standaard Ssd's beschikbaar op alle Azure-Vm's.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standaard Ssd's zijn ontworpen om latentie van één cijfer en de IOPS en door voer te bieden tot de limieten die zijn beschreven in de voor gaande tabel 99% van de tijd. De werkelijke IOPS en door Voer kunnen soms variëren, afhankelijk van de verkeers patronen. Standaard Ssd's bieden consistente prestaties dan de harde schijven met een lagere latentie.

### <a name="transactions"></a>Transacties

Voor standaard Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB. Deze trans acties hebben een facturerings impact.

## <a name="standard-hdd"></a>Standard HDD

Azure Standard Hdd's levert betrouw bare, voordelige schijf ondersteuning voor virtuele machines met latentie-ongevoelig werk belastingen. Bij standaard opslag worden de gegevens opgeslagen op de harde schijven (Hdd's). Latentie, IOPS en door Voer van Standard-HDD schijven kunnen veel meer verschillen in vergelijking met schijven op basis van SSD. Standard-HDD schijven zijn ontworpen voor het leveren van schrijf latentie onder 10 MS en lees latentie onder 20ms voor de meeste i/o-bewerkingen, maar de werkelijke prestaties kunnen echter variëren, afhankelijk van de i/o-grootte en het werkbelasting patroon. Wanneer u werkt met Vm's, kunt u standaard HDD-schijven gebruiken voor dev/test-scenario's en minder kritieke workloads. Standaard Hdd's zijn beschikbaar in alle Azure-regio's en kunnen worden gebruikt met alle Azure-Vm's.

### <a name="disk-size"></a>Schijfgrootte
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transacties

Voor standaard Hdd's wordt elke IO-bewerking beschouwd als één trans actie, ongeacht de I/O-grootte. Deze trans acties hebben een facturerings impact.

## <a name="billing"></a>Billing

Bij het gebruik van beheerde schijven gelden de volgende facturerings overwegingen:

- Schijftype
- Grootte van beheerde schijf
- Momentopnamen
- Uitgaande gegevens overdracht
- Aantal trans acties

**Beheerde schijf grootte**: beheerde schijven worden gefactureerd op de ingerichte grootte. Azure wijst de ingerichte grootte (afgerond) toe aan de dichtstbijzijnde schijf grootte. Zie de vorige tabellen voor meer informatie over de geboden schijf groottes. Elke schijf wordt toegewezen aan een ondersteunde aanbieding van de ingerichte schijf en wordt dienovereenkomstig gefactureerd. Als u bijvoorbeeld een 200 GiB-Standard-SSD hebt ingericht, wordt deze toegewezen aan de aanbieding voor de schijf grootte van E15 (256 GiB). Facturering voor een ingerichte schijf wordt per uur naar beneden gefactureerd met behulp van de maandelijkse prijs voor de opslag aanbieding. Als u bijvoorbeeld een E10-schijf hebt ingericht en deze na 20 uur hebt verwijderd, wordt u gefactureerd voor het E10-aanbod dat Maxi maal 20 uur in rekening wordt gebracht. Dit is ongeacht de hoeveelheid gegevens die naar de schijf wordt geschreven.

**Moment opnamen**: moment opnamen worden gefactureerd op basis van de gebruikte grootte. Als u bijvoorbeeld een moment opname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GiB en de daad werkelijke gebruikte gegevens grootte van 10 GiB, wordt de moment opname alleen gefactureerd voor de gebruikte gegevens grootte van 10 GiB.

Zie de sectie over moment opnamen in het [overzicht Managed Disk](managed-disks-overview.md)voor meer informatie over moment opnamen.

**Uitgaande gegevens overdrachten**: [uitgaande gegevens overdracht](https://azure.microsoft.com/pricing/details/bandwidth/) (gegevens die uit de Azure-data centers worden verzonden) zijn er facturen voor bandbreedte gebruik.

**Trans acties**: u wordt gefactureerd voor het aantal trans acties dat u uitvoert op een Standard Managed disk. Voor standaard Ssd's wordt elke I/O-bewerking kleiner dan of gelijk aan 256 KiB van de door Voer beschouwd als één I/O-bewerking. I/O-bewerkingen die groter zijn dan 256 KiB door Voer, worden beschouwd als meerdere I/O's van grootte 256 KiB. Voor standaard Hdd's wordt elke IO-bewerking beschouwd als één trans actie, ongeacht de I/O-grootte.

Zie [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks)voor gedetailleerde informatie over de prijzen voor Managed disks, waaronder transactie kosten.

### <a name="ultra-disk-vm-reservation-fee"></a>Kosten voor de reserve ring van ultra schijf-VM

Azure-Vm's hebben de mogelijkheid om aan te geven of ze compatibel zijn met ultra disks. Een ultra Disk-compatibele VM wijst toegewezen bandbreedte capaciteit toe tussen het Compute-VM-exemplaar en de schaal eenheid voor blok opslag om de prestaties te optimaliseren en de latentie te verminderen. Het toevoegen van deze mogelijkheid op de VM resulteert in een reserverings kosten die alleen worden opgelegd als u ultra Disk-functionaliteit op de virtuele machine hebt ingeschakeld zonder een ultra schijf aan de VM te koppelen. Wanneer een ultra schijf is gekoppeld aan de ultra Disk compatible VM, worden deze kosten niet toegepast. Deze kosten zijn per vCPU ingericht op de VM. 

> [!Note]
> Voor [beperkte core VM-grootten](constrained-vcpu.md)zijn de reserverings kosten gebaseerd op het werkelijke aantal vcpu's en niet de beperkte kernen. Voor Standard_E32-8s_v3 zijn de reserverings kosten gebaseerd op 32 kernen. 

Raadpleeg de [pagina met prijzen voor Azure](https://azure.microsoft.com/pricing/details/managed-disks/) disks voor Ultra Disk-prijs informatie.

### <a name="azure-disk-reservation"></a>Azure-schijf reservering

Schijf reservering is de mogelijkheid om één jaar aan schijf opslag vooraf te kopen tegen een korting, waardoor de totale kosten worden verminderd. Wanneer u een schijf reservering aanschaft, selecteert u een specifieke schijf-SKU in een doel regio, bijvoorbeeld 10 P30 (1TiB) Premium Ssd's in de regio VS Oost 2 voor een periode van één jaar. De reserverings ervaring is vergelijkbaar met de gereserveerde exemplaren van virtuele machines (VM). U kunt virtuele machines en schijf reserveringen bundelen om uw besparingen te maximaliseren. De Azure Disk reservation biedt nu één jaar toezeggings plan voor Premium SSD Sku's van P30 (1TiB) tot P80 (32 TiB) in alle productie regio's. Zie de [pagina met prijzen voor Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over de prijzen voor gereserveerde schijven.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks/) om aan de slag te gaan.