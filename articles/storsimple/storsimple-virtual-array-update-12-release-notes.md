---
title: Release opmerkingen bij Update 1,2 voor de virtuele matrix Microsoft Azure StorSimple | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de virtuele StorSimple-matrix met Update 1,2 beschreven.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960186"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Release opmerkingen bij Update 1,2 StorSimple Virtual array

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt. Wanneer er kritieke problemen worden ontdekt waarvoor een tijdelijke oplossing nodig is, wordt hierover informatie toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-matrix implementeert.

Update 1,2 komt overeen met de software versie **10.0.10311.0**.

> [!IMPORTANT]
> - Updates zijn storend en opnieuw opstarten van het apparaat. Als I/O wordt uitgevoerd, loopt het apparaat downtime. Ga naar [update 1,2 downloaden](#download-update-12)voor gedetailleerde instructies voor pakketten die worden gebruikt om deze update toe te passen.
> - Update 1,2 is alleen beschikbaar voor u via de Azure Portal als op uw apparaat update 1,0 of 1,1 wordt uitgevoerd.

## <a name="whats-new-in-update-12"></a>Wat is er nieuw in update 1,2

Deze update bevat de volgende fout oplossingen:

- Verbeterde tolerantie bij het verwerken van verwijderde bestanden.
- Verbeterde afhandeling van uitzonde ringen in het code-opstart traject leidt tot minder fouten in back-ups, herstel, Cloud Lees bewerkingen en automatische ruimte vrijmaken.

## <a name="download-update-12"></a>Update 1,2 downloaden

Als u deze update wilt downloaden, gaat u naar de [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) -server en downloadt u het pakket KB4502035. Dit pakket bevat de volgende pakketten:

 - **KB4493446** met cumulatieve Windows-Updates voor 2012 R2 tot en met april 2019. Voor meer informatie over wat er in dit pakket is opgenomen, gaat u naar [maandelijkse Security Rollup van april](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** dit is een Microsoft Update zelfstandig pakket bestand WINDOWSTH-KB3011067-x64. Dit bestand wordt gebruikt om de software van het apparaat bij te werken.

Down load KB4502035 en volg deze instructies om [de update toe te passen via de lokale web-UI](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemen die zijn opgelost in update 1,2

De volgende tabel bevat een overzicht van de problemen die in deze release zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Verwijdering| In de vorige versies van de software is er een probleem opgetreden toen het gebruik van het apparaat niet werd gewijzigd, zelfs niet wanneer bestanden zijn verwijderd. Dit probleem is opgelost in deze versie. Het pad naar de laag code is verbeterd tijdens het verwerken van verwijderde bestanden.|
| 2 |Afhandeling van uitzonderingen| In de vorige versies van de software is er een probleem opgetreden bij het opnieuw opstarten van het systeem. Dit kan leiden tot storingen in back-ups, herstel, het lezen van de Cloud en automatische ruimte vrijmaken. Deze release bevat wijzigingen in de manier waarop de uitzonde ringen in het opstart traject zijn verwerkt.|

## <a name="known-issues-in-update-12"></a>Bekende problemen in update 1,2

Er zijn geen nieuwe problemen vermeld in update 1,2. Alle problemen die in de release worden vermeld, worden overgenomen van eerdere versies. Ga naar [bekende problemen in Update 1,1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)voor een overzicht van de bekende problemen die zijn opgenomen in de vorige versies.

## <a name="next-steps"></a>Volgende stappen

Down load KB4502035 en [Pas de update toe via de lokale web-UI](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Verwijzingen

Zoekt u een oudere release-Opmerking? Ga naar:
* [Release opmerkingen bij Update 1,1 StorSimple Virtual array](storsimple-virtual-array-update-11-release-notes.md)
* [Release opmerkingen bij Update 1,0 StorSimple Virtual array](storsimple-virtual-array-update-1-release-notes.md)
* [Release opmerkingen bij Update 0,6 StorSimple Virtual array](storsimple-virtual-array-update-06-release-notes.md)
* [Release opmerkingen bij Update 0,5 StorSimple Virtual array](storsimple-virtual-array-update-05-release-notes.md)
* [Release opmerkingen bij Update 0,4 StorSimple Virtual array](storsimple-virtual-array-update-04-release-notes.md)
* [Release opmerkingen bij Update 0,3 StorSimple Virtual array](storsimple-ova-update-03-release-notes.md)
* [Release opmerkingen bij Update 0,1 en 0,2 voor StorSimple Virtual array](storsimple-ova-update-01-release-notes.md)
* [Release opmerkingen bij de algemene Beschik baarheid van StorSimple Virtual array](./storsimple-virtual-array-update-06-release-notes.md)