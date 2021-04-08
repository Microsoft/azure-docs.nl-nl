---
title: Release opmerkingen bij Update 0,6 StorSimple Virtual array | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de virtuele StorSimple-matrix met Update 0,6 beschreven.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000909"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Release opmerkingen bij Update 0,6 StorSimple Virtual array

## <a name="overview"></a>Overzicht

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-matrix implementeert.

Update 0,6 komt overeen met de software versie **10.0.10293.0**.

> [!IMPORTANT]
> - Updates zijn storend en opnieuw opstarten van het apparaat. Als I/O wordt uitgevoerd, loopt het apparaat downtime. Ga voor meer informatie over het Toep assen van de update naar [update 0,6 installeren](storsimple-virtual-array-install-update-06.md).
>
> - We raden u ten zeerste aan om update 0,6 onmiddellijk te installeren, omdat deze essentiële beveiligingsfixes bevat.


## <a name="whats-new-in-the-update-06"></a>Wat is er nieuw in de update 0,6
Update 0,6 is een essentiële update die direct moet worden geïmplementeerd. Deze update bevat de volgende oplossingen: 

- **Windows-beveiligings oplossingen** : deze release bevat **essentiële Windows-beveiligings oplossingen**. Raadpleeg de volgende beveiligings updates voor meer informatie over de beveiligings problemen en de bijbehorende oplossingen:
    - [Update van december 2016 voor alleen beveiliging van hoge kwaliteit voor Windows 8,1 en Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Update van maart 2017 voor alleen beveiliging van de kwaliteit van Windows 8,1 en Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 mei 2017 — KB4019213 (alleen beveiligings update)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Herstel** herstellen: in eerdere releases is er een fout opgetreden waardoor de herstel bewerking niet kan worden voltooid. Deze fout is opgelost in deze release.


## <a name="issues-fixed-in-the-update-06"></a>Problemen die zijn opgelost in de update 0,6

De volgende tabel bevat een overzicht van de problemen die in deze release zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Beveiliging| Deze release bevat essentiële Windows-beveiligings updates. U wordt aangeraden deze update onmiddellijk te installeren.|
| 2 |Herstellen| Tijdens het herstellen was er een race voorwaarde waardoor de herstel taak niet kan worden voltooid. Met de fout oplossing wordt deze race voorwaarde opgelost.|


## <a name="known-issues-in-the-update-06"></a>Bekende problemen in de update 0,6

De volgende tabel bevat een samen vatting van bekende problemen voor de virtuele StorSimple-matrix en bevat de release-items die zijn vermeld in de vorige releases.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele apparaten die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie van de algemene Beschik baarheid. |Voor deze virtuele apparaten moet een failover worden uitgevoerd voor de release van de algemene Beschik baarheid met behulp van een werk stroom voor nood herstel (DR). |
| **2.** |Ingerichte gegevens schijf |Wanneer u een gegevens schijf van een bepaalde grootte hebt ingericht en het bijbehorende virtuele StorSimple-apparaat hebt gemaakt, moet u de gegevens schijf niet uitbreiden of verkleinen. Als u probeert resultaten te maken, gaan de gegevens in de lokale lagen van het apparaat verloren. | |
| **3.** |Groepsbeleid |Wanneer een apparaat lid is van een domein, kan het Toep assen van een groeps beleid een nadelige invloed hebben op de werking van het apparaat. |Zorg ervoor dat uw virtuele matrix zich in een eigen organisatie-eenheid (OE) bevindt voor Active Directory en dat er geen groeps beleidsobjecten worden toegepast. |
| **4.** |Lokale web-UI |Als Verbeterde beveiligings functies zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-UI-pagina's, zoals het oplossen van problemen of het onderhoud mogelijk niet goed. De knoppen op deze pagina's werken mogelijk ook niet. |Verbeterde beveiligings functies uitschakelen in Internet Explorer. |
| **5.** |Lokale web-UI |In een Hyper-V virtuele machine worden de netwerk interfaces in de Web-UI weer gegeven als 10 Gbps-interfaces. |Dit gedrag is een reflectie van Hyper-V. Hyper-V geeft altijd 10 Gbps weer voor virtuele netwerk adapters. |
| **6.** |Gelaagde volumes of shares |Het vergren delen van een byte bereik voor toepassingen die werken met de StorSimple-gelaagde volumes, wordt niet ondersteund. Als het vergren delen van het byte bereik is ingeschakeld, werkt StorSimple lagen niet. |Aanbevolen maat regelen zijn onder andere: <br></br>Schakel de vergren deling van het byte bereik uit in uw toepassings logica.<br></br>U kunt ervoor kiezen om gegevens voor deze toepassing te plaatsen in lokaal vastgemaakte volumes, in tegens telling tot gelaagde volumes.<br></br>Voor *behoud*: wanneer lokaal vastgemaakte volumes en het vergren delen van het bereik van bytes is ingeschakeld, kan het lokaal vastgemaakte volume online zijn, zelfs voordat het herstel is voltooid. Als een herstel bewerking wordt uitgevoerd, moet u wachten tot de herstel bewerking is voltooid. |
| **7.** |Gelaagde shares |Het werken met grote bestanden kan leiden tot trage lagen. |Wanneer u werkt met grote bestanden, raden we aan dat het grootste bestand kleiner is dan 3% van de share grootte. |
| **achtste.** |Gebruikte capaciteit voor shares |U ziet mogelijk het verbruik delen als de share geen gegevens bevat. Dit verbruik is omdat de gebruikte capaciteit voor shares meta gegevens bevat. | |
| **9,4.** |Herstel na noodgeval |U kunt alleen herstel na nood geval uitvoeren voor een bestands server naar hetzelfde domein als die van het bron apparaat. Herstel na nood gevallen naar een doel apparaat in een ander domein wordt niet ondersteund in deze release. |Dit is geïmplementeerd in een latere versie. Ga voor meer informatie naar [failover en herstel na nood gevallen voor uw virtuele StorSimple-matrix](storsimple-virtual-array-failover-dr.md) |
| **6.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen niet worden beheerd via de Azure PowerShell in deze release. |Het beheer van de virtuele apparaten moet worden uitgevoerd via de Azure Portal en de lokale webgebruikersinterface. |
| **9.** |Wachtwoord wijzigen |De console van de virtuele matrix heeft alleen invoer in en-US-toetsenbord indeling toegestaan. | |
| **12.** |CHAP |De CHAP-referenties kunnen niet worden verwijderd als deze eenmaal zijn gemaakt. Als u de CHAP-referenties wijzigt, moet u bovendien de volumes offline halen en deze vervolgens online brengen om de wijziging door te voeren. |Dit probleem wordt opgelost in een latere versie. |
| **13.** |iSCSI-server |De gebruikte opslag ruimte die voor een iSCSI-volume wordt weer gegeven, kan verschillen in de StorSimple-Apparaatbeheer service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteem weergave.<br></br>Het apparaat ziet de blokken die worden toegewezen wanneer het volume de maximum grootte heeft. |
| **15.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstream (ADS) is gekoppeld, wordt er geen back-up gemaakt van de ADS of kan deze worden hersteld via herstel na nood gevallen, klonen en herstellen op item niveau. | |
| **15.** |Bestandsserver |Symbolische koppelingen worden niet ondersteund. | |
| **18.** |Bestandsserver |Bestanden die worden beveiligd door Windows Encrypting File System (EFS) wanneer deze worden gekopieerd naar of opgeslagen op de StorSimple-Bestands server van de virtuele matrix, resulteren in een niet-ondersteunde configuratie.  | |
| **Nr.** |Updates |Als fout code: 2359302 (hex 0x240006) wordt weer geven tijdens het installeren van een hotfix via de lokale gebruikers interface, houdt dit in dat de hotfix al op uw apparaat is geïnstalleerd.   | |

## <a name="next-step"></a>Volgende stap
[Installeer Update 0,6](storsimple-virtual-array-install-update-06.md) op uw virtuele StorSimple-matrix.

## <a name="references"></a>Referenties
Zoekt u een oudere release-Opmerking? Ga naar:

* [Release opmerkingen bij Update 0,5 StorSimple Virtual array](storsimple-virtual-array-update-05-release-notes.md)
* [Release opmerkingen bij Update 0,4 StorSimple Virtual array](storsimple-virtual-array-update-04-release-notes.md)
* [Release opmerkingen bij Update 0,3 StorSimple Virtual array](storsimple-ova-update-03-release-notes.md)
* [Release opmerkingen bij Update 0,1 en 0,2 voor StorSimple Virtual array](storsimple-ova-update-01-release-notes.md)
* [Release opmerkingen bij de algemene Beschik baarheid van StorSimple Virtual array]()