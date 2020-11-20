---
title: Release opmerkingen bij StorSimple 8000 Series update 4 | Microsoft Docs
description: Hierin worden de nieuwe functies, problemen en tijdelijke oplossingen voor de StorSimple 8000 Series update 4 beschreven.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954049"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Release opmerkingen bij StorSimple 8000 Series update 4

## <a name="overview"></a>Overzicht

In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële openstaande problemen voor de StorSimple 8000 Series update 4 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple software-updates die zijn opgenomen in deze release. 

Update 4 kan worden toegepast op elk StorSimple-apparaat waarop release (GA) of update 0,1 via update 3,1. De versie van het apparaat die is gekoppeld aan update 4 is 6.3.9600.17820.

Raadpleeg de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert.

> [!IMPORTANT]
> * Update 4 heeft apparaatsoftware, eigenschappen van-firmware, LSI-stuur programma en firmware, schijf firmware, Storport en Spaceport, beveiliging en andere updates van het besturings systeem. Het duurt ongeveer 4 uur om deze update te installeren. Update van de schijf firmware is een storende update en resulteert in een downtime voor uw apparaat. U wordt aangeraden update 4 toe te passen om uw apparaat up-to-date te houden. 
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Wacht enkele dagen en zoek opnieuw naar updates, aangezien deze binnenkort beschikbaar worden.

## <a name="whats-new-in-update-4"></a>Wat is er nieuw in update 4

In update 4 zijn de volgende belang rijke verbeteringen en oplossingen voor oplossingen aangebracht.

* **Algoritmen voor het automatisch inwinnen van de slimmere ruimte** : in update 4 zijn de algoritmen voor automatische ruimte vrijmaken verbeterd om de ruimte op te stellen op basis van de verwachte vrijgemaakte ruimte die beschikbaar is in de Cloud. 

* **Prestatie verbeteringen voor lokaal vastgemaakte volumes** : update 4 heeft de prestaties van lokaal vastgemaakte volumes verbeterd in scenario's met hoge gegevens opname (gegevens die vergelijkbaar zijn met de volume grootte).

* **Herstel op basis van Heatmap** : in de eerdere releases na een nood herstel (Dr) zijn de gegevens hersteld vanuit de Cloud op basis van de toegangs patronen die het resultaat zijn van een trage prestatie. 

    Er wordt een nieuwe functie geïmplementeerd in update 4 die regel matig gebruikte gegevens bijhoudt om een heatmap te maken wanneer het apparaat wordt gebruikt vóór DR. (de meeste gebruikte gegevens segmenten hebben een hoge hitte terwijl minder hitte segmenten weinig worden gebruikt). Na DR gebruikt StorSimple de heatmap om de gegevens van de Cloud automatisch te herstellen en opnieuw te gebruiken. 

    Alle herstel bewerkingen zijn nu gebaseerd op heatmap. Ga naar [Windows PowerShell voor StorSimple-cmdlet-verwijzing](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps)voor meer informatie over het opvragen en annuleren van Heatmap herstel-en rehydratatie-taken.

* **Hulp programma StorSimple Diagnostics** : in update 4 wordt een StorSimple Diagnostics-hulp programma vrijgegeven waarmee u eenvoudig diagnoses en problemen kunt oplossen met betrekking tot de status van het systeem, het netwerk, de prestaties en de hardware van het onderdeel. Dit hulp programma wordt uitgevoerd via de Windows PowerShell voor StorSimple. Ga voor meer informatie naar [problemen oplossen met het hulp programma StorSimple Diagnostics](storsimple-8000-diagnostics.md).

* **Hulp programma voor StorSimple-migratie** van de gebruikers interface: vóór deze release hebben de migratie van gegevens van de 5000-7000-serie vereist dat de gebruikers een deel van de migratie werk stroom uitvoeren met behulp van de Azure PowerShell-interface. In deze release wordt een gebruiks vriendelijk, op gebruikers interface gebaseerd hulp programma voor StorSimple-migratie beschikbaar gesteld om de migratie werk stroom te vergemakkelijken. Met dit hulp programma kunt u ook herstel buckets samen voegen. 

