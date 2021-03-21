---
title: Release opmerkingen bij StorSimple Virtual array Update 0,2 & 0,1
description: Hierin worden essentiële openstaande problemen en oplossingen voor de virtuele StorSimple-matrix met Update 0,2 en 0,1 beschreven.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966153"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Release opmerkingen bij Update 0,2 en 0,1 voor StorSimple Virtual array
## <a name="overview"></a>Overzicht
In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd. (Microsoft Azure StorSimple virtuele matrix staat ook bekend als het StorSimple on-premises virtuele apparaat of het virtuele StorSimple-apparaat.) 

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-apparaat implementeert.

Update 0,2 komt overeen met de software versie **10.0.10280.0**; Update 0,1 is versie **10.0.10279.0**. In de volgende secties worden de wijzigingen voor elke update weer gegeven. 

> [!NOTE]
> Updates zijn storend en het apparaat wordt opnieuw opgestart. Als I/O wordt uitgevoerd, zal het apparaat downtime.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemen die zijn opgelost in de update 0,2
Update 0,2 bevat alle wijzigingen van Update 0,1 naast de oplossing die in de volgende tabel wordt beschreven:

| Functie | Probleem |
| --- | --- |
| Updates |In de laatste release werden updates niet automatisch gedetecteerd in de klassieke Azure-Portal. Daarom moest u de lokale web-UI gebruiken om updates te installeren. Dit probleem is opgelost in deze release. Na de installatie van Update 0,2 kunt u toekomstige updates installeren met behulp van de klassieke Azure-Portal. |

## <a name="whats-new-in-the-update-01"></a>Wat is er nieuw in de update 0,1
Update 0,1 bevat de volgende oplossingen voor fouten en verbeteringen. 

* **Verbeterde tolerantie voor Cloud** storingen: deze release bevat verschillende fout oplossingen rond herstel na nood gevallen, back-ups maken, herstellen en trapsgewijs scha kelen in het geval van een storing in de Cloud verbinding. 
* **Verbeterde prestaties voor herstellen**: deze release bevat oplossingen voor fouten die de voltooiings tijd van de herstel taken aanzienlijk verminderen.
* **Optimalisatie van automatische ruimte winning**: wanneer gegevens worden verwijderd op thin provisioned volumes, moeten de ongebruikte opslag blokken worden vrijgemaakt. Met deze versie is het proces voor het vrijmaken van ruimte in de Cloud verbeterd, waardoor de ongebruikte ruimte sneller beschikbaar is in vergelijking met de vorige versies.
* **Nieuwe installatie kopieën voor virtuele schijven**: nieuwe VHD, VHDX en VMDK zijn nu beschikbaar via de klassieke Azure-Portal. U kunt deze installatie kopieën downloaden om nieuwe update 0,1-apparaten in te richten.
* **De nauw keurigheid van de status van taken in de portal verbeteren**: in de eerdere versie van de software is taak status rapportage in de portal niet gedetailleerd. Dit probleem wordt opgelost in deze release.
* **Domein deelname**: problemen oplossen met betrekking tot het toevoegen aan een domein en het wijzigen van de naam van het apparaat.

## <a name="issues-fixed-in-the-update-01"></a>Problemen die zijn opgelost in de update 0,1
De volgende tabel bevat een overzicht van de problemen die in deze release zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |VMDK |In sommige VMware-versies werd de besturingssysteem schijf gezien als verspreide waarschuwingen, waardoor normale bewerkingen worden verstoord. Dit probleem is opgelost in deze release. |
| 2 |iSCSI-server |In de laatste release moest de gebruiker een gateway opgeven voor elke ingeschakelde netwerk interface van het virtuele StorSimple-apparaat. Dit gedrag wordt gewijzigd in deze release, zodat de gebruiker ten minste één gateway moet configureren voor alle ingeschakelde netwerk interfaces. |
| 3 |Ondersteunings pakket |In de eerdere versie van de software is de ondersteunings pakket verzameling mislukt wanneer de pakket grootten groter zijn dan 1 GB. Dit probleem is opgelost in deze release. |
| 4 |Cloud toegang |Als de virtuele StorSimple-matrix in de laatste release geen netwerk verbinding heeft en opnieuw is opgestart, zouden de lokale gebruikers interface verbindings problemen hebben. Dit probleem is opgelost in deze release. |
| 5 |Bewakings grafieken |In de vorige versie, na een failover van een apparaat, worden in de grafieken voor Cloud capaciteits gebruik onjuiste waarden weer gegeven in de klassieke Azure-Portal. Dit is in de huidige release opgelost. |

