---
title: Release opmerkingen bij Update 1,0 StorSimple Virtual array
description: Hierin worden essentiële openstaande problemen en oplossingen voor de virtuele StorSimple-matrix met Update 1,0 beschreven.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 2ec88e4e97d45b27c0226198491b3adec6448496
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960237"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Release opmerkingen bij Update 1,0 StorSimple Virtual array

## <a name="overview"></a>Overzicht

In de volgende release opmerkingen worden de kritieke openstaande problemen en de opgeloste problemen voor het bijwerken Microsoft Azure StorSimple van de virtuele-matrix geïdentificeerd.

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw virtuele StorSimple-matrix implementeert.

Update 1,0 komt overeen met de software versie **10.0.10296.0**.

> [!IMPORTANT]
> - Updates zijn storend en opnieuw opstarten van het apparaat. Als I/O wordt uitgevoerd, loopt het apparaat downtime. Ga voor meer informatie over het Toep assen van de update naar [update 1,0 installeren](storsimple-virtual-array-install-update-1.md).
>
> - Update 1 is alleen beschikbaar via de Azure Portal als op uw apparaat Update 0,6 wordt uitgevoerd.

## <a name="whats-new-in-update-10"></a>Wat is er nieuw in update 1,0

**Update 1,0 bevat wijzigingen die betrekking hebben op de verificatie van StorSimple Apparaatbeheer service en moet zo snel mogelijk worden geïmplementeerd.** Deze update bevat de volgende verbeteringen en oplossingen:

 - **Gebruik van Azure Active Directory (Aad) voor verificatie met StorSimple Apparaatbeheer-service** – vanaf update 1,0 wordt Azure Active Directory gebruikt voor verificatie met de StorSimple Apparaatbeheer-service. Het oude verificatie mechanisme wordt vervangen door december 2017. Alle gebruikers moeten de nieuwe verificatie-Url's in hun firewall regels opnemen. Ga naar verificatie-Url's die worden vermeld in de [netwerk vereisten voor uw virtuele StorSimple-matrix](storsimple-ova-system-requirements.md)voor meer informatie.
 
    Als de verificatie-URL niet is opgenomen in de firewall regels, krijgen de gebruikers een kritieke waarschuwing te zien dat hun StorSimple-apparaat niet kan worden geverifieerd bij de service. Als de gebruikers deze waarschuwing zien, moeten ze de nieuwe verificatie-URL toevoegen. Ga naar [StorSimple-netwerk waarschuwingen](storsimple-virtual-array-manage-alerts.md)voor meer informatie.

 - **Verbetering** van de prestaties: er zijn verschillende oplossingen uitgevoerd om de snelheid van de Cloud Lees bewerkingen, de lagen en de lagen te verbeteren. Als gevolg hiervan zijn de prestaties van de back-up en het herstellen verbeterd voor iSCSI-en Bestands server apparaten.

 - **Verbetering van garbagecollection** : deze release bevat oplossingen voor problemen die de prestaties van de garbagecollection-cyclus verbeteren wanneer het apparaat en het opslag account zich in twee externe regio's bevinden.

 - **Verbetering van logboek registratie** : deze release bevat verbeteringen voor logboek registratie met betrekking tot garbagecollection en I/O-pad.


## <a name="issues-fixed-in-update-10"></a>Problemen die zijn opgelost in update 1,0

De volgende tabel bevat een overzicht van de problemen die in deze release zijn opgelost.

| Nee. | Functie | Probleem |
| --- | --- | --- |
| 1 |Verificatie op basis van AAD| Deze release bevat wijzigingen waarmee AAD kan worden geverifieerd met de StorSimple-Apparaatbeheer.|
| 2 |Garbagecollection| Dit probleem is gerapporteerd op de site van een klant waar het apparaat en de opslag accounts zich in verschillende regio's bevinden en de klant heeft periodieke netwerk fouten gerapporteerd die van invloed zijn op de facturering. In deze release is dit probleem opgelost. |
| 3 |Prestaties| Deze release bevat wijzigingen die resulteren in het verbeteren van de prestaties van herstellen/lezen/in de Cloud/laag.|
| 4 |Bijwerken| Er is een probleem opgetreden met de update in de eerdere versie die heeft geleid tot back-upfouten op een klant site. Dit probleem is opgelost in deze release.|

