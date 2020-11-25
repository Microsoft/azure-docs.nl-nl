---
title: Failover en herstel na nood gevallen voor StorSimple Series-apparaat van de 8000-serie
description: Meer informatie over het uitvoeren van een failover van uw StorSimple-apparaat naar zichzelf, een ander fysiek apparaat of een Cloud apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017114"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Failover en herstel na noodgevallen voor apparaat uit de StorSimple 8000-serie

## <a name="overview"></a>Overzicht

In dit artikel wordt de functie Failover van het apparaat voor de StorSimple 8000-serie apparaten beschreven en hoe deze functie kan worden gebruikt om StorSimple-apparaten te herstellen als er sprake is van een nood geval. StorSimple gebruikt failover van het apparaat om de gegevens van een bron apparaat in het Data Center te migreren naar een ander doel apparaat. De richt lijnen in dit artikel zijn van toepassing op fysieke apparaten van de StorSimple 8000-serie en Cloud apparaten met software versies update 3 en hoger.

StorSimple maakt gebruik van de Blade **apparaten** om de functie Failover van het apparaat te starten in het geval van een ramp. Deze Blade bevat een lijst met alle StorSimple-apparaten die zijn verbonden met uw StorSimple-Apparaatbeheer service.

![Blade apparaten](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Herstel na nood geval (DR) en failover van apparaat

In een scenario voor nood herstel (DR) functioneert het primaire apparaat niet meer. StorSimple maakt gebruik van het primaire apparaat als _bron_ en verplaatst de gekoppelde cloud gegevens naar een ander _doel_ apparaat. Dit proces wordt de *failover* genoemd. In de volgende afbeelding ziet u het proces van failover.

![Wat gebeurt er bij failover van het apparaat?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Het doel apparaat voor een failover kan een fysiek of zelfs een Cloud apparaat zijn. Het doel apparaat bevindt zich mogelijk op dezelfde of een andere geografische locatie dan het bron apparaat.

Tijdens de failover kunt u volume containers selecteren voor migratie. StorSimple wijzigt vervolgens het eigendom van deze volume containers van het bron apparaat naar het doel apparaat. Zodra de volume containers het eigendom wijzigen, verwijdert StorSimple deze containers van het bron apparaat. Nadat de verwijdering is voltooid, kunt u een failback uitvoeren naar het doel apparaat. _Failback_ stuurt het eigendom terug naar het oorspronkelijke bron apparaat.

### <a name="cloud-snapshot-used-during-device-failover"></a>Cloud momentopname gebruikt tijdens failover van apparaat

Na een DR. de meest recente Cloud back-up wordt gebruikt om de gegevens naar het doel apparaat te herstellen. Zie [de StorSimple-Apparaatbeheer-service gebruiken om een hand matige back-up te maken](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup)voor meer informatie over Cloud momentopnamen.

In een StorSimple 8000-serie zijn back-upbeleid gekoppeld aan back-ups. Als er meerdere back-upbeleid voor hetzelfde volume is, selecteert StorSimple het back-upbeleid met het grootste aantal volumes. StorSimple gebruikt vervolgens de meest recente back-up van het geselecteerde back-upbeleid om de gegevens op het doel apparaat te herstellen.

Stel dat er twee back-upbeleid, *defaultPol* en *customPol*:

* *defaultPol*: één volume, *VOL1*, wordt dagelijks uitgevoerd vanaf 10:30 uur.
* *customPol*: vier volumes, *VOL1*, *VOL2*, *VOL3*, *VOL4*, worden dagelijks uitgevoerd vanaf 10:00 uur.

In dit geval StorSimple prioriteiten voor crash-consistentie en maakt gebruik van *customPol* wanneer het meer volumes heeft. De meest recente back-up van dit beleid wordt gebruikt om gegevens te herstellen. Ga voor meer informatie over het maken en beheren van back-upbeleid naar [gebruik van de StorSimple Apparaatbeheer-service om back-upbeleiden te beheren](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Algemene overwegingen voor failover van apparaat

Lees de volgende informatie voordat u een failover van een apparaat doorstuurt:

* Voordat een failover van een apparaat wordt gestart, moeten alle volumes in de volume containers offline zijn. Bij een niet-geplande failover worden StotSimple-volumes automatisch offline gezet. Maar als u een geplande failover uitvoert (om DR te testen), moet u alle volumes offline halen.
* Alleen de volume containers met een bijbehorende Cloud momentopname worden weer gegeven voor DR. Er moet ten minste één volume container zijn met een bijbehorende Cloud momentopname om gegevens te herstellen.
* Als er Cloud momentopnamen zijn die over meerdere volume containers beschikken, StorSimple failover van deze volume containers als een set. Als er sprake is van lokale moment opnamen die betrekking hebben op meerdere volume containers, maar gekoppelde cloud momentopnamen, wordt in een zeldzame exemplaar een failover uitgevoerd voor de lokale moment opnamen en worden de lokale gegevens na DR verwijderd.
* De beschik bare doel apparaten voor DR zijn apparaten met voldoende ruimte voor de geselecteerde volume containers. Apparaten die niet voldoende ruimte hebben, worden niet vermeld als doel apparaten.
* Na een DR (voor een beperkte duur) kan de prestaties van de gegevens toegang aanzienlijk worden beïnvloed, omdat het apparaat toegang moet hebben tot de gegevens uit de Cloud en lokaal kan worden opgeslagen.

#### <a name="device-failover-across-software-versions"></a>Failover van het apparaat tussen software versies

Een StorSimple-Apparaatbeheer service in een implementatie heeft mogelijk meerdere apparaten, zowel fysiek als Cloud, alle actieve verschillende software versies.

Gebruik de volgende tabel om te bepalen of u een failover of failback kunt uitvoeren naar een ander apparaat met een andere software versie en hoe de volume typen zich gedragen tijdens DR.

#### <a name="failover-and-failback-across-software-versions"></a>Failover en failback tussen software versies

| Failover/failback van | Fysiek apparaat | Cloudapparaat |
| --- | --- | --- |
| Update 3 voor update 4 |Failover van gelaagde volumes als een laag. <br></br>Failover van lokaal vastgemaakte volumes is lokaal vastgemaakt. <br></br> Na het uitvoeren van een failover wanneer u een moment opname maakt op het apparaat met update 4, wordt [heatmap op basis van bijhouden](storsimple-update4-release-notes.md#whats-new-in-update-4) . |Failover van lokaal vastgemaakte volumes als een laag. |
| Update 4 voor update 3 |Failover van gelaagde volumes als een laag. <br></br>Failover van lokaal vastgemaakte volumes is lokaal vastgemaakt. <br></br> Back-ups die worden gebruikt voor het herstellen van behoud van Heatmap-meta gegevens. <br></br>Tracering op basis van Heatmap is niet beschikbaar in update 3 na een failback. |Failover van lokaal vastgemaakte volumes als een laag. |


## <a name="device-failover-scenarios"></a>Failover-scenario's voor apparaten

Als er een nood geval is, kunt u ervoor kiezen om een failover uit te laten zien van uw StorSimple-apparaat:

* [Naar een fysiek apparaat](storsimple-8000-device-failover-physical-device.md).
* [Naar zichzelf](storsimple-8000-device-failover-same-device.md).
* [Naar een Cloud apparaat](storsimple-8000-device-failover-cloud-appliance.md).

De voor gaande artikelen bevatten gedetailleerde stappen voor elk van de bovenstaande failover-cases.


## <a name="failback"></a>Failback

Voor update 3 en latere versies ondersteunt StorSimple ook failback. Failback is slechts een omgekeerde failover, het doel wordt de bron en het oorspronkelijke bron apparaat wordt nu in de failover het doel apparaat. 

Tijdens het failback worden de gegevens opnieuw gesynchroniseerd naar de primaire locatie, wordt de I/O-en toepassings activiteit gestopt en wordt er teruggezet naar de oorspronkelijke locatie.

Nadat een failover is voltooid, voert StorSimple de volgende acties uit:

* StorSimple reinigt de volume containers waarvoor failover van het bron apparaat is uitgevoerd.
* StorSimple initieert een achtergrond taak per volume container (failover) op het bron apparaat. Als u probeert een failback uit te voeren terwijl de taak wordt uitgevoerd, ontvangt u een melding voor dat effect. Wacht tot de taak is voltooid om de failback te starten.
* De tijd die nodig is om het verwijderen van volume containers te volt ooien, is afhankelijk van verschillende factoren, zoals de hoeveelheid gegevens, de leeftijd van de gegevens, het aantal back-ups en de beschik bare netwerk bandbreedte voor de bewerking.

Als u testfailover wilt plannen of failbacks wilt testen, raden we u aan om volume containers met minder gegevens te testen (GB). Normaal gesp roken kunt u de failback 24 uur starten nadat de failover is voltooid.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

V. **Wat gebeurt er als DR mislukt of gedeeltelijk is geslaagd?**

A. Als de DR mislukt, raden we u aan het opnieuw te proberen. De tweede failover-taak van het apparaat is op de hoogte van de voortgang van de eerste taak en begint vanaf dat moment.

V. **Kan ik een apparaat verwijderen terwijl de failover van het apparaat wordt uitgevoerd?**

A. U kunt een apparaat niet verwijderen terwijl er een DR-bewerking wordt uitgevoerd. U kunt uw apparaat alleen verwijderen nadat de DR is voltooid. U kunt de voortgang van de failover-taak van een apparaat controleren op de Blade **taken** .

V. **Wanneer start de garbagecollection op het bron apparaat zodat de lokale gegevens op het bron apparaat worden verwijderd?**

A. Garbage Collection is alleen ingeschakeld op het bron apparaat nadat het apparaat volledig is opgeruimd. Het opschonen omvat het opschonen van objecten waarvoor een failover van het bron apparaat is uitgevoerd, zoals volumes, back-upobjecten (niet gegevens), volume containers en beleids regels.

V. **Wat gebeurt er als de Verwijder taak die is gekoppeld aan de volume containers in het bron apparaat mislukt?**

A.  Als de Verwijder taak mislukt, kunt u de volume containers hand matig verwijderen. Selecteer uw bron apparaat op de Blade **apparaten** en klik op **volume containers**. Selecteer de volume containers waarvoor u een failover hebt uitgevoerd en klik onder aan de Blade op **verwijderen**. Nadat u alle failover-volume containers op het bron apparaat hebt verwijderd, kunt u de failback starten. Ga voor meer informatie naar [een volume container verwijderen](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Bedrijfs continuïteit nood herstel (BCDR)

Een scenario voor een herstel na nood geval in bedrijfs continuïteit (BCDR) treedt op wanneer het hele Azure-Data Center niet meer werkt. Dit scenario kan van invloed zijn op uw StorSimple Apparaatbeheer-service en de bijbehorende StorSimple-apparaten.

Als een StorSimple-apparaat is geregistreerd vlak voordat een nood geval is opgetreden, moet het apparaat mogelijk opnieuw worden ingesteld op de fabrieks instellingen. Na de nood geval wordt het StorSimple-apparaat in de Azure Portal als offline weer gegeven. Dit apparaat moet worden verwijderd uit de portal. Het apparaat terugzetten naar de fabrieks instellingen en het opnieuw registreren bij de service.

## <a name="next-steps"></a>Volgende stappen

Als u een failover voor een apparaat wilt uitvoeren, kiest u een van de volgende scenario's voor gedetailleerde instructies:

- [Failover naar een ander fysiek apparaat](storsimple-8000-device-failover-physical-device.md)
- [Failover naar hetzelfde apparaat](storsimple-8000-device-failover-same-device.md)
- [Failover naar StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Als u een failover hebt uitgevoerd voor uw apparaat, kiest u een van de volgende opties:

* [Uw StorSimple-apparaat deactiveren of verwijderen](storsimple-8000-deactivate-and-delete-device.md).
* [Gebruik de StorSimple Apparaatbeheer-service om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).

