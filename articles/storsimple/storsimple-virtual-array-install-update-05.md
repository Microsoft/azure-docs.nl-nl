---
title: Update 0,5 op StorSimple Virtual array installeren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Web-UI van StorSimple Virtual array gebruikt om update 0,5 toe te passen met behulp van de methode Azure Portal en Hot Fix.
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: 5723e8d9fc7b0a72393dda1b225ca073a6474a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534310"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Update 0,5 installeren op de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om update 0,5 te installeren op uw virtuele StorSimple-matrix via de lokale webgebruikersinterface en via de Azure Portal. U moet software-updates of hotfixes Toep assen om uw virtuele StorSimple-matrix up-to-date te houden.

Voordat u een update toepast, raden we u aan om eerst de volumes of shares op de host offline te zetten en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens. Nadat de volumes of shares offline zijn, moet u ook een hand matige back-up van het apparaat maken.

> [!IMPORTANT]
> - Update 0,5 komt overeen met de **10.0.10290.0** -software versie op het apparaat. Voor informatie over wat er nieuw is in deze update gaat u naar [release opmerkingen voor update 0,5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Als u update 0,2 of hoger uitvoert, wordt u aangeraden de updates te installeren via de Azure Portal. Als u update 0,1 of GA software versies uitvoert, moet u de hotfix-methode gebruiken via de lokale web-UI om update 0,5 te installeren.
>
> - Als u een update of hotfix installeert, wordt het apparaat opnieuw opgestart. Gezien de StorSimple virtuele matrix is een apparaat met één knoop punt, worden alle I/O-bewerkingen onderbroken en wordt de uitval tijd van uw apparaat verstoord.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u update 0,2 en hoger uitvoert, wordt u aangeraden updates te installeren via de Azure Portal. Voor de portal procedure moet de gebruiker de updates scannen, downloaden en installeren. Deze procedure duurt circa 7 minuten om te volt ooien. Voer de volgende stappen uit om de update of hotfix te installeren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Nadat de installatie is voltooid, gaat u naar de StorSimple-Apparaatbeheer service. Selecteer **apparaten** en selecteer en klik vervolgens op het apparaat dat u zojuist hebt bijgewerkt. Ga naar **instellingen > > updates** van het apparaat te beheren. De weer gegeven software versie moet **10.0.10290.0** zijn.

## <a name="use-the-local-web-ui"></a>De lokale web-UI gebruiken

Er zijn twee stappen voor het gebruik van de lokale web-UI:

* De update of hotfix downloaden
* De update of hotfix installeren

### <a name="download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

Voer de volgende stappen uit om de software-update te downloaden uit de Microsoft Update-catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

1. Start Internet Explorer en ga naar [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Als dit de eerste keer is dat u de Microsoft Update-catalogus op deze computer gebruikt, klikt u op **Installeren** wanneer u wordt gevraagd of u de invoegtoepassing voor de Microsoft Update-catalogus wilt installeren.

3. Voer in het zoekvak van de catalogus van de Microsoft Update het Knowledge Base-nummer (KB) in van de hotfix die u wilt downloaden. Voer **4021576** in bij Update 0,5 en klik vervolgens op **zoeken**.
   
    De hotfix-vermelding wordt weer gegeven, bijvoorbeeld **StorSimple Virtual array Update 0,5**.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Klik op **Downloaden**. 

5. Als het goed is, ziet u dat er twee bestanden moeten worden gedownload: een *. MSU* en een *cab* -bestand. Down load elk van deze bestanden naar een map. De map kan ook worden gekopieerd naar een netwerkshare die bereikbaar is vanaf het apparaat.

6. Open de map waarin de bestanden zich bevinden.
    ![Bestanden in het pakket](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    U ziet:
    -  Een Microsoft Update zelfstandig pakket bestand `WindowsTH-KB3011067-x64` . Dit bestand wordt gebruikt om de software van het apparaat bij te werken.
    - Een Genève Monitoring Agent-pakket bestand `GenevaMonitoringAgentPackageInstaller` . Dit bestand wordt gebruikt voor het bijwerken van de MDS-agent (bewaking en diagnose service). Dubbel klik op het CAB-bestand. Er wordt een. msi-bestand weer gegeven. Selecteer het bestand, klik met de rechter muisknop en **pak** het bestand uit. Het _MSI_ -bestand wordt gebruikt om de agent bij te werken.

        ![MDS-agent update bestand uitpakken](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>De update of hotfix installeren

Voordat u de update of hotfix installeert, moet u ervoor zorgen dat de update of de hotfix lokaal op uw host is gedownload of toegankelijk is via een netwerk share.

Gebruik deze methode om updates te installeren op een apparaat met GA of update 0,1-software versies. Het volt ooien van deze procedure duurt minder dan twee minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of hotfix installeren

1. Ga in de lokale web-UI naar **onderhouds**  >  **Software-update**.
   
    ![Scherm afbeelding toont software-update die is geselecteerd in het menu onderhoud.](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. In **pad van update bestand** voert u de bestands naam in voor de update of de hotfix. U kunt ook naar het installatie bestand van de update of hotfix bladeren als u dit op een netwerk share plaatst. Klik op **Toepassen**.
   
    ![Scherm afbeelding toont het tekstvak pad van het bestand bijwerken op de pagina software-update.](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Er wordt een waarschuwing weer gegeven. Gezien dit is een apparaat met één knoop punt, nadat de update is toegepast, het apparaat opnieuw wordt opgestart en er uitval tijd is. Klik op het vinkje.
   
   ![Scherm afbeelding toont een dialoog venster waarschuwing over uitval tijd.](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikers interface is niet toegankelijk in deze duur.
   
    ![Scherm afbeelding toont een geslaagd bericht voor de update.](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Wanneer het opnieuw opstarten is voltooid, wordt u naar de **aanmeldings** pagina geleid. Als u wilt controleren of de software van het apparaat is bijgewerkt, gaat u naar **onderhouds**  >  **Software-update** in de lokale web-UI. De weer gegeven software versie moet **10.0.0.0.0.10290.0** zijn voor Update 0,5.
   
   > [!NOTE]
   > De software versies worden op een iets andere manier gerapporteerd in de lokale web-UI en de Azure Portal. De lokale web-UI rapporteert bijvoorbeeld **10.0.0.0.0.10290** en de Azure Portal rapport **10.0.10290.0** voor dezelfde versie.
   
    ![Scherm afbeelding toont de software-update pagina met de huidige software versie.](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. De volgende stap is het bijwerken van de MDS-agent. Ga op de pagina **Software-update** naar het pad naar het **update bestand** en blader naar het `GenevaMonitoringAgentPackageInstaller.msi` bestand. Herhaal stap 2-4. Nadat de virtuele matrix opnieuw is opgestart, meldt u zich aan bij de lokale web-UI.

De update is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).

