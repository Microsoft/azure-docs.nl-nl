---
title: Release opmerkingen bij Update 0,4 StorSimple Virtual array | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de virtuele StorSimple-matrix met Update 0,4 beschreven.
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967054"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Release opmerkingen bij Update 0,4 StorSimple Virtual array

## <a name="overview"></a>Overzicht

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-matrix implementeert.

Update 0,4 komt overeen met de software versie **10.0.10289.0**.

> [!NOTE]
> Updates zijn storend en opnieuw opstarten van het apparaat. Als I/O wordt uitgevoerd, loopt het apparaat downtime.


## <a name="whats-new-in-the-update-04"></a>Wat is er nieuw in de update 0,4
Update 0,4 is voornamelijk een probleem oplossing die is gekoppeld aan een paar verbeteringen. In deze versie zijn verschillende bugs die resulteren in back-upfouten in de vorige versie, besproken. De belangrijkste verbeteringen en fout oplossingen zijn als volgt:

- **Verbeteringen** in de prestaties van de back-up: deze release heeft verschillende belang rijke verbeteringen aangebracht voor het verbeteren van de back-upprestaties. Als gevolg hiervan zien de back-ups die betrekking hebben op een groot aantal bestanden een aanzienlijke verlaging van de tijd om te volt ooien, voor volledige en incrementele back-ups.

- **Verbeterde prestaties voor herstellen** : deze release bevat verbeteringen die de prestaties van het herstellen aanzienlijk verbeteren wanneer u een groot aantal bestanden gebruikt. Als u 2-4 miljoen bestanden gebruikt, raden we u aan een virtuele matrix met 16 GB RAM-geheugen in te richten om de verbeteringen te bekijken. Wanneer u minder dan 2.000.000 bestanden gebruikt, blijft de minimale vereiste voor de virtuele machine 8 GB RAM-geheugen.

- **Verbeteringen ter ondersteuning** van het pakket: de verbeteringen omvatten logboek registratie in de statistieken voor schijf, CPU, geheugen, netwerk en Cloud in het ondersteunings pakket, waardoor het proces van het diagnosticeren/oplossen van problemen met apparaten wordt verbeterd.

- **Lokaal vastgemaakte iSCSI-volumes beperken tot 200 GB** : voor lokaal vastgemaakte volumes raden wij u aan om een iSCSI-volume van 200 GB op uw virtuele StorSimple-matrix te beperken. De lokale reserve ring voor gelaagde volumes blijft 10% van de ingerichte volume grootte, maar is beperkt tot 200 GB. 

- Problemen met **betrekking tot back-ups van fouten** -in eerdere versies van software waren er kwesties met betrekking tot back-ups die back-upfouten veroorzaken. Deze fouten zijn in deze release opgelost.


## <a name="issues-fixed-in-the-update-04"></a>Problemen die zijn opgelost in de update 0,4

De volgende tabel bevat een overzicht van de problemen die in deze release zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Back-upprestaties|In de eerdere versies kan het veel tijd duren voordat de back-ups met een groot aantal bestanden zijn voltooid (in de volg orde van dagen). In deze release zien de volledige en incrementele back-ups een aanzienlijke verlaging van de tijd tot aan de voltooiing. |
| 2 |Ondersteunings pakket|De gegevens van de schijf, de CPU, het geheugen, het netwerk en de Cloud zijn nu aangemeld bij de ondersteunings logboeken die de ondersteunings pakketten zeer effectief maken bij het oplossen van problemen met apparaten.|
| 3 |Backup |In eerdere releases kunnen langlopende back-ups ertoe leiden dat het apparaat een ruimte beveiligt, wat resulteert in back-upfouten. Deze fout wordt opgelost in deze release door Maxi maal vijf back-ups in de wachtrij te plaatsen.|
| 4 |iSCSI | In eerdere releases was de lokale reserve ring voor gelaagde of lokaal vastgemaakte volumes 10% van de ingerichte volume grootte. In deze release is de lokale reserve ring voor alle iSCSI-volumes (lokaal vastgemaakt of gelaagd) beperkt tot 10% met een maximum van Maxi maal 200 GB (voor gelaagde volumes van meer dan 2 TB), waardoor er meer ruimte vrij is op de lokale schijf. Het is raadzaam dat de lokaal vastgemaakte volumes in deze release beperkt zijn tot 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Bekende problemen in de update 0,4

