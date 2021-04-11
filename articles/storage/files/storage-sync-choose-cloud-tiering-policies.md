---
title: Kies Azure File Sync beleid voor Cloud lagen | Microsoft Docs
description: Meer informatie over wat u moet onthouden bij het kiezen van Azure File Sync beleid voor Cloud lagen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 01bf9ac6a3bfcb30fb6e6a6f9d56de3f9f516f03
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059272"
---
# <a name="choose-cloud-tiering-policies"></a>Beleid voor Cloud lagen kiezen

Dit artikel bevat richt lijnen voor gebruikers die het Cloud-laag beleid selecteren en aanpassen. Voordat u dit artikel leest, moet u weten hoe Cloud lagen werken. Zie [inzicht in azure file sync Cloud-lagen](storage-sync-cloud-tiering-overview.md)voor de basis principes van Cloud lagen. Zie [Azure file sync Cloud-Tiering policies](storage-sync-cloud-tiering-policy.md)(Engelstalig) voor een gedetailleerde uitleg van beleid voor Cloud lagen met voor beelden.

## <a name="limitations"></a>Beperkingen
- Cloud lagen worden niet ondersteund op het Windows-systeem volume.

- U kunt Cloud lagen nog steeds inschakelen als u een FSRM-quotum op volume niveau hebt. Zodra een FSRM-quotum is ingesteld, worden in de vrije ruimte query-Api's die worden aangeroepen, automatisch de beschik bare ruimte op het volume gerapporteerd volgens de quotum instelling. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minimale bestands grootte voor een bestand dat moet worden gelaagd

Voor Agent versie 9 en hoger is de minimale bestands grootte voor een bestand op laag gebaseerd op de grootte van het bestandssysteem cluster. De minimale bestands grootte die in aanmerking komt voor Cloud lagen, wordt berekend door 2x de cluster grootte en ten minste 8 KB. In de volgende tabel ziet u de minimale bestands grootte die kan worden getierd op basis van de grootte van het volume cluster:

|Volume cluster grootte (bytes) |Bestanden van deze grootte of groter kunnen worden getierd  |
|----------------------------|---------|
|4 KB of kleiner (4096)      | 8 kB    |
|8 KB (8192)                 | 16 kB   |
|16 KB (16384)               | 32 kB   |
|32 KB (32768)               | 64 kB   |
|64 KB (65536)    | 128 kB  |
|128 KB (131072) | 256 kB |
|256 KB (262144) | 512 kB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Cluster grootten tot 2 MB worden ondersteund met Azure File Sync agent versie 12, maar voor grotere grootten werkt Cloud lagen niet.

Alle bestands systemen die worden gebruikt door Windows, organiseren de vaste schijf op basis van de cluster grootte (ook wel bekend als Allocation Unit Size). Cluster grootte vertegenwoordigt de kleinste hoeveelheid schijf ruimte die kan worden gebruikt om een bestand te bewaren. Wanneer de bestands grootte niet van een even veelvoud van de cluster grootte komt, moet er extra ruimte worden gebruikt om het bestand op te slaan op het volgende veelvoud van de cluster grootte.

Azure File Sync wordt ondersteund op NTFS-volumes met Windows Server 2012 R2 en nieuwer. De volgende tabel beschrijft de standaard cluster grootten wanneer u een nieuw NTFS-volume maakt met Windows Server 2019.

|Volume grootte    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 kB                |
|16TB – 32 TB   | 8 kB                |
|32 TB – 64 TB   | 16 kB               |
|64 TB – 128 TB  | 32 kB               |
|128TB – 256 TB | 64 KB (eerdere Max.) |
|256 TB – 512 TB| 128 kB              |
|512 TB – 1 PB  | 256 kB              |
|1 PB – 2 PB    | 512 kB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (max. grootte)  |
|> 8 TB         | niet ondersteund       |

