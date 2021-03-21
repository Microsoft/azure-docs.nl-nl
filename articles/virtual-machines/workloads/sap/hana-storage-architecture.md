---
title: Opslag architectuur van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Opslag architectuur van het implementeren van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff0a674e4f07dd38eaab2ffdbafe1f39e44e6ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676848"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Opslag architectuur van SAP HANA (grote exemplaren)

De opslag indeling voor SAP HANA op Azure (grote instanties) wordt geconfigureerd door SAP HANA op het klassieke implementatie model per door SAP aanbevolen richt lijnen. De richt lijnen worden beschreven in het Witboek [SAP Hana Storage-vereisten](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) .

De HANA grote instantie van de klasse type I wordt geleverd met vier keer zoveel geheugen als opslag volume. Voor de klasse type II van HANA grote instantie-eenheden is de opslag niet vier keer meer. De eenheden worden geleverd met een volume dat is bedoeld voor het opslaan van de back-ups van HANA-transactie Logboeken. Zie [SAP Hana (grote exemplaren) installeren en configureren op Azure](hana-installation.md)voor meer informatie.

Zie de volgende tabel voor meer informatie over de opslag toewijzing. De tabel bevat de ruwe capaciteit voor de verschillende volumes die worden meegeleverd met de verschillende HANA grote instantie-eenheden.

| De SKU voor grote instanties van HANA | Hana/gegevens | Hana/logboek | Hana/gedeeld | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3.328 GB | 768 GB |1.280 GB | 768 GB |
| S96 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4.608 GB | 1.024 GB | 1.536 GB | 1.024 GB |
| S192m | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S192xm |  11.520 GB |  1.536 GB |  1.792 GB |  1.536 GB |
| S384 | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S384m | 12.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xm | 16.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xxm |  20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576m | 20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576xm | 31.744 GB | 4.096 GB | 2.048 GB | 4.096 GB |
| S768m | 28.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S768xm | 40.960 GB | 6.144 GB | 4.096 GB | 6.144 GB |
| S960m | 36.000 GB | 4.100 GB | 2.050 GB | 4.100 GB |
| S896m | 33.792 GB | 512 GB | 1.024 GB | 512 GB |

Meer recente Sku's van HANA grote instanties worden geleverd met opslag configuraties die er ongeveer als volgt uitzien:

| De SKU voor grote instanties van HANA | Hana/gegevens | Hana/logboek | Hana/gedeeld | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4.224 GB | 512 GB | 1.024 GB | 512 GB |
| S224oo | 6.336 GB | 512 GB | 1.024 GB | 512 GB |
| S224m | 8.448 GB | 512 GB | 1.024 GB | 512 GB |
| S224om | 8.448 GB | 512 GB | 1.024 GB | 512 GB |
| S224ooo | 10.560 GB | 512 GB | 1.024 GB | 512 GB |
| S224oom | 12.672 GB | 512 GB | 1.024 GB | 512 GB |
| S448 | 8.448 GB | 512 GB | 1.024 GB | 512 GB |
| S448oo | 12.672 GB | 512 GB | 1.024 GB | 512 GB |
| S448m | 16.896 GB | 512 GB | 1.024 GB | 512 GB |
| S448om | 16.896 GB | 512 GB | 1.024 GB | 512 GB |
| S448ooo | 21.120 GB | 512 GB | 1.024 GB | 512 GB |
| S448oom | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S672 | 12.672 GB | 512 GB | 1.024 GB | 512 GB |
| S672oo | 19.008 GB | 512 GB | 1.024 GB | 512 GB |
| S672m | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S672om | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S672ooo | 31.680 GB | 512 GB | 1.024 GB | 512 GB |
| S672oom | 38.016 GB | 512 GB | 1.024 GB | 512 GB |
| S896 | 16.896 GB | 512 GB | 1.024 GB | 512 GB |
| S896oo | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S896om | 33.792 GB | 512 GB | 1.024 GB | 512 GB |
| S896ooo | 42.240 GB | 512 GB | 1.024 GB | 512 GB |
| S896oom | 50.688 GB | 512 GB | 1.024 GB | 512 GB |


De daad werkelijke geïmplementeerde volumes kunnen variëren op basis van de implementatie en het hulp programma dat wordt gebruikt om de volume grootten weer te geven.

Als u een een HANA-SKU voor grote instanties onderverdelen, kunnen enkele voor beelden van mogelijke delen van onderdelen er als volgt uitzien:

| Geheugen partitie in GB | Hana/gegevens | Hana/logboek | Hana/gedeeld | Hana/logboek/back-up |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| 1\.024 | 1.792 GB | 640 GB | 1.024 GB | 640 GB |
| 1536 | 3.328 GB | 768 GB | 1.280 GB | 768 GB |


Deze grootten zijn ruwe volume nummers die enigszins kunnen variëren op basis van de implementatie en de hulpprogram ma's die worden gebruikt om de volumes te bekijken. Er zijn ook andere partitie formaten, zoals 2,5 TB. Deze opslag grootten worden berekend met een formule die lijkt op die van de vorige partities. De term ' Partitions ' betekent niet dat het besturings systeem, het geheugen of de CPU-resources op een wille keurige manier zijn gepartitioneerd. Het duidt op opslag partities voor de verschillende HANA-instanties die u wilt implementeren op één enkele HANA grote instantie-eenheid. 

