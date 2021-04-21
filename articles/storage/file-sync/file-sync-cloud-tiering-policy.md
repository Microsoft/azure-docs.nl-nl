---
title: Azure File Sync beleidsregels voor cloudopslaglagen | Microsoft Docs
description: Meer informatie over hoe het beleid voor datum- en volumeruimteruimte samen werkt voor verschillende scenario's.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797041"
---
# <a name="cloud-tiering-policies"></a>Beleidsregels voor cloudopslaglagen

Cloudopslaglagen hebben twee beleidsregels die bepalen welke bestanden in de cloud worden opgeslagen: het beleid voor vrije ruimte op het **volume** en het **datumbeleid**.

Het **beleid voor vrije ruimte op het** volume zorgt ervoor dat een opgegeven percentage van het lokale volume waar het server-eindpunt zich bevindt, altijd vrij blijft. 

Met **het datumbeleid** worden bestanden voor het laatst gebruikt x dagen geleden of later. Het beleid voor vrije ruimte op het volume heeft altijd voorrang; als er onvoldoende vrije ruimte op het volume is voor het opslaan van zoveel dagen aan bestanden zoals beschreven in het datumbeleid, overschrijft Azure File Sync het datumbeleid en blijven de koudste bestanden in lagen opslaan totdat aan het percentage vrije ruimte op het volume is voldaan.

## <a name="how-both-policies-work-together"></a>Hoe beide beleidsregels samenwerken

We gebruiken een voorbeeld om te illustreren hoe dit beleid werkt: Stel dat u Azure File Sync hebt geconfigureerd op een lokaal volume van 500 GB en dat opslag in cloudlagen nooit is ingeschakeld. Dit zijn de bestanden in uw bestands share:

|Bestandsnaam |Laatste toegangstijd  |Bestandsgrootte  |Opgeslagen in |
|----------|------------------|-----------|----------|
|Bestand 1    | 2 dagen geleden  | 10 GB | Server en Azure-bestands share
|Bestand 2    | 10 dagen geleden | 30 GB | Server en Azure-bestands share
|Bestand 3    | 1 jaar geleden | 200 GB | Server en Azure-bestands share
|Bestand 4    | 1 jaar, 2 dagen geleden | 130 GB | Server en Azure-bestands share
|Bestand 5    | 2 jaar, 1 dag geleden | 140 GB | Server en Azure-bestands share

**Wijziging 1:** U hebt opslag in cloudlagen ingeschakeld, een volumebeleid voor vrije ruimte van 20% ingesteld en het datumbeleid uitgeschakeld gehouden. Met deze configuratie zorgt cloudopslaglagen ervoor dat 20% (in dit geval 100 GB) aan ruimte vrij blijft en beschikbaar blijft op de lokale computer. Als gevolg hiervan is de totale capaciteit van de lokale cache 400 GB. Met 400 GB worden de meest recent en vaak gebruikte bestanden op het lokale volume opgeslagen.

Met deze configuratie worden alleen bestanden 1 tot en met 4 opgeslagen in de lokale cache en wordt bestand 5 gelaagd. Dit is slechts 370 GB van de 400 GB die kan worden gebruikt. Bestand 5 is 140 GB en overschrijdt de limiet van 400 GB als het lokaal in de cache is opgeslagen. 

**Wijziging 2:** Stel dat een gebruiker toegang heeft tot bestand 5. Hierdoor is bestand 5 het laatst toegankelijk bestand in de share. Als gevolg hiervan wordt Bestand 5 opgeslagen in de lokale cache en wordt bestand 4 gelaagd zodat het onder de limiet van 400 GB past. In de volgende tabel ziet u waar de bestanden worden opgeslagen, met deze updates:

|Bestandsnaam |Tijd van laatste toegang  |Bestandsgrootte  |Opgeslagen in |
|----------|------------------|-----------|----------|
|Bestand 5    | 2 uur geleden | 140 GB | Server en Azure-bestands share
|Bestand 1    | 2 dagen geleden  | 10 GB | Server en Azure-bestands share
|Bestand 2    | 10 dagen geleden | 30 GB | Server en Azure-bestands share
|Bestand 3    | 1 jaar geleden | 200 GB | Server en Azure-bestands share
|Bestand 4    | 1 jaar, 2 dagen geleden | 130 GB | Azure-bestands share, lokaal gelaagd

**Wijziging 3:** Stel dat u het beleid hebt bijgewerkt, zodat het op datum gebaseerde beleid voor opslaglagen 60 dagen is en het volumebeleid voor vrije ruimte 70% is. Nu kan slechts maximaal 150 GB worden opgeslagen in de lokale cache. Hoewel bestand 2 minder dan 60 dagen geleden is gebruikt, overschrijven het beleid voor vrije ruimte op het volume het datumbeleid en wordt bestand 2 gelaagd om de 70% lokale vrije ruimte te behouden.

**Wijziging 4:** Als u het volumebeleid voor vrije ruimte hebt gewijzigd in 20% en vervolgens gebruikt om alle bestanden terug te halen die op het lokale station passen terwijl u zich houdt aan het beleid voor cloudopslaglagen, ziet de tabel er als volgende `Invoke-StorageSyncFileRecall` uit:

|Bestandsnaam |Laatste toegangstijd  |Bestandsgrootte  |Opgeslagen in |
|----------|------------------|-----------|----------|
|Bestand 5    | 1 uur geleden  | 140 GB | Server en Azure-bestands share
|Bestand 1    | 2 dagen geleden  | 10 GB | Server en Azure-bestands share
|Bestand 2    | 10 dagen geleden | 30 GB | Server en Azure-bestands share
|Bestand 3    | 1 jaar geleden | 200 GB | Azure-bestands share, lokaal gelaagd
|Bestand 4    | 1 jaar, 2 dagen geleden | 130 GB | Azure-bestands share, lokaal gelaagd

In dit geval worden bestanden 1, 2 en 5 lokaal in de cache opgeslagen en worden bestanden 3 en 4 gelaagd. Omdat het datumbeleid 60 dagen is, worden bestanden 3 en 4 gelaagd, zelfs als het volumebeleid voor vrije ruimte lokaal maximaal 400 GB toestaat.

> [!NOTE] 
> Bestanden worden niet automatisch teruggeroepen wanneer klanten het volumebeleid voor vrije ruimte wijzigen in een kleinere waarde (bijvoorbeeld van 20% in 10%) of wijzig het datumbeleid in een grotere waarde (bijvoorbeeld van 20 dagen in 50 dagen).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Meerdere server-eindpunten op een lokaal volume

Opslag in cloudlagen kan worden ingeschakeld voor meerdere server-eindpunten op één lokaal volume. Voor deze configuratie moet u de vrije ruimte van het volume instellen op dezelfde hoeveelheid voor alle server-eindpunten op hetzelfde volume. Als u verschillende beleidsregels voor vrije ruimte voor verschillende server eindpunten op hetzelfde volume in te stellen, heeft het grootste percentage vrije ruimte volume prioriteit. Dit wordt het effectieve **beleid voor vrije volumeruimte genoemd.** Als u bijvoorbeeld drie server eindpunten op hetzelfde lokale volume hebt, één ingesteld op 15%, een andere op 20%, en een derde op 30%, beginnen ze alle de koudste bestanden in lagen op te slaan wanneer ze minder dan 30% vrije ruimte beschikbaar hebben.

## <a name="next-steps"></a>Volgende stappen

* [Opslag in cloudlagen bewaken](file-sync-monitor-cloud-tiering.md)
