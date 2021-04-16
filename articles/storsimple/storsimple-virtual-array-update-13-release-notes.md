---
title: Microsoft Azure StorSimple opmerkingen bij de release van Virtual Array Update 1.3 | Microsoft Docs
description: Beschrijft kritieke openstaande problemen en oplossingen voor de virtuele Azure StorSimple-matrix met Update 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389516"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Opmerkingen bij de release van StorSimple Virtual Array Update 1.3

In de volgende releasenotities worden de kritieke openstaande problemen en de opgeloste problemen voor Microsoft Azure StorSimple updates van virtuele matrices.

De opmerkingen bij de release worden voortdurend bijgewerkt. Wanneer er kritieke problemen worden ontdekt waarvoor een tijdelijke oplossing nodig is, wordt hierover informatie toegevoegd. Voordat u uw virtuele StorSimple-matrix implementeert, moet u de informatie in de releasenotities zorgvuldig controleren.

Update 1.3 komt overeen met softwareversie 10.0.10319.0.

> [!IMPORTANT]
> - Update 1.3 is een essentiële update. U wordt ten zeerste aangeraden deze zo snel mogelijk te installeren.
> - U kunt Update 1.3 alleen installeren op apparaten met Update 1.2.
> - Updates verstoren en starten uw apparaat opnieuw op. Als I/O wordt uitgevoerd, teert er downtime op het apparaat. Ga naar [Update 1.3](#download-update-13)downloaden voor gedetailleerde instructies over pakketten die worden gebruikt om deze update toe te passen.

## <a name="whats-new-in-update-13"></a>Wat is er nieuw in Update 1.3

Deze update bevat de volgende verbeteringen:KB4540725

- Transport Layer Security (TLS) 1.2 is een verplichte update en moet worden geïnstalleerd. Vanaf deze release wordt TLS 1.2 het standaardprotocol voor alle Azure Portal communicatie.
  
   Als u de volgende waarschuwing ziet, moet u de software op het apparaat bijwerken voordat u doorgaat:

   Op een of meer StorSimple-apparaten wordt een oudere softwareversie uitgevoerd. De meest recente beschikbare update voor TLS 1.2 is een verplichte update die direct op deze apparaten moet worden geïnstalleerd. TLS 1.2 wordt gebruikt voor alle Azure Portal communicatie en zonder deze update kan het apparaat niet communiceren met de StorSimple-service.

- Oplossingen voor problemen met garbageverzameling verbeteren de prestaties van de garbage collection-cyclus wanneer het apparaat en het opslagaccount zich in twee verafgelegen regio's hebben.
- Oplossing voor back-upfouten vanwege blob-time-outs.
- Bijgewerkte os/.NET Framework-beveiligingspatches:
  - [KB4540725:](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)maart 2020 SSU (Servicing Stack Update)
  - [KB4565541:](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)rollup van juli 2020
  - [KB4565622:](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)update van juli 2020 .NET Framework update

## <a name="download-update-13"></a>Update 1.3 downloaden

Als u deze update wilt downloaden, gaat u [naar Microsoft Update Catalog-server](https://www.catalog.update.microsoft.com/Home.aspx) en downloadt u het KB4575898-pakket. Dit pakket bevat de volgende pakketten. Installeer de pakketten in deze volgorde:

1. **KB4540725,** dat cumulatieve Windows-updates bevat voor 2012 R2 tot maart 2020. Ga naar Maandelijks beveiligings rollup van maart voor meer informatie over wat is opgenomen in [dit rollup.](https://support.microsoft.com/help/4540725)
1. **KB4565541,** dat cumulatieve Windows-updates bevat voor 2012 R2 tot juli 2020. Ga naar maandelijks beveiligings rollup van juli voor meer informatie over wat is opgenomen in [dit rollup.](https://support.microsoft.com/help/4565541)
1. **KB4565622,** dat tot juli 2020 cumulative.NET Framework-updates bevat. Ga naar [KB4565622](https://support.microsoft.com/help/4565622)voor meer informatie over wat in dit rollup is opgenomen.<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067,** dat software-updates voor apparaten bevat.

Download KB4575898 en volg deze instructies om de update toe te [passen via de lokale webinterface](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Bekende problemen in Update 1.3
Er zijn geen nieuwe problemen vermeld in Update 1.3. Alle door de release genoteerde problemen worden overgedragen van eerdere releases. Ga naar Bekende problemen [in Update 1.2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)voor een overzicht van bekende problemen uit de vorige versies.

## <a name="next-steps"></a>Volgende stappen
Download KB4575898 en [Pas de update toe via de lokale webinterface](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Referenties
Op zoek naar een oudere release-opmerking? Ga naar:

- [Opmerkingen bij de release van StorSimple Virtual Array Update 1.2](./storsimple-virtual-array-update-12-release-notes.md)
- [Opmerkingen bij de release van StorSimple Virtual Array Update 1.0](./storsimple-virtual-array-update-1-release-notes.md)
- [Opmerkingen bij de release van StorSimple Virtual Array Update 0.6](./storsimple-virtual-array-update-06-release-notes.md)
- [Opmerkingen bij de release van StorSimple Virtual Array Update 0.5](./storsimple-virtual-array-update-05-release-notes.md)
- [Opmerkingen bij de release van StorSimple Virtual Array Update 0.4](./storsimple-virtual-array-update-04-release-notes.md)
- [Opmerkingen bij de release van StorSimple Virtual Array Update 0.3](./storsimple-ova-update-03-release-notes.md)
- [Release-opmerkingen voor StorSimple Virtual Array Update 0.1 en 0.2](./storsimple-ova-update-01-release-notes.md)
- [Opmerkingen bij de algemene beschikbaarheid van virtuele StorSimple-matrices](./storsimple-virtual-array-update-06-release-notes.md)