De volgende tabel bevat een samen vatting van bekende problemen voor de virtuele StorSimple-matrix en bevat de release-items die zijn vermeld in de vorige releases. 

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele apparaten die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie van de algemene Beschik baarheid. |Voor deze virtuele apparaten moet een failover worden uitgevoerd voor de release van de algemene Beschik baarheid met behulp van een werk stroom voor nood herstel (DR). |
| **2.** |Ingerichte gegevens schijf |Wanneer u een gegevens schijf van een bepaalde grootte hebt ingericht en het bijbehorende virtuele StorSimple-apparaat hebt gemaakt, moet u de gegevens schijf niet uitbreiden of verkleinen. Als u probeert resultaten te maken, gaan de gegevens in de lokale lagen van het apparaat verloren. | |
| **3.** |Groepsbeleid |Wanneer een apparaat lid is van een domein, kan het Toep assen van een groeps beleid een nadelige invloed hebben op de werking van het apparaat. |Zorg ervoor dat uw virtuele matrix zich in een eigen organisatie-eenheid (OE) bevindt voor Active Directory en dat er geen groeps beleidsobjecten worden toegepast. |
| **4.** |Lokale web-UI |Als Verbeterde beveiligings functies zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-UI-pagina's, zoals het oplossen van problemen of het onderhoud mogelijk niet goed. De knoppen op deze pagina's werken mogelijk ook niet. |Verbeterde beveiligings functies uitschakelen in Internet Explorer. |
| **5,0.** |Lokale web-UI |In een Hyper-V virtuele machine worden de netwerk interfaces in de Web-UI weer gegeven als 10 Gbps-interfaces. |Dit gedrag is een reflectie van Hyper-V. Hyper-V geeft altijd 10 Gbps weer voor virtuele netwerk adapters. |
| **6,5.** |Gelaagde volumes of shares |Het vergren delen van een byte bereik voor toepassingen die werken met de StorSimple-gelaagde volumes, wordt niet ondersteund. Als het vergren delen van het byte bereik is ingeschakeld, werkt StorSimple lagen niet. |Aanbevolen maat regelen zijn onder andere: <br></br>Schakel de vergren deling van het byte bereik uit in uw toepassings logica.<br></br>U kunt ervoor kiezen om gegevens voor deze toepassing te plaatsen in lokaal vastgemaakte volumes, in tegens telling tot gelaagde volumes.<br></br>Voor *behoud*: wanneer lokaal vastgemaakte volumes en het vergren delen van het bereik van bytes is ingeschakeld, kan het lokaal vastgemaakte volume online zijn, zelfs voordat het herstel is voltooid. Als een herstel bewerking wordt uitgevoerd, moet u wachten tot de herstel bewerking is voltooid. |
| **7.** |Gelaagde shares |Het werken met grote bestanden kan leiden tot trage lagen. |Wanneer u werkt met grote bestanden, raden we aan dat het grootste bestand kleiner is dan 3% van de share grootte. |
| **achtste.** |Gebruikte capaciteit voor shares |U ziet mogelijk het verbruik delen als de share geen gegevens bevat. Dit komt omdat de gebruikte capaciteit voor shares meta gegevens bevat. | |
| **9,4.** |Herstel na noodgeval |U kunt alleen herstel na nood geval uitvoeren voor een bestands server naar hetzelfde domein als die van het bron apparaat. Herstel na nood gevallen naar een doel apparaat in een ander domein wordt niet ondersteund in deze release. |Dit is geïmplementeerd in een latere versie. |
| **6.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen niet worden beheerd via de Azure PowerShell in deze release. |Het beheer van de virtuele apparaten moet worden uitgevoerd via de klassieke Azure-Portal en de lokale webgebruikersinterface. |
| **9.** |Wachtwoord wijzigen |De console van de virtuele matrix heeft alleen invoer in en-US-toetsenbord indeling toegestaan. | |
| **12.** |CHAP |De CHAP-referenties kunnen niet worden verwijderd als deze eenmaal zijn gemaakt. Als u de CHAP-referenties wijzigt, moet u bovendien de volumes offline halen en deze vervolgens online brengen om de wijziging door te voeren. |Dit probleem wordt opgelost in een latere versie. |
| **13.** |iSCSI-server |De gebruikte opslag ruimte die voor een iSCSI-volume wordt weer gegeven, kan verschillen in de StorSimple Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteem weergave.<br></br>Het apparaat ziet de blokken die worden toegewezen wanneer het volume de maximum grootte heeft. |
| **15.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstream (ADS) is gekoppeld, wordt er geen back-up gemaakt van de ADS of kan deze worden hersteld via herstel na nood gevallen, klonen en herstellen op item niveau. | |
| **15.** |Bestandsserver |Symbolische koppelingen worden niet ondersteund. | |
| **18.** |Bestandsserver |Bestanden die worden beveiligd door Windows Encrypting File System (EFS) wanneer deze worden gekopieerd naar of opgeslagen op de StorSimple-Bestands server van de virtuele matrix, resulteren in een niet-ondersteunde configuratie.  | |

## <a name="next-step"></a>Volgende stap
[Installeer Update 0,4](storsimple-virtual-array-install-update-04.md) op uw virtuele StorSimple-matrix.

## <a name="references"></a>Verwijzingen
Zoekt u een oudere release-Opmerking? Ga naar: 

* [Release opmerkingen bij Update 0,3 StorSimple Virtual array](storsimple-ova-update-03-release-notes.md)
* [Release opmerkingen bij Update 0,1 en 0,2 voor StorSimple Virtual array](storsimple-ova-update-01-release-notes.md)
* [Release opmerkingen bij de algemene Beschik baarheid van StorSimple Virtual array](./storsimple-virtual-array-update-06-release-notes.md)