Mogelijk hebt u meer opslag ruimte nodig. U kunt opslag toevoegen door extra opslag ruimte te kopen in 1 TB eenheden. Deze extra opslag ruimte kan worden toegevoegd als aanvullend volume. Het kan ook worden gebruikt om een of meer van de bestaande volumes uit te breiden. Het is niet mogelijk om de grootte van de volumes te verlagen zoals oorspronkelijk geïmplementeerd en voornamelijk gedocumenteerd door de vorige tabellen. Het is ook niet mogelijk om de namen van de volumes of koppel namen te wijzigen. De eerder beschreven opslag volumes worden gekoppeld aan de HANA grote instantie-eenheden als NFS4-volumes.

U kunt opslag momentopnamen gebruiken voor back-up-en herstel bewerkingen en herstel na een nood geval. Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie.

Raadpleeg [HLI ondersteunde scenario's](hana-supported-scenario.md) voor de details van de opslag indeling voor uw scenario.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Meerdere exemplaren van SAP HANA uitvoeren op één HANA grote instantie-eenheid

Het is mogelijk om meer dan één Active SAP HANA-exemplaar te hosten op HANA grote instantie-eenheden. Als u de mogelijkheden van moment opnamen van opslag en herstel na nood gevallen wilt bieden, is voor een dergelijke configuratie een volumeset per exemplaar vereist. Op dit moment kunnen HANA grote instantie-eenheden als volgt worden onderverdeeld:

- **S72, S72m, S96, S144, S192**: in stappen van 256 GB, met 256 GB de kleinste begin eenheid. Verschillende stappen, zoals 256 GB en 512 GB, kunnen worden gecombineerd tot het maximum van het geheugen van de eenheid.
- **S144m en S192m**: in stappen van 256 GB, met 512 GB de kleinste eenheid. Verschillende stappen, zoals 512 GB en 768 GB, kunnen worden gecombineerd tot het maximum van het geheugen van de eenheid.
- **Type II-klasse**: in stappen van 512 GB, met de kleinste start eenheid van 2 TB. Verschillende stappen, zoals 512 GB, 1 TB en 1,5 TB, kunnen worden gecombineerd tot het maximum van het geheugen van de eenheid.

Enkele voor beelden van het uitvoeren van meerdere SAP HANA-exemplaren kunnen er als volgt uitzien.

| SKU | Geheugen grootte | Opslag grootte | Grootten met meerdere data bases |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768-GB HANA-instantie<br /> of 1x512-GB instance + 1x256-GB instantie<br /> of 3x256-GB exemplaren | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-instanties<br />of 1x512-GB instance + 1x1-TB instantie<br />of 6x256-GB exemplaren<br />of 1x 1,5-TB instantie | 
| S192m | 4 TB | 16 TB | 8x512-GB exemplaren<br />of 4x1-TB instanties<br />of 4x512-GB exemplaren + 2x1-TB instanties<br />of 4x768-GB exemplaren + 2x512-GB exemplaren<br />of een exemplaar van 1x4 TB |
| S384xm | 8 TB | 22 TB | 4x2-TB instanties<br />of 2x4-TB instanties<br />of 2x3-TB instanties + 1x2-TB instanties<br />of 2x 2,5-TB instanties + 1x3-TB instanties<br />of 1x8-TB instantie |


Er zijn ook andere variaties. 

## <a name="encryption-of-data-at-rest"></a>Versleuteling van data-at-rest
De opslag die wordt gebruikt voor de HANA grote instantie gebruikt transparante versleuteling voor de gegevens, omdat deze zijn opgeslagen op de schijven sinds het einde van het jaar 2018. In eerdere implementaties kunt u ervoor kiezen om de volumes versleuteld op te halen. Als u deze optie hebt gekozen, kunt u een aanvraag indienen om de volumes online te versleutelen. De verplaatsing van niet-versleuteld naar versleutelde volumes is transparant en vereist geen uitval tijd. 

Met de klasse van type I van Sku's wordt het volume waarop de opstart-LUN is opgeslagen, versleuteld. In revisie 3 HANA grote instantie tempels, met behulp van de klasse type II van Sku's van HANA grote instantie, moet u de opstart-LUN versleutelen met OS-methoden. In revisie 4 HANA grote instantie stem pels, met behulp van type II-eenheden het volume waarop de opstart-LUN is opgeslagen en is ook standaard versleuteld. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Vereiste instellingen voor grotere HANA-instanties in HANA grote instanties
De opslag die wordt gebruikt in HANA grote instanties heeft een beperking voor de bestands grootte. De limiet voor de [grootte is 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per bestand. In tegens telling tot beperkingen van de bestands grootte in de EXT3-bestands systemen is HANA niet impliciet van de opslag beperking die wordt afgedwongen door de HANA-opslag voor grote instanties. Als gevolg van een resultaat van HANA wordt niet automatisch een nieuw gegevens bestand gemaakt wanneer de maximale bestands grootte van 16 TB is bereikt. Aangezien HANA probeert het bestand groter dan 16 TB te laten groeien, worden fouten gerapporteerd en de index server wordt aan het einde gecrasht.

> [!IMPORTANT]
> U moet de volgende para meters instellen in het global.ini configuratie bestand van HANA om te voor komen dat HANA gegevens bestanden probeert te verg Roten dan de bestands grootte limiet van 16 TB voor de omvang van de omvang van een grote opslag.
> 
> - datavolume_striping = True
> - datavolume_striping_size_gb = 15000
> - Zie ook SAP-notitie [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Houd rekening met SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Volgende stappen**
- Raadpleeg [ondersteunde scenario's voor Hana grote instanties](hana-supported-scenario.md)