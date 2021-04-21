---
title: Kies Azure File Sync beleidsregels voor cloudopslaglagen | Microsoft Docs
description: Meer informatie over waar u rekening mee moet houden bij het kiezen Azure File Sync beleidsregels voor cloudopslaglagen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797059"
---
# <a name="choose-cloud-tiering-policies"></a>Beleidsregels voor cloudopslaglagen kiezen

Dit artikel bevat richtlijnen voor gebruikers die hun beleid voor cloudopslaglagen selecteren en aanpassen. Voordat u dit artikel leest, moet u weten hoe opslag in cloudlagen werkt. Zie Understand Azure File Sync cloud tiering (Meer Azure File Sync [cloudopslaglagen) voor de basisprincipes van cloudopslaglagen.](file-sync-cloud-tiering-overview.md) Zie Beleidsregels voor cloudlagen Azure File Sync uitgebreide uitleg over beleidsregels voor [cloudopslaglagen.](file-sync-cloud-tiering-policy.md)

## <a name="limitations"></a>Beperkingen
- Cloudopslaglagen worden niet ondersteund op het Windows-systeemvolume.

- U kunt opslag in cloudlagen nog steeds inschakelen als u een FSRM-quotum op volumeniveau hebt. Zodra een FSRM-quotum is ingesteld, rapporteren de query-API's voor vrije ruimte die worden aangeroepen automatisch de vrije ruimte op het volume volgens de quotuminstelling. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minimale bestandsgrootte voor een bestand in een laag

Voor agentversies 9 en nieuwer is de minimale bestandsgrootte voor een bestand in de laag gebaseerd op de clustergrootte van het bestandssysteem. De minimale bestandsgrootte die in aanmerking komt voor opslag in cloudlagen, wordt berekend door 2x de clustergrootte en minimaal 8 kB. In de volgende tabel ziet u de minimale bestandsgrootten die gelaagd kunnen worden op basis van de grootte van het volumecluster:

|Volumeclustergrootte (bytes) |Bestanden van deze grootte of groter kunnen in lagen worden opgeslagen  |
|----------------------------|---------|
|4 kB of kleiner (4096)      | 8 kB    |
|8 kB (8192)                 | 16 kB   |
|16 kB (16384)               | 32 kB   |
|32 kB (32768)               | 64 kB   |
|64 kB (65536)    | 128 kB  |
|128 kB (131072) | 256 kB |
|256 kB (262144) | 512 kB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Clustergrootten tot 2 MB worden ondersteund met Azure File Sync agent versie 12, maar voor grotere hoeveelheden werkt cloudopslaglagen niet.

Alle bestandssystemen die door Windows worden gebruikt, organiseren uw harde schijf op basis van de clustergrootte (ook wel de grootte van de toewijzingseenheid genoemd). Clustergrootte vertegenwoordigt de kleinste hoeveelheid schijfruimte die kan worden gebruikt voor het opslaan van een bestand. Wanneer de bestandsgrootte niet gelijk is aan een veelvoud van de clustergrootte, moet er extra ruimte worden gebruikt voor het opslaan van het bestand, tot het volgende veelvoud van de clustergrootte.

Azure File Sync wordt ondersteund op NTFS-volumes met Windows Server 2012 R2 en hoger. In de volgende tabel worden de standaardclustergrootten beschreven wanneer u een nieuw NTFS-volume maakt met Windows Server 2019.

|Volumegrootte    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 kB                |
|16 TB – 32 TB   | 8 kB                |
|32 TB – 64 TB   | 16 kB               |
|64 TB – 128 TB  | 32 kB               |
|128 TB – 256 TB | 64 kB (eerder maximum) |
|256 TB – 512 TB| 128 kB              |
|512 TB – 1 PB  | 256 kB              |
|1 PB – 2 PB    | 512 kB              |
|2 TB – 4 PB    | 1024 kB             |
|4 TB – 8 TB    | 2048 KB (maximale grootte)  |
|> 8 TB         | niet ondersteund       |

