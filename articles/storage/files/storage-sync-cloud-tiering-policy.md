---
title: Beleid voor Cloud lagen Azure File Sync | Microsoft Docs
description: Meer informatie over hoe de datum en het volume beschik bare ruimte beleid samen werken voor verschillende scenario's.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204356"
---
# <a name="cloud-tiering-policies"></a>Beleid voor Cloud lagen

Cloud lagen heeft twee beleids regels die bepalen welke bestanden in de cloud worden gelaagd: het **volume beschik bare ruimte beleid** en het **datum beleid**.

Het **volume beschik bare ruimte beleid** zorgt ervoor dat een opgegeven percentage van het lokale volume waarop het server eindpunt zich bevindt, altijd gratis wordt bewaard. 

De **datum beleid** -lagen bestanden laatst geopend x dagen geleden of later. Het volume beschik bare ruimte beleid heeft altijd prioriteit. Als er onvoldoende vrije ruimte op het volume is om zoveel dagen aan bestanden op te slaan, zoals wordt beschreven in het datum beleid, overschrijft Azure File Sync het datum beleid en gaat u verder met het trapsgewijs scha kelen van de Coldest-bestanden totdat aan het volume beschik bare ruimte percentage wordt voldaan.

## <a name="how-both-policies-work-together"></a>Hoe beide beleids regels samen werken

We gebruiken een voor beeld om te laten zien hoe deze beleids regels werken: Stel dat u Azure File Sync hebt geconfigureerd op een lokaal volume van 500 GB en dat Cloud lagen nooit zijn ingeschakeld. Dit zijn de bestanden in de bestands share:

|Bestandsnaam |Tijd van laatste toegang  |Bestandsgrootte  |Opgeslagen in |
|----------|------------------|-----------|----------|
|Bestand 1    | 2 dagen geleden  | 10 GB | Server-en Azure-bestands share
|Bestand 2    | 10 dagen geleden | 30 GB | Server-en Azure-bestands share
|Bestand 3    | 1 jaar geleden | 200 GB | Server-en Azure-bestands share
|Bestand 4    | 1 jaar, 2 dagen geleden | 130 GB | Server-en Azure-bestands share
|Bestand 5    | 2 jaar, 1 dag geleden | 140 GB | Server-en Azure-bestands share

**Wijziging 1:** U hebt Cloud lagen ingeschakeld, een beleid voor vrije ruimte op volumes van 20% ingesteld en het datum beleid is uitgeschakeld. Met deze configuratie zorgt Cloud lagen ervoor dat 20% (in dit geval 100 GB) aan ruimte vrij is en beschikbaar is op de lokale computer. Als gevolg hiervan is de totale capaciteit van de lokale cache 400 GB. Met 400 GB worden de meest recent en veelgebruikte bestanden op het lokale volume opgeslagen.

Met deze configuratie worden alleen de bestanden 1 tot en met 4 opgeslagen in de lokale cache en wordt bestand 5 gelaagd. Dit is slechts 370 GB van de 400 GB die kunnen worden gebruikt. Bestand 5 is 140 GB en overschrijdt de limiet van 400 GB als deze lokaal in de cache is opgeslagen. 

**Wijziging 2:** Stel dat een gebruiker toegang heeft tot bestand 5. Dit maakt bestand 5 het laatst geopende bestand in de share. Als gevolg hiervan wordt bestand 5 opgeslagen in de lokale cache en te passen onder de limiet van 400 GB, wordt bestand 4 gelaagd. In de volgende tabel ziet u waar de bestanden worden opgeslagen, met deze updates:

|Bestandsnaam |Tijd van laatste toegang  |Bestandsgrootte  |Opgeslagen in |
|----------|------------------|-----------|----------|
|Bestand 5    | 2 uur geleden | 140 GB | Server-en Azure-bestands share
|Bestand 1    | 2 dagen geleden  | 10 GB | Server-en Azure-bestands share
|Bestand 2    | 10 dagen geleden | 30 GB | Server-en Azure-bestands share
|Bestand 3    | 1 jaar geleden | 200 GB | Server-en Azure-bestands share
|Bestand 4    | 1 jaar, 2 dagen geleden | 130 GB | Azure-bestands share, lokaal gelaagd

**Wijziging 3:** Stel dat u de beleids regels hebt bijgewerkt, zodat het op de datum gebaseerde laag beleid 60 dagen bedraagt en het volume beschik bare ruimte beleid 70% is. Nu kan Maxi maal 150 GB worden opgeslagen in de lokale cache. Hoewel bestand 2 minder dan 60 dagen geleden is geopend, overschrijft het volume beschik bare ruimte beleid het datum beleid en wordt het bestand 2 gelaagd om de lokale vrije ruimte van 70% te onderhouden.

**Wijziging 4:** Als u het volume beschik bare ruimte beleid hebt gewijzigd in 20% en vervolgens wordt gebruikt `Invoke-StorageSyncFileRecall` om alle bestanden die op de lokale schijf passen, in te trekken tijdens het naleven van het beleid voor Cloud lagen, ziet de tabel er als volgt uit:

|Bestandsnaam |Tijd van laatste toegang  |Bestandsgrootte  |Opgeslagen in |
|----------|------------------|-----------|----------|
|Bestand 5    | 1 uur geleden  | 140 GB | Server-en Azure-bestands share
|Bestand 1    | 2 dagen geleden  | 10 GB | Server-en Azure-bestands share
|Bestand 2    | 10 dagen geleden | 30 GB | Server-en Azure-bestands share
|Bestand 3    | 1 jaar geleden | 200 GB | Azure-bestands share, lokaal gelaagd
|Bestand 4    | 1 jaar, 2 dagen geleden | 130 GB | Azure-bestands share, lokaal gelaagd

In dit geval worden de bestanden 1, 2 en 5 lokaal in de cache opgeslagen en worden de bestanden 3 en 4 gelaagd. Omdat het datum beleid 60 dagen is, worden de bestanden 3 en 4 gelaagd, zelfs al is het volume beschik bare ruimte beleid tot 400 GB lokaal toegestaan.

> [!NOTE] 
> Bestanden worden niet automatisch ingetrokken wanneer klanten het volume beschik bare ruimte beleid wijzigen in een kleinere waarde (bijvoorbeeld van 20% tot 10%) of wijzig het datum beleid in een grotere waarde (bijvoorbeeld van 20 dagen naar 50 dagen).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Meerdere server eindpunten op een lokaal volume

Cloud lagen kunnen worden ingeschakeld voor meerdere server eindpunten op één lokaal volume. Voor deze configuratie moet u de vrije ruimte voor het volume instellen op dezelfde waarde voor alle server eindpunten op hetzelfde volume. Als u verschillende beleids regels voor volume vrije ruimte instelt voor meerdere server eindpunten op hetzelfde volume, heeft het grootste volume vrije ruimte percentage prioriteit. Dit wordt het **efficiënte volume beschik bare ruimte beleid** genoemd. Als u bijvoorbeeld drie server-eind punten op hetzelfde lokale volume hebt, één ingesteld op 15%, een andere ingesteld op 20%, en een derde ingesteld op 30%, worden de Coldest-bestanden eerst gelaagd wanneer er minder dan 30% beschik bare ruimte beschikbaar is.

## <a name="next-steps"></a>Volgende stappen
* [Cloud lagen bewaken](storage-sync-monitor-cloud-tiering.md)
