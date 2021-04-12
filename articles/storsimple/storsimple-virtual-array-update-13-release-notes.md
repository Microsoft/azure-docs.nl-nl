---
title: Release opmerkingen bij Update 1,3 voor de virtuele matrix Microsoft Azure StorSimple | Microsoft Docs
description: Beschrijft essentiële open problemen en oplossingen voor de virtuele Azure StorSimple-matrix met Update 1,3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: b2c2af4267140370c2aa9421a67cb3a6f413165f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657549"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Release opmerkingen bij Update 1,3 StorSimple Virtual array

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt. Wanneer er kritieke problemen worden ontdekt waarvoor een tijdelijke oplossing nodig is, wordt hierover informatie toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-matrix implementeert.

Update 1,3 komt overeen met de software versie 10.0.10319.0.

> [!IMPORTANT]
> - Update 1,3 is een essentiële update. We raden u ten zeerste aan om dit zo snel mogelijk te installeren.
> - U kunt update 1,3 alleen installeren op apparaten met Update 1,2.
> - Updates zijn storend en opnieuw opstarten van het apparaat. Als I/O wordt uitgevoerd, loopt het apparaat downtime. Ga naar [update 1,3 downloaden](#download-update-13)voor gedetailleerde instructies voor pakketten die worden gebruikt om deze update toe te passen.

## <a name="whats-new-in-update-13"></a>Wat is er nieuw in update 1,3

Deze update bevat de volgende verbeteringen: KB4540725

- Transport Layer Security (TLS) 1,2 is een verplichte update en moet worden geïnstalleerd. Vanaf deze release wordt TLS 1,2 het standaard protocol voor alle Azure Portal communicatie.
  
   Als de volgende waarschuwing wordt weer gegeven, moet u de software op het apparaat bijwerken voordat u doorgaat:

   Op een of meer StorSimple-apparaten wordt een oudere software versie uitgevoerd. De meest recente update voor TLS 1,2 is een verplichte update die direct op deze apparaten moet worden geïnstalleerd. TLS 1,2 wordt gebruikt voor alle Azure Portal communicatie en zonder deze update, kan het apparaat niet communiceren met de StorSimple-service.

- Problemen met garbagecollection-fouten verbeteren de prestaties van de garbagecollection-cyclus wanneer het apparaat en het opslag account zich in twee externe regio's bevinden.
- Oplossing voor back-upfouten vanwege BLOB-time-outs.
- Bijgewerkte beveiligings patches voor OS/. NET Framework:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): maart 2020 Ssu (onderhoud stack update)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): 2020-Rollup van juli
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): juli 2020 .NET Framework update

## <a name="download-update-13"></a>Update 1,3 downloaden

Als u deze update wilt downloaden, gaat u naar de [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) -server en downloadt u het pakket KB4575898. Dit pakket bevat de volgende pakketten:

- **KB4540725**, dat cumulatieve Windows-updates bevat voor 2012 R2 tot en met maart 2020. Ga naar [maandelijkse Security Rollup van maart](https://support.microsoft.com/help/4540725)voor meer informatie over wat er in dit pakket is opgenomen.
- **KB4565541**, dat cumulatieve Windows-updates bevat voor 2012 R2 tot maar liefst juli 2020. Ga naar [maandelijkse Security Rollup van februari](https://support.microsoft.com/help/4565541)voor meer informatie over wat er in dit pakket is opgenomen.
- **KB4565622**, dat Cumulative.NET Framework-updates bevat tot 2020 juli. Ga naar [maandelijkse Security Rollup van februari](https://support.microsoft.com/help/4565622)voor meer informatie over wat er in dit pakket is opgenomen.
- **KB3011067**, dat software-updates van het apparaat bevat.

Down load KB4575898 en volg deze instructies om [de update toe te passen via de lokale web-UI](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Bekende problemen in update 1,3
Er zijn geen nieuwe problemen vermeld in update 1,3. Alle problemen die in de release worden vermeld, worden overgenomen van eerdere versies. Ga naar [bekende problemen in Update 1,2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)voor een overzicht van de bekende problemen die zijn opgenomen in de vorige versies.

## <a name="next-steps"></a>Volgende stappen
Down load KB4575898 en [Pas de update toe via de lokale web-UI](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Referenties
Zoekt u een oudere release-Opmerking? Ga naar:

- [Release opmerkingen bij Update 1,2 StorSimple Virtual array](./storsimple-virtual-array-update-12-release-notes.md)
- [Release opmerkingen bij Update 1,0 StorSimple Virtual array](./storsimple-virtual-array-update-1-release-notes.md)
- [Release opmerkingen bij Update 0,6 StorSimple Virtual array](./storsimple-virtual-array-update-06-release-notes.md)
- [Release opmerkingen bij Update 0,5 StorSimple Virtual array](./storsimple-virtual-array-update-05-release-notes.md)
- [Release opmerkingen bij Update 0,4 StorSimple Virtual array](./storsimple-virtual-array-update-04-release-notes.md)
- [Release opmerkingen bij Update 0,3 StorSimple Virtual array](./storsimple-ova-update-03-release-notes.md)
- [Release opmerkingen bij Update 0,1 en 0,2 voor StorSimple Virtual array](./storsimple-ova-update-01-release-notes.md)
- [Release opmerkingen bij de algemene Beschik baarheid van StorSimple Virtual array](./storsimple-virtual-array-update-06-release-notes.md)