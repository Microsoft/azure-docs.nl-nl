---
title: Update 1,0 op StorSimple Virtual array installeren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Web-UI van StorSimple Virtual array gebruikt om update 1,0 toe te passen met behulp van de methode Azure Portal en Hot Fix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: d05395f97aded0b57780f898e9cba6ddca491f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005811"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Update 1,0 installeren op de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om update 1,0 te installeren op uw virtuele StorSimple-matrix via de lokale webgebruikersinterface en via de Azure Portal.

U past de software-updates of hotfixes toe om uw virtuele StorSimple-matrix up-to-date te houden. Voordat u een update toepast, raden we u aan om eerst de volumes of shares op de host offline te zetten en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens. Nadat de volumes of shares offline zijn, moet u ook een hand matige back-up van het apparaat maken.

> [!IMPORTANT]
>
> - Update 1,0 komt overeen met de **10.0.10296.0** -software versie op het apparaat. Voor informatie over wat er nieuw is in deze update gaat u naar [release opmerkingen voor update 1,0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Als u een update of hotfix installeert, wordt het apparaat opnieuw opgestart. Gezien de StorSimple virtuele matrix is een apparaat met één knoop punt, worden alle I/O-bewerkingen onderbroken en wordt de uitval tijd van uw apparaat verstoord.
>
> - Update 1 is alleen beschikbaar in de Azure Portal als update 0,6 wordt uitgevoerd op de virtuele matrix. Voor virtuele matrices met een versie van vóór update 0,6 moet u eerst Update 0,6 installeren en vervolgens Update 1 installeren.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u update 0,2 en hoger uitvoert, wordt u aangeraden updates te installeren via de Azure Portal. Voor de portal procedure moet de gebruiker de updates scannen, downloaden en installeren. Afhankelijk van de software versie waarop uw virtuele array wordt uitgevoerd, wordt het Toep assen van een update via de Azure Portal anders.

 - Als uw virtuele array Update 0,6 uitvoert, installeert de Azure Portal direct update 1 (10.0.10296.0) op het apparaat. Deze procedure duurt circa 7 minuten om te volt ooien.
 - Als uw virtuele array een versie van vóór update 0,6, wordt update uitgevoerd in twee fasen. De Azure Portal installeert eerst Update 0,6 (10.0.10293.0) op het apparaat. De virtuele matrix wordt opnieuw opgestart en de portal installeert vervolgens Update 1 (10.0.10296.0) op het apparaat. Deze procedure duurt ongeveer 15 minuten.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Nadat de installatie is voltooid, gaat u naar de StorSimple-Apparaatbeheer service. Selecteer **apparaten** en selecteer en klik vervolgens op het apparaat dat u zojuist hebt bijgewerkt. Ga naar **instellingen > > updates** van het apparaat te beheren. De weer gegeven software versie moet **10.0.10296.0** zijn.

![Software versie na update](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>De lokale web-UI gebruiken

Er zijn twee stappen voor het gebruik van de lokale web-UI:

* De update of hotfix downloaden
* De update of hotfix installeren

> [!IMPORTANT] 
> **Ga alleen verder met deze update als u update 0,6 (10.0.10293.0) uitvoert. Als u een eerdere versie gebruikt, installeert u eerst [update 0,6](storsimple-virtual-array-install-update-06.md) op uw apparaat en past u vervolgens Update 1 toe.**

### <a name="download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

Als uw virtuele array Update 0,6 uitvoert, voert u de volgende stappen uit om update 1 te downloaden uit de Microsoft Update catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

1. Start Internet Explorer en ga naar [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Als u de Microsoft Update catalogus voor de eerste keer op deze computer gebruikt, klikt u op **installeren** wanneer u wordt gevraagd de invoeg toepassing Microsoft Update catalogus te installeren.

3. Voer in het zoekvak van de catalogus van de Microsoft Update het Knowledge Base-nummer (KB) in van de hotfix die u wilt downloaden. Voer **4047203** in bij Update 1,0 en klik vervolgens op **zoeken**.
   
    De hotfix-vermelding wordt weer gegeven, bijvoorbeeld **StorSimple Virtual array Update 1,0**.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klik op **Downloaden**.

5. Down load de twee bestanden naar een map. U kunt de map ook kopiëren naar een netwerk share die bereikbaar is vanaf het apparaat.

6. Open de map waarin de bestanden zich bevinden.

    ![Bestanden in het pakket](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    U ziet twee bestanden:
    -  Een Microsoft Update zelfstandig pakket bestand `WindowsTH-KB3011067-x64` . Dit bestand wordt gebruikt om de software van het apparaat bij te werken.
    - Een bestand dat cumulatieve updates voor augustus bevat `windows8.1-kb4034681-x64` . Voor meer informatie over wat er in dit pakket is opgenomen, gaat u naar [maandelijkse beveiligings update van augustus](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>De update of hotfix installeren

Voordat u de update of hotfix installeert, moet u het volgende doen:

 - U beschikt over de update of de hotfix is lokaal op uw host gedownload of toegankelijk via een netwerk share.
 - In uw virtuele matrix wordt Update 0,6 (10.0.10293.0) uitgevoerd. Als u een versie uitvoert vóór update 0,6, installeert u eerst [update 0,6](storsimple-virtual-array-install-update-06.md) en installeert u vervolgens Update 1.

Deze procedure duurt circa 4 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of hotfix installeren

1. Ga in de lokale web-UI naar **onderhouds**  >  **Software-update**. Noteer de software versie die u uitvoert. **Ga alleen verder met deze update als u update 0,6 (10.0.10293.0) uitvoert. Als u een eerdere versie gebruikt, installeert u eerst [update 0,6](storsimple-virtual-array-install-update-06.md) op uw apparaat en past u vervolgens Update 1 toe.**
   
    ![Scherm afbeelding toont software-update die is geselecteerd in het menu onderhoud.](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. In **pad van update bestand** voert u de bestands naam in voor de update of de hotfix. U kunt ook naar het installatie bestand van de update of hotfix bladeren als u dit op een netwerk share plaatst. Klik op **Toepassen**.
   
    ![Scherm afbeelding toont de huidige software versie en het tekstvak pad naar het update bestand op de pagina software-update.](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Er wordt een waarschuwing weer gegeven. Gezien de virtuele matrix is een apparaat met één knoop punt, nadat de update is toegepast, wordt het apparaat opnieuw opgestart en is er downtime. Klik op het vinkje.
   
   ![Scherm afbeelding toont een dialoog venster waarschuwing over uitval tijd.](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikers interface is niet toegankelijk in deze duur.
   
    ![Scherm afbeelding toont een geslaagd bericht voor de update.](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Wanneer het opnieuw opstarten is voltooid, wordt u naar de **aanmeldings** pagina geleid. Als u wilt controleren of de software van het apparaat is bijgewerkt, gaat u naar **onderhouds**  >  **Software-update** in de lokale web-UI. De weer gegeven software versie moet **10.0.0.0.0.10296** zijn voor update 1,0.
   
   > [!NOTE]
   > De software versies worden op een iets andere manier gerapporteerd in de lokale web-UI en de Azure Portal. De lokale web-UI rapporteert bijvoorbeeld **10.0.0.0.0.10296** en de Azure Portal rapport **10.0.10296.0** voor dezelfde versie.
   
    ![Scherm afbeelding toont de software-update pagina met de huidige software versie.](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Herhaal stap 2-4 om de Windows-beveiligings oplossing met behulp van bestand te installeren `windows8.1-kb4012213-x64` . De virtuele matrix wordt opnieuw opgestart na de installatie en u moet zich aanmelden bij de lokale webgebruikersinterface.

> [!NOTE]
> Als u update 1 rechtstreeks hebt toegepast op een apparaat met een versie die ouder is dan 0,6, ontbreken er updates. Neem contact op met Microsoft Ondersteuning voor de volgende stappen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).