Het is mogelijk dat u na het maken van het volume hand matig het volume met een andere cluster grootte hebt geformatteerd. Als uw volume is afgeleid van een oudere versie van Windows, kunnen de standaard cluster grootten ook verschillen. [In dit artikel vindt u meer informatie over de standaard cluster groottes.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Zelfs als u een cluster grootte kiest die kleiner is dan 4 KB, geldt een limiet van 8 KB als de kleinste bestands grootte die kan worden getierd en is nog steeds van toepassing. (Zelfs als technisch 2x de cluster grootte zou moeten gelijk zijn aan minder dan 8 KB.)

De reden voor het absolute minimum is te vinden op de manier waarop NTFS extreem kleine bestanden-1 KB aan bestanden met een grootte van 4 KB opslaat. Afhankelijk van de andere para meters van het volume kunnen kleine bestanden niet worden opgeslagen in een cluster op schijf. Het is mogelijk efficiënter om dergelijke bestanden rechtstreeks op te slaan in de hoofd bestands tabel van het volume of de MFT-record. Het distributie punt in de Cloud-laag wordt altijd op schijf opgeslagen en neemt precies één cluster in beslag. Bij het trapsgewijs scha kelen van dergelijke kleine bestanden kan er geen ruimte worden bespaard. Extreme gevallen kunnen zelfs eindigen met het gebruik van meer ruimte terwijl Cloud lagen zijn ingeschakeld. Ter bescherming tegen dat geldt dat de kleinste grootte van een bestand dat in de Cloud wordt laag, 8 KB is op een cluster grootte van 4 KB of kleiner. 

## <a name="selecting-your-initial-policies"></a>Het eerste beleid selecteren

Wanneer u Cloud lagen op een server eindpunt inschakelt, moet u in het algemeen één lokale virtuele schijf maken voor elk afzonderlijk server eindpunt. Het isoleren van het server eindpunt maakt het gemakkelijker om te begrijpen hoe Cloud lagen werken en uw beleid dienovereenkomstig aan te passen. Azure File Sync werkt echter zelfs als u meerdere server-eind punten op hetzelfde station hebt. Zie de sectie [meerdere server eindpunten op het lokale volume](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) voor meer informatie. U wordt ook aangeraden om het beleid voor de eerste keer Cloud lagen in te scha kelen en het volume beschik bare ruimte in te stellen op ongeveer 10% tot 20%. Voor de meeste Bestands server volumes is 20% volume beschik bare ruimte doorgaans de beste optie.

Ter vereenvoudiging en om een duidelijk inzicht te krijgen in de manier waarop items worden trapsgewijs gelaagd, raden we u aan om uw volume beschik bare ruimte beleid voornamelijk aan te passen en uw datum beleid uit te scha kelen, tenzij dit nodig is. We raden u aan dit omdat de meeste klanten het waardevol vinden om de lokale cache te vullen met zo veel mogelijk Hot files, en de rest naar de cloud te verlaagen. Het datum beleid kan echter nuttig zijn als u lokale schijf ruimte proactief wilt vrijmaken en u weet dat de bestanden in dat Server eindpunt toegankelijk zijn nadat het aantal dagen dat is opgegeven in uw datum beleid niet lokaal hoeft te worden bewaard. Door het datum beleid in te stellen, maakt u waardevolle lokale schijf capaciteit vrij voor andere eind punten op hetzelfde volume om meer bestanden in de cache op te slaan.

Nadat u uw beleid hebt ingesteld, controleert u de uitvoer en past u beide beleids regels dienovereenkomstig aan. We raden u aan om de **intrek grootte van de Cloud lagen** en de **intrek grootte voor Cloud lagen op basis** van de metrische gegevens van de toepassing in azure monitor te bekijken. Zie [Cloud lagen bewaken](storage-sync-monitor-cloud-tiering.md)voor meer informatie over het bewaken van uitgaand verkeer.

## <a name="adjusting-your-policies"></a>Uw beleid aanpassen

Als de hoeveelheid bestanden die voortdurend van Azure wordt ingetrokken, groter is dan u wilt, hebt u mogelijk meer hot files dan u ruimte hebt om deze op het lokale server volume op te slaan. Verhoog de grootte van uw lokale volume indien mogelijk, en/of verklein het percentage beschik bare ruimte voor het volume in kleine stappen. Het verminderen van het percentage vrije ruimte op de volume kan ook negatieve gevolgen hebben. Een hoger verloop in uw gegevensset vereist meer vrije ruimte: voor nieuwe bestanden en het terughalen van ' koude ' bestanden. U kunt met een vertraging van Maxi maal één uur aan de slag met lagen en vervolgens de verwerkings tijd vereisen. Daarom moet u altijd voldoende vrije ruimte op uw volume hebben.

Als u meer gegevens lokaal houdt, betekent dit lagere kosten voor uitgaand verkeer naarmate er minder bestanden worden ingetrokken van Azure, maar moet u ook een grotere hoeveelheid on-premises opslag ruimte op eigen kosten hebben. 

Bij het aanpassen van uw volume beschik bare ruimte-beleid wordt de hoeveelheid gegevens die u moet lokaal blijven bepalen door de volgende factoren: uw band breedte, het toegangs patroon van de gegevensset en het budget. Met een verbinding met lage band breedte wilt u mogelijk meer lokale gegevens gebruiken om ervoor te zorgen dat gebruikers minimale vertraging oplopen. Als dat niet het geval is, kunt u deze baseren op het verloop tempo tijdens een bepaalde periode. Als u bijvoorbeeld weet dat 10% van uw gegevensset van 1 TB wordt gewijzigd of elke maand actief wordt geopend, is het raadzaam om 100 GB lokaal te houden, zodat u niet vaak bestanden terugroept. Als uw volume 2 TB is, wilt u 5% (of 100 GB) lokaal blijven, wat betekent dat de resterende 95% uw volume beschik bare ruimte percentage is. U moet echter een buffer toevoegen voor Peri Oden met een hoger verloop, met andere woorden, beginnend met een groter volume beschik bare ruimte, en deze vervolgens aanpassen als dat later nodig is.

## <a name="standard-operating-procedures"></a>Standaardwerk procedures

- Wanneer u de eerste keer migreert naar Azure Files via Azure File Sync, is de Cloud Tiering afhankelijk van de eerste upload
- Cloud-Tiering controleert de naleving van de beschik bare ruimte op het volume en het datum beleid elke 60 minuten
- Wanneer u met behulp van de/LFSM-switch op Robocopy bestanden migreert, kunnen bestanden worden gesynchroniseerd en kunnen Cloud lagen ruimte maken tijdens de eerste upload 
- Als er een laag wordt gemaakt voordat een heatmap wordt gevormd, worden de bestanden op de laatst gewijzigde tijds tempel gelaagd

## <a name="next-steps"></a>Volgende stappen
* [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
