---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 801f0f03b49d20c84a4531bd0daad7630a0ed01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585098"
---
## <a name="common-scenarios"></a>Algemene scenario's
De volgende scenario's kunnen aanzienlijk van bursting profiteren:
- Het verbeteren van de **opstart tijd** – met bursting, wordt uw exemplaar met een aanzienlijk sneller systeem opgestart. Zo is de standaard besturingssysteem schijf voor Premium ingeschakelde Vm's de P4-schijf. Dit is een ingerichte snelheid van Maxi maal 120 IOPS en 25 MB/s. Met bursting kan de P4 tot 3500 IOPS en 170 MB/s gaan, waardoor een opstart tijd kan worden versneld door 6X.
- **Verwerken van batch-taken** : de werk belastingen van sommige toepassingen zijn van elkaar afnemen en vereisen een optimale prestaties voor de meeste tijd en vereisen een korte periode. Een voor beeld hiervan is een boekhoud programma waarmee dagelijks trans acties worden verwerkt waarvoor een kleine hoeveelheid schijf verkeer nodig is. Aan het einde van de maand worden rapporten die een veel grotere hoeveelheid schijf verkeer nodig hebben, afgestemd.
- **Voor bereiding op het verkeer van verkeers pieken** : webservers en hun toepassingen kunnen op elk gewenst moment verkeer in pieken ondervinden. Als uw webserver wordt ondersteund door Vm's of schijven met behulp van burstisatie, zijn de servers beter uitgerust voor het afhandelen van verkeers pieken. 

## <a name="bursting-flow"></a>Bursting-stroom
Het bursting-krediet systeem is op dezelfde manier van toepassing op het niveau van de virtuele machine en op het schijf niveau. Uw bron, ofwel een virtuele machine of schijf, begint met volledig in voor Raad gecrediteerd tegoed. Met deze tegoeden kunt u 30 minuten op het maximum aantal bursts bellen. Bursting-tegoeden worden verzameld wanneer uw resource wordt uitgevoerd onder de opslag limieten voor de prestaties van de schijf. Voor alle IOPS en MB/s die door uw resource worden gebruikt, is de prestatie limiet die u begint om tegoed te accumuleren. Als uw resource transitorische tegoeden heeft om te gebruiken voor burstisatie en uw werk belasting de extra prestaties nodig heeft, kan uw resource die tegoeden gebruiken om de prestaties van de schijf te verhalen om aan de vraag te voldoen.



![Bucket diagram voor burstisatie](media/managed-disks-bursting/bucket-diagram.jpg)

U kunt het beste de 30 minuten van de bursting gebruiken. U kunt deze gedurende 30 minuten opeenvolgend of per dag gebruiken. Wanneer het product wordt geïmplementeerd, is het gratis met een volledig tegoed en wanneer het de tegoeden afneemt, neemt het minder dan een dag in beslag. U kunt hun burstse tegoeden verzamelen en best Eden aan uw keuze en de Bucket van 30 minuten hoeft niet opnieuw te worden gevuld naar burst. Een ding om te weten over burst accumulatie is dat deze verschilt voor elke resource, omdat deze is gebaseerd op de ongebruikte IOPS en MB/s onder hun prestatie aantallen. Dit betekent dat hogere prestatie producten met een basis lijn hun burst-bedragen sneller kunnen samen voegen dan lagere basis producten. Bijvoorbeeld: een P1-schijf stationair draaien zonder activiteit zal 120 IOPS per seconde toenemen, terwijl een P20-schijf 2.300 IOPS per seconde toeneemt tijdens het stationair draaien zonder activiteit.

## <a name="bursting-states"></a>Bursting-statussen
Er zijn drie statussen die uw resource kan hebben met bursting ingeschakeld:
- Meer **: het** io-verkeer van de resource gebruikt minder dan het prestatie doel. Het accumuleren van burst-tegoed voor IOPS en MB/s wordt gescheiden van elkaar uitgevoerd. Uw resource kan tegoeden van IOPS en bestedingen per seconde of andersom.
- **Bursting** : het verkeer van de resource gebruikt meer dan het prestatie doel. In het burst-verkeer wordt onafhankelijk van de tegoeden van IOPS of band breedte verbruikt.
- **Constante** : het verkeer van de resource heeft precies betrekking op het prestatie doel.

## <a name="examples-of-bursting"></a>Voor beelden van bursting

In de volgende voor beelden ziet u hoe bursting werkt met verschillende combi Naties van VM'S en schijven. Om ervoor te zorgen dat de voor beelden gemakkelijk te volgen zijn, zullen we de focus richten op MB/s, maar dezelfde logica wordt onafhankelijk toegepast op IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Niet-Burstable virtuele machine met Burstable-schijven
**Combi natie van VM en schijf:** 
- Standard_D8as_v4 
    - Niet in cache opgeslagen MB/s: 192
- P4-besturingssysteem schijf
    - Ingericht (e) MB/s: 25
    - Max burst MB/s: 170 
- 2 P10-gegevens schijven 
    - Ingericht (e) MB/s: 100
    - Max burst MB/s: 170

 Wanneer de virtuele machine wordt opgestart, worden gegevens opgehaald van de besturingssysteem schijf. Omdat de besturingssysteem schijf deel uitmaakt van een VM die wordt opgestart, is de besturingssysteem schijf vol met burst-tegoed. Met deze tegoeden kan het opstarten van de besturingssysteem schijf tot 170 MB/s seconde worden gestart.