* **FIPS-gerelateerde wijzigingen** -deze release gaat standaard ingeschakeld op alle apparaten met de StorSimple 8000-serie voor zowel de Microsoft Azure Government als Azure open bare-Cloud accounts.

* **Wijzigingen** in deze release bijwerken: fouten die betrekking hebben op Update fouten, zijn opgelost.

* **Waarschuwing voor schijf fouten** : er wordt een nieuwe waarschuwing gegeven die de gebruiker waarschuwt dat er problemen met de harde schijf zijn toegevoegd in deze versie. Als u deze waarschuwing ontvangt, neemt u contact op met Microsoft Ondersteuning om een vervangende schijf te verzenden. Ga voor meer informatie naar [hardwareproblemen op uw StorSimple-apparaat](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Vervanging** van de controller: een cmdlet waarmee de gebruiker de status van het vervangings proces van de controller kan opvragen, wordt toegevoegd in deze release. Voor meer informatie gaat u naar de [cmdlet voor het opvragen van de vervangings status](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps)van de controller.


## <a name="issues-fixed-in-update-4"></a>Problemen die zijn opgelost in update 4

De volgende tabel bevat een overzicht van problemen die zijn opgelost in update 4.    

| Nee | Functie | Probleem | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Failover |In de eerdere versie was er na de failover een probleem met het opschonen van de klant. Dit probleem is opgelost in deze release. |Ja |Ja |
| 2 |Lokaal vastgemaakte volumes |In de vorige versie is er een probleem opgetreden bij het maken van een gerelateerd volume voor lokaal vastgemaakte volumes, waardoor er fouten ontstaan bij het maken van volumes. Dit probleem is in deze release veroorzaakt en opgelost. |Ja |Nee |
| 3 |Ondersteunings pakket |In de vorige versie zijn er problemen met het ondersteunings pakket die resulteren in een System. OutOfMemory-uitzonde ring of andere fouten die resulteren in een probleem bij het maken van een ondersteunings pakket. Deze fouten zijn opgelost in deze release. |Ja |Ja |
| 4 |Bewaking |In de vorige versie heeft zich een probleem voorgedaan bij het bewaken van grafieken voor lokaal vastgemaakte volumes waarbij verbruik in EB werd weer gegeven. Deze fout is opgelost in deze release. |Ja |Ja |
| 5 |Migratie |In de vorige versie waren er diverse problemen met de betrouw baarheid van de migratie van de 5000-7000-serie naar 8000 Series-apparaten. Deze problemen zijn opgelost in deze release. |Ja |Ja |
| 6 |Bijwerken |Als er bij eerdere releases een update fout is opgetreden, gaan de controllers in de herstel modus. de gebruiker kan daarom niet door gaan met de update en moet contact opnemen met Microsoft Ondersteuning. <br> Dit gedrag is gewijzigd in deze release. Als de gebruiker een update fout heeft nadat beide controllers dezelfde versie hebben (update 4), gaan de controllers niet naar de herstel modus. Als de gebruiker deze fout tegen komt, raden we u aan te wachten op een beetje en de update opnieuw uit te voeren. De nieuwe poging slaagt. Als de nieuwe poging mislukt, moet u contact opnemen met Microsoft Ondersteuning. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Bekende problemen in update 4 van eerdere versies

Er zijn geen nieuwe bekende problemen in update 4. Voor een lijst met problemen die zijn overgedragen aan update 4 van eerdere versies, gaat u naar [Update 3 release opmerkingen](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>SAS-controller (Serial-Attached SCSI) en firmware-updates in update 4

Deze release heeft SAS controller-en LSI-Stuur Programma's en firmware-updates. Zie [Update 4 installeren](./storsimple-8000-install-update-4.md) op uw StorSimple-apparaat voor meer informatie over het installeren van deze updates.

## <a name="virtual-device-updates-in-update-4"></a>Updates van het virtuele apparaat in update 4

Deze update kan niet worden toegepast op de StorSimple Cloud Appliance (ook wel bekend als het virtuele apparaat). Er moeten nieuwe virtuele apparaten worden gemaakt. 

## <a name="next-step"></a>Volgende stap

Meer informatie over het [installeren van update 4](./storsimple-8000-install-update-4.md) op uw StorSimple-apparaat.