## <a name="known-issues-in-the-update-01"></a>Bekende problemen in de update 0,1
De volgende tabel bevat een samen vatting van bekende problemen voor de virtuele StorSimple-matrix en bevat de release-items die zijn vermeld in de vorige releases. **De release van de problemen die in deze release wordt vermeld, is gemarkeerd met een sterretje. Bijna alle problemen in deze lijst zijn overgenomen van de GA-versie van StorSimple Virtual array.**

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele apparaten die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie van de algemene Beschik baarheid. |Voor deze virtuele apparaten moet een failover worden uitgevoerd voor de release van de algemene Beschik baarheid met behulp van een werk stroom voor nood herstel (DR). |
| **2.** |Ingerichte gegevens schijf |Wanneer u een gegevens schijf van een bepaalde grootte hebt ingericht en het bijbehorende virtuele StorSimple-apparaat hebt gemaakt, moet u de gegevens schijf niet uitbreiden of verkleinen. Als u dit probeert te doen, gaan alle gegevens in de lokale lagen van het apparaat verloren. | |
| **3.** |Groepsbeleid |Wanneer een apparaat lid is van een domein, kan het Toep assen van een groeps beleid een nadelige invloed hebben op de werking van het apparaat. |Zorg ervoor dat uw virtuele matrix zich in een eigen organisatie-eenheid (OE) bevindt voor Active Directory en dat er geen groeps beleidsobjecten worden toegepast. |
| **4.** |Lokale web-UI |Als Verbeterde beveiligings functies zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-UI-pagina's, zoals het oplossen van problemen of het onderhoud mogelijk niet goed. De knoppen op deze pagina's werken mogelijk ook niet. |Verbeterde beveiligings functies uitschakelen in Internet Explorer. |
| **5.** |Lokale web-UI |In een Hyper-V virtuele machine worden de netwerk interfaces in de Web-UI weer gegeven als 10 Gbps-interfaces. |Dit gedrag is een reflectie van Hyper-V. Hyper-V geeft altijd 10 Gbps weer voor virtuele netwerk adapters. |
| **6.** |Gelaagde volumes of shares |Het vergren delen van een byte bereik voor toepassingen die werken met de StorSimple-gelaagde volumes, wordt niet ondersteund. Als het vergren delen van het byte bereik is ingeschakeld, werkt StorSimple lagen niet. |Aanbevolen maat regelen zijn onder andere: <br></br>Schakel de vergren deling van het byte bereik uit in uw toepassings logica.<br></br>U kunt ervoor kiezen om gegevens voor deze toepassing te plaatsen in lokaal vastgemaakte volumes, in tegens telling tot gelaagde volumes.<br></br>Voor *behoud*: als lokaal vastgemaakte volumes en het vergren delen van het bereik van bytes is ingeschakeld, moet u er rekening mee houden dat het lokaal vastgemaakte volume online kan zijn, zelfs voordat de herstel bewerking is voltooid. Als een herstel bewerking wordt uitgevoerd, moet u wachten tot de herstel bewerking is voltooid. |
| **7.** |Gelaagde shares |Het werken met grote bestanden kan leiden tot trage lagen. |Wanneer u werkt met grote bestanden, raden we aan dat het grootste bestand kleiner is dan 3% van de share grootte. |
| **achtste.** |Gebruikte capaciteit voor shares |Het is mogelijk dat er deel verbruik wordt weer gegeven als er geen gegevens op de share zijn. Dit komt omdat de gebruikte capaciteit voor shares meta gegevens bevat. | |
| **9,4.** |Herstel na noodgeval |U kunt alleen herstel na nood geval uitvoeren voor een bestands server naar hetzelfde domein als die van het bron apparaat. Herstel na nood gevallen naar een doel apparaat in een ander domein wordt niet ondersteund in deze release. |Dit wordt in een latere versie geïmplementeerd. |
| **6.** |Azure PowerShell |De virtuele StorSimple-apparaten kunnen niet worden beheerd via de Azure PowerShell in deze release. |Het beheer van de virtuele apparaten moet worden uitgevoerd via de klassieke Azure-Portal en de lokale webgebruikersinterface. |
| **9.** |Wachtwoord wijzigen |De console van de virtuele matrix heeft alleen invoer in en-US-toetsenbord indeling toegestaan. | |
| **12.** |CHAP |De CHAP-referenties kunnen niet worden verwijderd als deze eenmaal zijn gemaakt. Als u de CHAP-referenties wijzigt, moet u bovendien de volumes offline halen en deze vervolgens online brengen om de wijziging door te voeren. |Deze worden in een latere versie besproken. |
| **13.** |iSCSI-server |De gebruikte opslag ruimte die voor een iSCSI-volume wordt weer gegeven, kan verschillen in de StorSimple Manager-service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteem weergave.<br></br>Het apparaat ziet de blokken die worden toegewezen wanneer het volume de maximum grootte heeft. |
| **15.** |Bestands server * |Als aan een bestand in een map een alternatieve gegevensstream (ADS) is gekoppeld, wordt er geen back-up gemaakt van de ADS of kan deze worden hersteld via herstel na nood gevallen, klonen en herstellen op item niveau. | |

## <a name="next-step"></a>Volgende stap
[Installeer updates](./storsimple-virtual-array-install-update-06.md) op uw virtuele StorSimple-matrix.