## <a name="known-issues-in-update-10"></a>Bekende problemen in update 1,0

De volgende tabel bevat een samen vatting van bekende problemen voor de virtuele StorSimple-matrix en bevat de release-items die zijn vermeld in de vorige releases.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Updates |De virtuele matrices die in de preview-versie zijn gemaakt, kunnen niet worden bijgewerkt naar een ondersteunde versie van de algemene Beschik baarheid. |Voor deze virtuele matrices moet een failover worden uitgevoerd voor de release van de algemene Beschik baarheid met behulp van een nood herstel werk stroom (DR). |
| **2.** |Ingerichte gegevens schijf |Wanneer u een gegevens schijf van een bepaalde grootte hebt ingericht en de bijbehorende virtuele StorSimple-matrix hebt gemaakt, moet u de gegevens schijf niet uitbreiden of verkleinen. Als u probeert resultaten te maken, gaan de gegevens in de lokale lagen van het apparaat verloren. | |
| **3.** |Groepsbeleid |Wanneer een apparaat lid is van een domein, kan het Toep assen van een groeps beleid een nadelige invloed hebben op de werking van het apparaat. |Zorg ervoor dat uw virtuele matrix zich in een eigen organisatie-eenheid (OE) bevindt voor Active Directory en dat er geen groeps beleidsobjecten worden toegepast. |
| **4.** |Lokale web-UI |Als Verbeterde beveiligings functies zijn ingeschakeld in Internet Explorer (IE ESC), werken sommige lokale web-UI-pagina's, zoals het oplossen van problemen of het onderhoud mogelijk niet goed. De knoppen op deze pagina's werken mogelijk ook niet. |Verbeterde beveiligings functies uitschakelen in Internet Explorer. |
| **5,0.** |Lokale web-UI |In een Hyper-V virtuele machine worden de netwerk interfaces in de Web-UI weer gegeven als 10 Gbps-interfaces. |Dit gedrag is een reflectie van Hyper-V. Hyper-V geeft altijd 10 Gbps weer voor virtuele netwerk adapters. |
| **6,5.** |Gelaagde volumes of shares |Het vergren delen van een byte bereik voor toepassingen die werken met de StorSimple-gelaagde volumes, wordt niet ondersteund. Als het vergren delen van het byte bereik is ingeschakeld, werkt StorSimple lagen niet. |Aanbevolen maat regelen zijn onder andere: <br></br>Schakel de vergren deling van het byte bereik uit in uw toepassings logica.<br></br>U kunt ervoor kiezen om gegevens voor deze toepassing te plaatsen in lokaal vastgemaakte volumes, in tegens telling tot gelaagde volumes.<br></br>Voor *behoud*: wanneer lokaal vastgemaakte volumes en het vergren delen van het bereik van bytes is ingeschakeld, kan het lokaal vastgemaakte volume online zijn, zelfs voordat het herstel is voltooid. Als een herstel bewerking wordt uitgevoerd, moet u wachten tot de herstel bewerking is voltooid. |
| **7.** |Gelaagde shares |Het werken met grote bestanden kan leiden tot trage lagen. |Wanneer u werkt met grote bestanden, raden we aan dat het grootste bestand kleiner is dan 3% van de share grootte. |
| **achtste.** |Gebruikte capaciteit voor shares |U ziet mogelijk het verbruik delen als de share geen gegevens bevat. Dit verbruik is omdat de gebruikte capaciteit voor shares meta gegevens bevat. | |
| **9,4.** |Herstel na noodgeval |U kunt alleen herstel na nood geval uitvoeren voor een bestands server naar hetzelfde domein als die van het bron apparaat. Herstel na nood gevallen naar een doel apparaat in een ander domein wordt niet ondersteund in deze release. |Dit is geïmplementeerd in een latere versie. Ga voor meer informatie naar [failover en herstel na nood gevallen voor uw virtuele StorSimple-matrix](storsimple-virtual-array-failover-dr.md) |
| **6.** |Azure PowerShell |De virtuele StorSimple-matrices kunnen niet worden beheerd via de Azure PowerShell in deze release. |Het beheer van de virtuele apparaten moet worden uitgevoerd via de Azure Portal en de lokale webgebruikersinterface. |
| **9.** |Wachtwoord wijzigen |De console van de virtuele matrix heeft alleen invoer in en-US-toetsenbord indeling toegestaan. | |
| **12.** |CHAP |De CHAP-referenties kunnen niet worden verwijderd als deze eenmaal zijn gemaakt. Als u de CHAP-referenties wijzigt, moet u bovendien de volumes offline halen en deze vervolgens online brengen om de wijziging door te voeren. |Dit probleem wordt opgelost in een latere versie. |
| **13.** |iSCSI-server |De gebruikte opslag ruimte die voor een iSCSI-volume wordt weer gegeven, kan verschillen in de StorSimple-Apparaatbeheer service en de iSCSI-host. |De iSCSI-host heeft de bestandssysteem weergave.<br></br>Het apparaat ziet de blokken die worden toegewezen wanneer het volume de maximum grootte heeft. |
| **15.** |Bestandsserver |Als aan een bestand in een map een alternatieve gegevensstream (ADS) is gekoppeld, wordt er geen back-up gemaakt van de ADS of kan deze worden hersteld via herstel na nood gevallen, klonen en herstellen op item niveau. | |
| **15.** |Bestandsserver |Symbolische koppelingen worden niet ondersteund. | |
| **18.** |Bestandsserver |Bestanden die worden beveiligd door Windows Encrypting File System (EFS) wanneer deze worden gekopieerd naar of opgeslagen op de StorSimple-Bestands server van de virtuele matrix, resulteren in een niet-ondersteunde configuratie.  | |
| **Nr.** |Updates |Als fout code: 2359302 (hex 0x240006) wordt weer geven tijdens het installeren van een hotfix via de lokale gebruikers interface, houdt dit in dat de hotfix al op uw apparaat is geïnstalleerd.   | |
| **18,0.** |Updates |Als u de lokale web-UI gebruikt om update 1 op uw virtuele matrix te installeren, moet u ervoor zorgen dat u update 0,6 uitvoert. Als u een lagere versie dan Update 0,6 gebruikt, moet u eerst Update 0,6 installeren en vervolgens Update 1 Toep assen. Als u update 1,0 rechtstreeks installeert vanaf een versie van vóór de update 0,6, zullen sommige updates worden verwijderd en worden de bewakings grafieken niet meer gebruikt.   | |


## <a name="next-steps"></a>Volgende stappen
[Installeer Update 1,0](storsimple-virtual-array-install-update-1.md) op uw virtuele StorSimple-matrix.

## <a name="references"></a>Verwijzingen
Zoekt u een oudere release-Opmerking? Ga naar:
*  [Release opmerkingen bij Update 0,6 StorSimple Virtual array](storsimple-virtual-array-update-06-release-notes.md)
* [Release opmerkingen bij Update 0,5 StorSimple Virtual array](storsimple-virtual-array-update-05-release-notes.md)
* [Release opmerkingen bij Update 0,4 StorSimple Virtual array](storsimple-virtual-array-update-04-release-notes.md)
* [Release opmerkingen bij Update 0,3 StorSimple Virtual array](storsimple-ova-update-03-release-notes.md)
* [Release opmerkingen bij Update 0,1 en 0,2 voor StorSimple Virtual array](storsimple-ova-update-01-release-notes.md)
* [Release opmerkingen bij de algemene Beschik baarheid van StorSimple Virtual array](./storsimple-virtual-array-update-06-release-notes.md)