Het is mogelijk dat u bij het maken van het volume het volume handmatig hebt geformatteerd met een andere clustergrootte. Als uw volume afkomstig is van een oudere versie van Windows, kunnen de standaardclustergrootten ook verschillen. [Dit artikel geeft meer informatie over standaardclustergrootten.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Zelfs als u een clustergrootte kiest die kleiner is dan 4 kB, geldt er nog steeds een limiet van 8 kB als de kleinste bestandsgrootte die gelaagd kan worden. (Zelfs als technisch gezien een clustergrootte van 2 x gelijk is aan minder dan 8 kB.)

De reden voor het absolute minimum is te vinden in de manier waarop NTFS extreem kleine bestanden opgeslagen - bestanden van 1 kB tot 4 kB. Afhankelijk van andere parameters van het volume is het mogelijk dat kleine bestanden helemaal niet worden opgeslagen in een cluster op schijf. Het is mogelijk efficiënter om dergelijke bestanden rechtstreeks op te slaan in de hoofdbestandstabel of MFT-record van het volume. Het reparsepunt voor cloudopslaglagen wordt altijd op schijf opgeslagen en neemt precies één cluster in beslag. Het in lagen opslaan van dergelijke kleine bestanden kan uiteindelijk geen ruimtebesparing opleveren. Extreme gevallen kunnen zelfs meer ruimte gebruiken als cloudopslaglagen zijn ingeschakeld. Ter bescherming hiervoor is de kleinste grootte van een bestand dat in cloudlagen wordt opgeslagen, 8 kB op een clustergrootte van 4 kB of kleiner. 

## <a name="selecting-your-initial-policies"></a>Uw eerste beleid selecteren

Over het algemeen moet u, wanneer u opslag in cloudlagen op een server-eindpunt inschakelen, één lokaal virtueel station maken voor elk afzonderlijk server-eindpunt. Het isoleren van het server-eindpunt maakt het gemakkelijker om te begrijpen hoe cloudopslaglagen werken en uw beleid dienovereenkomstig aan te passen. Echter, Azure File Sync werkt zelfs als u meerdere server eindpunten op hetzelfde station, zie voor meer informatie de meerdere server-eindpunten op [het lokale volume](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) sectie. We raden u ook aan het datumbeleid uitgeschakeld te houden wanneer u voor het eerst opslag in cloudlagen inschakelen, en het beleid voor volumeruimte vrij te houden op ongeveer 10% tot 20%. Voor de meeste bestandsservervolumes is 20% vrije volumeruimte meestal de beste optie.

Voor het gemak en om een duidelijk inzicht te krijgen in hoe items in lagen worden opgelaagd, raden we u aan uw beleid voor vrije volumeruimte voornamelijk aan te passen en uw datumbeleid uitgeschakeld te houden, tenzij dat nodig is. We raden dit aan omdat de meeste klanten het waardevol vinden om de lokale cache te vullen met zoveel mogelijk hot-bestanden en de rest naar de cloud te brengen. Het datumbeleid kan echter nuttig zijn als u proactief lokale schijfruimte wilt vrij maken en u weet dat bestanden in het server-eindpunt dat is gebruikt na het aantal dagen dat is opgegeven in uw datumbeleid, niet lokaal hoeven te worden bewaard. Het instellen van het datumbeleid maakt waardevolle lokale schijfcapaciteit vrij voor andere eindpunten op hetzelfde volume om meer van hun bestanden in de cache op te slaan.

Nadat u uw beleid hebt vastgesteld, controleert u degress en past u beide beleidsregels dienovereenkomstig aan. We raden u aan om specifiek te kijken naar de **terugroepgrootte van cloudlagen** en de terugroepgrootte van **cloudlagen** op basis van metrische gegevens van toepassingen in Azure Monitor. Zie Opslag in cloudlagen bewaken voor meer informatie over het bewaken [van degressie.](file-sync-monitor-cloud-tiering.md)

## <a name="adjusting-your-policies"></a>Uw beleid aanpassen

Als de hoeveelheid bestanden die voortdurend worden ingeroepen uit Azure groter is dan u wilt, hebt u mogelijk meer hot-bestanden dan u ruimte hebt om ze op het lokale servervolume op te slaan. Verhoog indien mogelijk de grootte van uw lokale volume en/of verklein het beleidspercentage voor de vrije ruimte van uw volume in kleine stappen. Het te veel verlagen van de vrije ruimte in het volume kan ook negatieve gevolgen hebben. Een hoger verloop in uw gegevensset vereist meer vrije ruimte voor nieuwe bestanden en het terughalen van 'koude' bestanden. Lagen worden met een vertraging van maximaal één uur in gebruik en hebben vervolgens verwerkingstijd nodig. Daarom moet u altijd voldoende vrije ruimte op uw volume hebben.

Als u meer gegevens lokaal houdt, betekent dit lagere kosten voor het uitvoeren van gegevens, omdat er minder bestanden worden teruggeroepen uit Azure, maar dat er ook een grotere hoeveelheid on-premises opslag nodig is die op zijn eigen kosten afkomt. 

Bij het aanpassen van uw beleid voor vrije volumeruimte wordt de hoeveelheid gegevens die u lokaal moet houden bepaald door de volgende factoren: uw bandbreedte, het toegangspatroon van de gegevensset en het budget. Met een verbinding met lage bandbreedte wilt u mogelijk meer lokale gegevens, om een minimale vertraging voor gebruikers te garanderen. Anders kunt u deze baseren op het verlooppercentage tijdens een bepaalde periode. Als u bijvoorbeeld weet dat 10% van uw gegevensset van 1 TB elke maand wordt gewijzigd of actief wordt geopend, kunt u 100 GB lokaal houden, zodat u bestanden niet regelmatig terugroept. Als uw volume 2 TB is, wilt u 5% (of 100 GB) lokaal houden, wat betekent dat de resterende 95% het percentage vrije ruimte van uw volume is. U moet echter een buffer toevoegen voor perioden met een hoger verloop, met andere woorden, beginnend met een groter percentage vrije volumeruimte en deze later aanpassen.

## <a name="standard-operating-procedures"></a>Standaard operationele procedures

- Bij de eerste migratie naar Azure Files via Azure File Sync, is cloudopslaglagen afhankelijk van de initiële upload
- Met cloudopslaglagen wordt elke 16 minuten gecontroleerd of de beschikbare ruimte op het volume en het datumbeleid worden nageleefd
- Als u de /LFSM-switch in Robocopy gebruikt bij het migreren van bestanden, kunnen bestanden worden gesynchroniseerd en in cloudlagen ruimte worden gemaakt tijdens de eerste upload 
- Als lagen worden opgeslagen voordat een heatmap wordt gevormd, worden bestanden gelaagd op basis van de tijdstempel die het laatst is gewijzigd

## <a name="next-steps"></a>Volgende stappen

* [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
