---
title: Update 0,6 op StorSimple Virtual array installeren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Web-UI van StorSimple Virtual array gebruikt om update 0,6 toe te passen met behulp van de methode Azure Portal en Hot Fix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 4b379bb76eaf7587c53ec55972ccee3f54a55f22
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94534242"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Update 0,6 installeren op de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om update 0,6 te installeren op uw virtuele StorSimple-matrix via de lokale webgebruikersinterface en via de Azure Portal. U past de software-updates of hotfixes toe om uw virtuele StorSimple-matrix up-to-date te houden.

Voordat u een update toepast, raden we u aan om eerst de volumes of shares op de host offline te zetten en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens. Nadat de volumes of shares offline zijn, moet u ook een hand matige back-up van het apparaat maken.

> [!IMPORTANT]
>
> - Update 0,6 komt overeen met de **10.0.10293.0** -software versie op het apparaat. Voor informatie over wat er nieuw is in deze update gaat u naar [release opmerkingen voor update 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Als u update 0,2 of hoger uitvoert, wordt u aangeraden de updates te installeren via de Azure Portal. Als u update 0,1 of GA software versies uitvoert, moet u de hotfix-methode gebruiken via de lokale web-UI om update 0,6 te installeren.
>
> - Als u een update of hotfix installeert, wordt het apparaat opnieuw opgestart. Gezien de StorSimple virtuele matrix is een apparaat met één knoop punt, worden alle I/O-bewerkingen onderbroken en wordt de uitval tijd van uw apparaat verstoord.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u update 0,2 en hoger uitvoert, wordt u aangeraden updates te installeren via de Azure Portal. Voor de portal procedure moet de gebruiker de updates scannen, downloaden en installeren. Deze procedure duurt circa 7 minuten om te volt ooien. Voer de volgende stappen uit om de update of hotfix te installeren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Nadat de installatie is voltooid, gaat u naar de StorSimple-Apparaatbeheer service. Selecteer **apparaten** en selecteer en klik vervolgens op het apparaat dat u zojuist hebt bijgewerkt. Ga naar **instellingen > > updates** van het apparaat te beheren. De weer gegeven software versie moet **10.0.10293.0** zijn.

## <a name="use-the-local-web-ui"></a>De lokale web-UI gebruiken

Er zijn twee stappen voor het gebruik van de lokale web-UI:

* De update of hotfix downloaden
* De update of hotfix installeren

### <a name="download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

Voer de volgende stappen uit om de software-update te downloaden uit de Microsoft Update-catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

1. Start Internet Explorer en ga naar [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Als u de Microsoft Update catalogus voor de eerste keer op deze computer gebruikt, klikt u op **installeren** wanneer u wordt gevraagd de invoeg toepassing Microsoft Update catalogus te installeren.

3. Voer in het zoekvak van de catalogus van de Microsoft Update het Knowledge Base-nummer (KB) in van de hotfix die u wilt downloaden. Voer **4023268** in bij Update 0,6 en klik vervolgens op **zoeken**.
   
    De hotfix-vermelding wordt weer gegeven, bijvoorbeeld **StorSimple Virtual array Update 0,6**.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klik op **Downloaden**.

5. Er moeten vijf bestanden worden gedownload. Down load elk van deze bestanden naar een map. De map kan ook worden gekopieerd naar een netwerkshare die bereikbaar is vanaf het apparaat.

6. Open de map waarin de bestanden zich bevinden.
    ![Bestanden in het pakket](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    U ziet:
    -  Een Microsoft Update zelfstandig pakket bestand `WindowsTH-KB3011067-x64` . Dit bestand wordt gebruikt om de software van het apparaat bij te werken.
    - Een Genève Monitoring Agent-pakket bestand `GenevaMonitoringAgentPackageInstaller` . Dit bestand wordt gebruikt voor het bijwerken van de MDS-agent (bewaking en diagnose service). Dubbel klik op het CAB-bestand. Er wordt een _. msi_ -bestand weer gegeven. Selecteer het bestand, klik met de rechter muisknop en **pak** het bestand uit. U gebruikt het _. msi_ -bestand om de agent bij te werken.

        ![MDS-agent update bestand uitpakken](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > U hoeft de MDS-agent niet bij te werken als u StorSimple Update 0,5 (0.0.10293.0) uitvoert.

    - Drie bestanden die essentiële Windows-beveiligings updates, `windows8.1-kb4012213-x64` , `windows8.1-kb3205400-x64` , en bevatten `windows8.1-kb4019213-x64` .


### <a name="install-the-update-or-the-hotfix"></a>De update of hotfix installeren

Voordat u de update of hotfix installeert, moet u ervoor zorgen dat de update of de hotfix lokaal op uw host is gedownload of toegankelijk is via een netwerk share.

Gebruik deze methode om updates te installeren op een apparaat met GA of update 0,1-software versies. Het volt ooien van deze procedure duurt ongeveer 12 minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of hotfix installeren

1. Ga in de lokale web-UI naar **onderhouds**  >  **Software-update**. Noteer de software versie die u uitvoert. Als u **10.0.10290.0** uitvoert, hoeft u de MDS-agent in stap 6 niet bij te werken.
   
    ![Scherm afbeelding toont software-update die is geselecteerd in het menu onderhoud.](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. In **pad van update bestand** voert u de bestands naam in voor de update of de hotfix. U kunt ook naar het installatie bestand van de update of hotfix bladeren als u dit op een netwerk share plaatst. Klik op **Toepassen**.
   
    ![Scherm afbeelding toont het tekstvak pad van het bestand bijwerken op de pagina software-update.](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Er wordt een waarschuwing weer gegeven. Gezien de virtuele matrix is een apparaat met één knoop punt, nadat de update is toegepast, wordt het apparaat opnieuw opgestart en is er downtime. Klik op het vinkje.
   
   ![Scherm afbeelding toont een dialoog venster waarschuwing over uitval tijd.](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikers interface is niet toegankelijk in deze duur.
   
    ![Scherm afbeelding toont een geslaagd bericht voor de update.](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Wanneer het opnieuw opstarten is voltooid, wordt u naar de **aanmeldings** pagina geleid. Als u wilt controleren of de software van het apparaat is bijgewerkt, gaat u naar **onderhouds**  >  **Software-update** in de lokale web-UI. De weer gegeven software versie moet **10.0.0.0.0.10293** zijn voor Update 0,6.
   
   > [!NOTE]
   > De software versies worden op een iets andere manier gerapporteerd in de lokale web-UI en de Azure Portal. De lokale web-UI rapporteert bijvoorbeeld **10.0.0.0.0.10293** en de Azure Portal rapport **10.0.10293.0** voor dezelfde versie.
   
    ![Scherm afbeelding toont de software-update pagina met de huidige software versie.](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Sla deze stap over als u StorSimple Virtual array Update 0,5 (**10.0.10290.0**) uitvoert voordat u deze update hebt toegepast. U hebt een opmerking ontvangen over de software versie in stap 1 voordat u begon met het bijwerken. Als u update 0,5 hebt uitgevoerd, is de MDS-agent al up-to-date.

    Als u een software versie uitvoert voordat u 0,5 bijwerkt, is de volgende stap voor u de MDS-agent bij te werken. Ga op de pagina **Software-update** naar het pad naar het **update bestand** en blader naar het `GenevaMonitoringAgentPackageInstaller.msi` bestand. Herhaal stap 2-4. Nadat de virtuele matrix opnieuw is opgestart, meldt u zich aan bij de lokale web-UI.

7. Herhaal stap 2-4 voor het installeren van de Windows-beveiligings oplossingen met behulp van bestanden `windows8.1-kb4012213-x64` , `windows8.1-kb3205400-x64` en `windows8.1-kb4019213-x64` . De virtuele matrix wordt na elke installatie opnieuw opgestart en u moet zich aanmelden bij de lokale webgebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).

