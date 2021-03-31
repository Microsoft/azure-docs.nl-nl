---
title: Release opmerkingen bij Update 0,3 StorSimple Virtual array
description: Hierin worden essentiële openstaande problemen en oplossingen voor de virtuele StorSimple-matrix met Update 0,3 beschreven.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966102"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Release opmerkingen bij Update 0,3 StorSimple Virtual array
## <a name="overview"></a>Overzicht
In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-matrix implementeert.

Update 0,3 komt overeen met de software versie **10.0.10288.0**.

> [!NOTE]
> Updates zijn storend en opnieuw opstarten van het apparaat. Als I/O wordt uitgevoerd, loopt het apparaat downtime.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Wat is er nieuw in de update 0,3
Update 0,3 is voornamelijk een build voor fout herstel. In deze versie zijn verschillende bugs die resulteren in back-upfouten in de vorige versie, besproken.

## <a name="issues-fixed-in-the-update-03"></a>Problemen die zijn opgelost in de update 0,3
De volgende tabel bevat een overzicht van de problemen die in deze release zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Back-ups |Er is een probleem waargenomen in de eerdere versie waar de back-ups niet konden worden voltooid voor een bestands share. Als dit probleem is opgetreden, mislukt de back-uptaak en wordt er een kritieke waarschuwing gegenereerd op de StorSimple Manager-service om de gebruiker op de hoogte te stellen. Dit probleem heeft geen invloed op de gegevens op de shares of de toegang tot de gegevens. De hoofd oorzaak is in deze release geïdentificeerd en opgelost. <br></br> De correctie wordt niet met terugwerkende kracht toegepast op shares die dit probleem al zien. Klanten die dit probleem zien, moeten eerst Update 0,3 Toep assen en contact opnemen met Microsoft Ondersteuning om het probleem op te lossen. In plaats van contact op te nemen met Microsoft Ondersteuning kunnen klanten ook herstellen naar een nieuwe share van een in orde zijnde back-up voor de betrokken shares. |
| 2 |iSCSI |Er is een probleem waargenomen in de eerdere versie waar de volumes verdwijnen wanneer u gegevens naar een volume op de virtuele StorSimple-matrix kopieert. Dit probleem is opgelost in deze release. <br></br> De oplossingen worden alleen van kracht op nieuw gemaakte volumes. De oplossingen worden niet met terugwerkende kracht toegepast op volumes die dit probleem al zien. Klanten wordt aangeraden de betrokken volumes online te brengen via de klassieke Azure-Portal, een back-up voor deze volumes uit te voeren en deze volumes vervolgens te herstellen op nieuwe volumes. |

## <a name="known-issues-in-the-update-03"></a>Bekende problemen in de update 0,3
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
| **achtste.** |Gebruikte capaciteit voor shares |U ziet mogelijk het verbruik delen als de share geen gegevens bevat. Dit komt omdat de gebruikte capaciteit voor shares meta gegevens bevat. | |
| **9,4.** |Herstel na noodgeval |U kunt alleen herstel na nood geval uitvoeren voor een bestands server naar hetzelfde domein als die van het bron apparaat. Herstel na nood gevallen naar een doel apparaat in een ander domein wordt niet ondersteund in deze release. |Dit is geïmplementeerd in een latere versie. |
| **6.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen niet worden beheerd via de Azure PowerShell in deze release. |Het beheer van de virtuele apparaten moet worden uitgevoerd via de klassieke Azure-Portal en de lokale webgebruikersinterface. |
| **9.** |Wachtwoord wijzigen |De console van de virtuele matrix heeft alleen invoer in en-US-toetsenbord indeling toegestaan. | |
| **12.** |CHAP |De CHAP-referenties kunnen niet worden verwijderd als deze eenmaal zijn gemaakt. Als u de CHAP-referenties wijzigt, moet u bovendien de volumes offline halen en deze vervolgens online brengen om de wijziging door te voeren. |Dit probleem wordt opgelost in een latere versie. |
| **13.** |iSCSI-server |De gebruikte opslag ruimte die voor een iSCSI-volume wordt weer gegeven, kan verschillen in de StorSimple Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteem weergave.<br></br>Het apparaat ziet de blokken die worden toegewezen wanneer het volume de maximum grootte heeft. |
| **15.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstream (ADS) is gekoppeld, wordt er geen back-up gemaakt van de ADS of kan deze worden hersteld via herstel na nood gevallen, klonen en herstellen op item niveau. | |

## <a name="next-step"></a>Volgende stap
[Installeer Update 0,3](./storsimple-virtual-array-install-update-06.md) op uw virtuele StorSimple-matrix.

## <a name="references"></a>Referenties
Zoekt u een oudere release-Opmerking? Ga naar: 

* [Release opmerkingen bij Update 0,1 en 0,2 voor StorSimple Virtual array](storsimple-ova-update-01-release-notes.md)
* [Release opmerkingen bij de algemene Beschik baarheid van StorSimple Virtual array](./storsimple-virtual-array-update-06-release-notes.md)