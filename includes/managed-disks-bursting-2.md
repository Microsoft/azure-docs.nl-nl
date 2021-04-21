---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820999"
---
## <a name="disk-level-bursting"></a>Bursting op schijfniveau

### <a name="on-demand-bursting-preview"></a>Bursting op aanvraag (preview)

Schijven die gebruikmaken van het bursting-model op aanvraag van schijf bursting kunnen bursts uitvoeren buiten de oorspronkelijke inrichtende doelen, zo vaak als nodig door hun workload, tot het maximale burstdoel. Op een P30-schijf van 1 TiB is de inrichtende IOPS bijvoorbeeld 5000 IOPS. Wanneer schijf bursting is ingeschakeld op deze schijf, kunnen uw workloads IO's aan deze schijf uitgeven tot de maximale burst-prestaties van 30.000 IOPS en 1000 MBps.

Als u verwacht dat uw workloads vaak buiten het inrichten van het doel worden uitgevoerd, is schijf bursting niet rendabel. In dit geval raden we u aan de prestatielaag van uw schijf te wijzigen in een hogere laag, voor betere basislijnprestaties. [](../articles/virtual-machines/disks-performance-tiers.md) Controleer uw factureringsgegevens en beoordeel deze op het verkeerspatroon van uw workloads.

Voordat u bursting op aanvraag inschakelen, moet u het volgende begrijpen:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Billing

Voor schijven die gebruikmaken van het burstingmodel op aanvraag worden vaste kosten per uur in rekening gebracht voor burst- en transactiekosten die van toepassing zijn op burst-transacties buiten het inrichtende doel. Transactiekosten worden in rekening gebracht volgens het betalen per gebruik-model, op basis van niet-gecachede schijf-IO's, inclusief lees- en schrijfgegevens die de ingerichte doelen overschrijden. Hier volgt een voorbeeld van schijfverkeerspatronen gedurende een factureringsuur:

Schijfconfiguratie: Premium - SSD – 1 TiB (P30), Schijf bursting ingeschakeld.

- 00:00:00 – 00:10:00 Schijf-IOPS onder het inrichtende doel van 5000 IOPS 
- 00:10:01 – 00:10:10 Application issued a batch job causing the disk IOPS to burst at 6,000 IOPS for 10 seconds 
- 00:10:11 – 00:59:00 Schijf-IOPS onder het inrichtende doel van 5000 IOPS 
- 00:59:01 – 01:00:00 Application issued another batch job causing the disk IOPS to burst at 7,000 IOPS for 60 seconds 

In dit factureringsuur bestaan de kosten van bursting uit twee kosten:

De eerste kosten zijn de vaste kosten voor burst-$X (bepaald door uw regio). Deze vaste kosten worden altijd in rekening gebracht op de schijf die de status van het koppelen negeert totdat deze is uitgeschakeld. 

Ten tweede zijn de burst-transactiekosten. Schijf bursting heeft plaatsgevonden in twee tijdvakken. Van 00:10:01 – 00:10:10 is de verzamelde bursttransactie (6.000 – 5.000) X 10 = 10.000. Van 00:59:01 – 01:00:00 is de geaccumuleerde bursttransactie (7.000 – 5.000) X 60 = 120.000. De totale burst-transacties zijn 10.000 + 120.000 = 130.000. Burst-transactiekosten worden in rekening gebracht $Y op basis van 13 eenheden van 10.000 transacties (op basis van regionale prijzen).

Hiermee zijn de totale kosten voor schijf bursting van dit factureringsuur gelijk aan $X + $Y. Dezelfde berekening is van toepassing op bursting over het inrichten van het doel van MBps. We vertalen de uitval van MB naar transacties met een IO-grootte van 256 KB. Als uw schijfverkeer de inrichtende IOPS en het MBps-doel overschrijdt, kunt u het onderstaande voorbeeld gebruiken om de burst-transacties te berekenen. 

Schijfconfiguratie: Premium - SSD – 1 TB (P30), Schijf bursting ingeschakeld.

- 00:00:01 – 00:00:05 Toepassing heeft een batch-taak uitgegeven waardoor de schijf-IOPS vijf seconden kan bursten op 10.000 IOPS en 300 MBps.
- 00:00:06 – 00:00:10 De toepassing heeft een herstel taak uitgegeven, waardoor de schijf-IOPS vijf seconden kan bursten naar 6000 IOPS en 600 MBps.

De burst-transactie wordt rekening gehouden met het maximum aantal transacties van IOPS- of MBps-bursting. Van 00:00:01 – 00:00:05 is de verzamelde burst-transactie maximaal ((10.000 – 5.000), (300 - 200) * 1024 / 256)) * 5 = 25.000 transacties. Van 00:00:06 – 00:00:10 is de verzamelde burst-transactie maximaal ((6.000 – 5.000), (600 - 200) * 1024 / 256)) * 5 = 8000 transacties. Daarnaast moet u de vaste kosten voor burst-inschakelen opnemen om de totale kosten te krijgen voor het inschakelen van schijf bursting op basis van on-demand. 