![VM verzendt een aanvraag voor 192 MB/s van de door voer naar de besturingssysteem schijf. de besturingssysteem schijf reageert met 170 MB/s-gegevens.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Nadat het opstarten is voltooid, wordt een toepassing uitgevoerd op de VM en heeft deze een niet-kritieke werk belasting. Voor deze workload is 15 MB/S vereist die gelijkmatig over alle schijven wordt verdeeld.

![De toepassing verzendt een aanvraag voor 15 MB/s van de door voer naar een virtuele machine. de VM haalt een aanvraag en stuurt elke schijf een aanvraag voor 5 MB/s. op deze schijven wordt 5 MB/s als resultaat gegeven. VM retourneert 15 MB/s voor de toepassing.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Vervolgens moet de toepassing een batch taak verwerken die 192 MB/s vereist. 2 MB/s worden gebruikt door de besturingssysteem schijf en de rest wordt evenredig verdeeld over de gegevens schijven.

![De toepassing verzendt een aanvraag voor 192 MB/s van de door voer naar de virtuele machine, voert een aanvraag uit en stuurt het grote deel van de aanvraag naar de gegevens schijven (95 MB/s) en 2 MB/s naar de besturingssysteem schijf, de gegevens schijven die aan de vraag voldoen en alle schijven retour neren de aangevraagde door voer naar de virtuele machine.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Burstable virtuele machine met niet-Burstable schijven
**Combi natie van VM en schijf:** 
- Standard_L8s_v2 
    - Niet in cache opgeslagen MB/s: 160
    - Max burst MB/s: 1.280
- P50-besturingssysteem schijf
    - Ingericht (e) MB/s: 250 
- 2 P10-gegevens schijven 
    - Ingericht (e) MB/s: 250

 Na de eerste keer opstarten wordt een toepassing uitgevoerd op de VM en heeft deze een niet-kritieke werk belasting. Deze werk belasting vereist 30 MB/s die gelijkmatig over alle schijven wordt verdeeld.
![De toepassing verzendt een aanvraag voor 30 MB/s van de door voer naar een virtuele machine, de virtuele machine stuurt een aanvraag en verzendt elke schijf een aanvraag voor 10 MB/s. alle schijven retour neren 10 MB/s. VM retourneert 30 MB/s voor de toepassing.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Vervolgens moet de toepassing een batch taak verwerken die 600 MB/s vereist. De Standard_L8s_v2-bursts om te voldoen aan deze vraag en vervolgens aanvragen naar de schijven, worden gelijkmatig verdeeld over P50-schijven.

![De toepassing verzendt een aanvraag voor 600 MB/s van de door voer naar de VM. de VM haalt bursts op om de aanvraag uit te voeren en stuurt elke schijf een aanvraag voor 200 MB/s. elke schijven retourneert 200 MB/s, virtuele machine-bursts om 600 MB/s te retour neren naar de toepassing.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Bebreekbaar virtuele machine met Burstable-schijven
**Combi natie van VM en schijf:** 
- Standard_L8s_v2 
    - Niet in cache opgeslagen MB/s: 160
    - Max burst MB/s: 1.280
- P4-besturingssysteem schijf
    - Ingericht (e) MB/s: 25
    - Max burst MB/s: 170 
- 2 P4-gegevens schijven 
    - Ingericht (e) MB/s: 25
    - Max burst MB/s: 170 

Wanneer de virtuele machine wordt gestart, wordt de burst-limiet van 1.280 MB/s op de besturingssysteem schijf gesplitst en reageert de besturingssysteem schijf met de burst-prestaties van 170 MB/s.

![Bij het opstarten verzendt de VM-bursts een aanvraag van 1.280 MB/s naar de besturingssysteem schijf, worden de schijf bursts van het besturings systeem om de 1.280 MB/s te retour neren.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Na het opstarten start u een toepassing met een niet-kritieke werk belasting. Voor deze toepassing is 15 MB/s vereist die gelijkmatig over alle schijven wordt verdeeld.

![De toepassing verzendt een aanvraag voor 15 MB/s van de door voer naar een virtuele machine. de VM haalt een aanvraag en stuurt elke schijf een aanvraag voor 5 MB/s. op deze schijven wordt 5 MB/s als resultaat gegeven. VM retourneert 15 MB/s voor de toepassing.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Vervolgens moet de toepassing een batch taak verwerken die 360 MB/s vereist. De Standard_L8s_v2-bursts om aan deze vraag te voldoen en vervolgens aanvragen. De besturingssysteem schijf heeft slechts 20 MB/s nodig. De resterende 340 MB/s worden verwerkt door de burst-P4-gegevens schijven.

![De toepassing verzendt een aanvraag voor 360 MB/s van de door voer naar de VM. de VM haalt bursts op om de aanvraag uit te voeren en stuurt elk van de gegevens schijven een aanvraag voor 170 MB/s en 20 MB/s van de besturingssysteem schijf. elke schijf retourneert de gevraagde MB/s, VM-bursts om 360 MB/s te retour neren naar de toepassing.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)