Raadpleeg de pagina met Managed Disks [voor](https://azure.microsoft.com/pricing/details/managed-disks/) meer informatie over prijzen en gebruik de Azure-prijscalculator om de evaluatie voor uw workload uit te voeren. [](https://azure.microsoft.com/pricing/calculator/?service=storage) 


Zie Bursting op aanvraag inschakelen als u bursting op aanvraag [wilt inschakelen.](../articles/virtual-machines/disks-enable-bursting.md)

### <a name="credit-based-bursting"></a>Bursting op basis van tegoeden

Bursting op basis van tegoeden is beschikbaar voor schijfgrootten P20 en kleiner in alle regio's in openbare Azure-clouds, overheids- en China-clouds. Standaard is schijf bursting ingeschakeld voor alle nieuwe en bestaande implementaties van ondersteunde schijfgrootten. Bursting op VM-niveau maakt alleen gebruik van bursts op basis van tegoeden.

## <a name="virtual-machine-level-bursting"></a>Bursting op het niveau van virtuele machines

Bursting op VM-niveau maakt alleen gebruik van het op tegoed gebaseerde model voor bursting. Het is standaard ingeschakeld voor alle VM's die dit ondersteunen.

Bursting op VM-niveau is ingeschakeld in alle regio's in de openbare Azure-cloud voor de volgende ondersteunde grootten: 
- [Lsv2-serie](../articles/virtual-machines/lsv2-series.md)
- [Dv3- en DSv3-serie](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3- en Esv3-serie](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Bursting-stroom

Het bursting-tegoedsysteem wordt op dezelfde manier toegepast op zowel het VM-niveau als het schijfniveau. Uw resource, ofwel een VM of schijf, begint met volledig opgeslagen tegoeden in een eigen burst-bucket. Met deze tegoeden kunt u maximaal 30 minuten bursten met de maximale burstsnelheid. U verzamelt tegoeden wanneer de IOPS of MB/s van de resource worden gebruikt onder het prestatiedoel van de resource. Als uw resource opgebouwde bursting-tegoeden heeft en uw workload de extra prestaties nodig heeft, kan uw resource deze tegoeden gebruiken om de prestatielimieten te overschrijden en de prestaties te verhogen om te voldoen aan de workloadbehoeften.

![Bursting-bucketdiagram.](media/managed-disks-bursting/bucket-diagram.jpg)

Hoe u uw beschikbare tegoeden uitgeeft, is aan u. U kunt gedurende de hele dag gedurende 30 minuten aan burst-tegoeden achtereenvolgens of sporadisch gebruiken. Wanneer resources worden geïmplementeerd, krijgen ze een volledige toewijzing van tegoeden. Wanneer deze zijn afput, duurt het minder dan een dag om weer aan te vullen. Tegoeden kunnen naar eigen goedheid worden besteed. De burst-bucket hoeft niet vol te zijn om resources te kunnen bursten. Burstaccumulatie varieert afhankelijk van elke resource, omdat deze is gebaseerd op ongebruikte IOPS en MB/s onder de prestatiedoelen. Resources met betere basislijnprestaties kunnen hun bursting-tegoeden sneller genereren dan resources die minder basislijnprestaties uitvoeren. Een P1-schijf-id zal bijvoorbeeld 120 IOPS per seconde genereren, terwijl een P20-schijf met een id 2300 IOPS per seconde zou genereren.

## <a name="bursting-states"></a>Bursting-staten
Er zijn drie staten waarin bursting kan zijn ingeschakeld voor uw resource:
- **Accruing:** het I/O-verkeer van de resource gebruikt minder dan het prestatiedoel. Het verzamelen van bursting-tegoeden voor IOPS en MB/s wordt los van elkaar uitgevoerd. Uw resource kan IOPS-tegoed ontvangen en MB/s-tegoeden uitgeven of vice versa.
- **Bursting:** het verkeer van de resource gebruikt meer dan het prestatiedoel. Het burst-verkeer verbruikt onafhankelijk tegoed van IOPS of bandbreedte.
- **Constant:** het verkeer van de resource staat precies op het prestatiedoel.

## <a name="bursting-examples"></a>Voorbeelden van bursting

De volgende voorbeelden laten zien hoe bursting werkt met verschillende VM- en schijfcombinaties. Om de voorbeelden gemakkelijk te volgen, richten we ons op MB/s, maar dezelfde logica wordt onafhankelijk toegepast op IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Niet-burstbare virtuele machine met burstable schijven
**Combinatie van VM en schijf:** 
- Standard_D8as_v4 
    - Niet-gecached MB/s: 192
- P4-besturingssysteemschijf
    - Inrichten van MB/s: 25
    - Max. burst MB/s: 170 
- 2 P10-gegevensschijven 
    - Inrichten van MB/s: 100
    - Max. burst MB/s: 170

 Wanneer de VM wordt opgevraagd, worden gegevens opgehaald van de besturingssysteemschijf. Omdat de besturingssysteemschijf deel uitmaakt van een VM die wordt opgestart, zit de besturingssysteemschijf vol bursting-tegoeden. Met deze tegoeden kan de besturingssysteemschijf worden gestart met 170 MB/s seconde.

![VM verzendt een aanvraag voor 192 MB/s doorvoer naar de besturingssysteemschijf, de besturingssysteemschijf reageert met 170 MB/s gegevens.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Nadat het opstarten is voltooid, wordt een toepassing uitgevoerd op de VM en heeft deze een niet-kritieke workload. Deze workload vereist 15 MB/S die gelijkmatig over alle schijven wordt verdeeld.

![Toepassing verzendt aanvraag voor 15 MB/s doorvoer naar VM, VM neemt aanvraag en verzendt elke schijf een aanvraag voor 5 MB/s, elke schijf retourneert 5 MB/s, VM retourneert 15 MB/s naar de toepassing.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Vervolgens moet de toepassing een batch-job verwerken die 192 MB/s vereist. 2 MB/s worden gebruikt door de besturingssysteemschijf en de rest wordt gelijkmatig verdeeld over de gegevensschijven.

![Toepassing verzendt aanvraag voor 192 MB/s doorvoer naar VM, VM neemt aanvraag en verzendt het merendeel van de aanvraag naar de gegevensschijven (elk 95 MB/s) en 2 MB/s naar de besturingssysteemschijf, de gegevensschijven bursten om aan de vraag te voldoen en alle schijven retourneren de aangevraagde doorvoer naar de VM, die deze naar de toepassing retourneert.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Burstable virtuele machine met niet-burstable schijven
**Combinatie van VM en schijf:** 
- Standard_L8s_v2 
    - Niet-gecached MB/s: 160
    - Max. burst MB/s: 1280
- P50-besturingssysteemschijf
    - Inrichten van MB/s: 250 
- 2 P10-gegevensschijven 
    - Inrichten van MB/s: 250

 Nadat de eerste keer is opgestart, wordt een toepassing uitgevoerd op de VM en heeft deze een niet-kritieke workload. Deze workload vereist 30 MB/s die gelijkmatig over alle schijven worden verdeeld.
![Toepassing verzendt aanvraag voor 30 MB/s doorvoer naar VM, VM neemt aanvraag en verzendt elke schijf een aanvraag voor 10 MB/s, elke schijf retourneert 10 MB/s, VM retourneert 30 MB/s naar de toepassing.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Vervolgens moet de toepassing een batch-taak verwerken die 600 MB/s vereist. De Standard_L8s_v2 bursts om aan deze vraag te voldoen en aanvragen naar de schijven worden gelijkmatig verdeeld over P50-schijven.

![Toepassing verzendt aanvraag voor 600 MB/s doorvoer naar VM, VM neemt bursts om de aanvraag te verwerken en verzendt elke schijf een aanvraag voor 200 MB/s, elke schijf retourneert 200 MB/s, VM-bursts om 600 MB/s naar de toepassing te retourneren.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Burstable virtuele machine met burstable schijven
**Combinatie van VM en schijf:** 
- Standard_L8s_v2 
    - Niet-gecached MB/s: 160
    - Maximale burst MB/s: 1280
- P4-besturingssysteemschijf
    - Inrichten van MB/s: 25
    - Max. burst MB/s: 170 
- 2 P4-gegevensschijven 
    - Inrichten van MB/s: 25
    - Max. burst MB/s: 170 

Wanneer de VM wordt gestart, wordt de burst-limiet van 1280 MB/s van de besturingssysteemschijf gevraagd en reageert de besturingssysteemschijf met de burstprestaties van 170 MB/s.

![Bij het opstarten stuurt de VM een aanvraag van 1280 MB/s naar de besturingssysteemschijf, met bursts van besturingssysteemschijven om de 1280 MB/s te retourneren.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Na het opstarten start u een toepassing met een niet-kritieke workload. Deze toepassing vereist 15 MB/s die gelijkmatig over alle schijven worden verdeeld.

![Toepassing verzendt aanvraag voor 15 MB/s doorvoer naar VM, VM neemt aanvraag en verzendt elke schijf een aanvraag voor 5 MB/s, elke schijf retourneert 5 MB/s antwoorden, VM retourneert 15 MB/s naar de toepassing.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Vervolgens moet de toepassing een batch-job verwerken die 360 MB/s vereist. De Standard_L8s_v2 bursts om aan deze vraag te voldoen en vervolgens aanvragen. De besturingssysteemschijf heeft slechts 20 MB/s nodig. De resterende 340 MB/s worden verwerkt door de burst-P4-gegevensschijven.

![Toepassing verzendt aanvraag voor 360 MB/s doorvoer naar VM, VM neemt bursts om de aanvraag te verwerken en verzendt elk van de gegevensschijven een aanvraag voor 170 MB/s en 20 MB/s vanaf de besturingssysteemschijf. Elke schijf retourneert de aangevraagde MB/s, VM-bursts om 360 MB/s naar de toepassing te retourneren.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
