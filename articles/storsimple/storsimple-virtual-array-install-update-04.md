---
title: Updates installeren op de virtuele StorSimple-matrix | Microsoft Docs
description: Hierin wordt beschreven hoe u de Web-UI van StorSimple Virtual array gebruikt om update 0,4 toe te passen met behulp van de methode Azure Portal en Hot Fix.
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: cc458f91a9b67ddd54e706575d29b8fc8618a2f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534293"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Update 0,4 installeren op de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In dit artikel worden de stappen beschreven die nodig zijn om update 0,4 te installeren op uw virtuele StorSimple-matrix via de lokale webgebruikersinterface en via de Azure Portal. U moet software-updates of hotfixes Toep assen om uw virtuele StorSimple-matrix up-to-date te houden. 

Als u een update of hotfix installeert, wordt het apparaat opnieuw opgestart. Gezien de StorSimple virtuele matrix is een apparaat met één knoop punt, worden alle I/O-bewerkingen onderbroken en wordt de uitval tijd van uw apparaat verstoord. 

Voordat u een update toepast, raden we u aan om eerst de volumes of shares op de host offline te zetten en vervolgens op het apparaat. Dit minimaliseert de kans op beschadigde gegevens.

> [!IMPORTANT]
> Als u update 0,1 of GA software versies uitvoert, moet u de hotfix-methode gebruiken via de lokale web-UI om update 0,3 te installeren. Als u update 0,2 of hoger uitvoert, wordt u aangeraden de updates te installeren via de Azure Portal.
 

## <a name="use-the-local-web-ui"></a>De lokale web-UI gebruiken

Er zijn twee stappen voor het gebruik van de lokale web-UI:

* De update of hotfix downloaden
* De update of hotfix installeren

### <a name="download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

Voer de volgende stappen uit om de software-update te downloaden uit de Microsoft Update-catalogus.

#### <a name="to-download-the-update-or-the-hotfix"></a>De update of hotfix downloaden

1. Start Internet Explorer en ga naar [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Als dit de eerste keer is dat u de Microsoft Update-catalogus op deze computer gebruikt, klikt u op **Installeren** wanneer u wordt gevraagd of u de invoegtoepassing voor de Microsoft Update-catalogus wilt installeren.

3. Voer in het zoekvak van de catalogus van de Microsoft Update het Knowledge Base-nummer (KB) in van de hotfix die u wilt downloaden. Voer **3216577** in bij Update 0,4 en klik vervolgens op **zoeken**.
   
    De hotfix-vermelding wordt weer gegeven, bijvoorbeeld **StorSimple Virtual array Update 0,4**.
   
    ![Catalogus doorzoeken](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klik op **Add**. De update wordt toegevoegd aan het mandje.

5. Klik op **Mandje weergeven**.

6. Klik op **Downloaden**. Typ of **blader naar** een lokale locatie waar u de downloads wilt weergeven. De updates worden naar de opgegeven locatie gedownload en in een submap met dezelfde naam als de update geplaatst. De map kan ook worden gekopieerd naar een netwerkshare die bereikbaar is vanaf het apparaat.

7. De gekopieerde map opent, ziet u een Microsoft Update zelfstandig pakket bestand `WindowsTH-KB3011067-x64` . Dit bestand wordt gebruikt om de update of hotfix te installeren.

### <a name="install-the-update-or-the-hotfix"></a>De update of hotfix installeren

Voordat u de update of hotfix installeert, moet u ervoor zorgen dat de update of de hotfix lokaal op uw host is gedownload of toegankelijk is via een netwerk share. 

Gebruik deze methode om updates te installeren op een apparaat met GA of update 0,1-software versies. Het volt ooien van deze procedure duurt minder dan twee minuten. Voer de volgende stappen uit om de update of hotfix te installeren.

#### <a name="to-install-the-update-or-the-hotfix"></a>De update of hotfix installeren

1. Ga in de lokale web-UI naar **onderhouds**  >  **Software-update**.
   
    ![Scherm afbeelding toont software-update die is geselecteerd in het menu onderhoud.](./media/storsimple-virtual-array-install-update/update1m.png)

2. In **pad van update bestand** voert u de bestands naam in voor de update of de hotfix. U kunt ook naar het installatie bestand van de update of hotfix bladeren als u dit op een netwerk share plaatst. Klik op **Toepassen**.
   
    ![Scherm afbeelding toont het tekstvak pad van het bestand bijwerken op de pagina software-update.](./media/storsimple-virtual-array-install-update/update2m.png)

3. Er wordt een waarschuwing weer gegeven. Gezien dit is een apparaat met één knoop punt, nadat de update is toegepast, het apparaat opnieuw wordt opgestart en er uitval tijd is. Klik op het vinkje.
   
   ![Scherm afbeelding toont een dialoog venster waarschuwing over uitval tijd.](./media/storsimple-virtual-array-install-update/update3m.png)

4. De update wordt gestart. Nadat het apparaat is bijgewerkt, wordt het opnieuw opgestart. De lokale gebruikers interface is niet toegankelijk in deze duur.
   
    ![Scherm afbeelding toont een geslaagd bericht voor de update.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Wanneer het opnieuw opstarten is voltooid, wordt u naar de **aanmeldings** pagina geleid. Als u wilt controleren of de software van het apparaat is bijgewerkt, gaat u naar **onderhouds**  >  **Software-update** in de lokale web-UI. De weer gegeven software versie moet **10.0.0.0.0.10289.0** zijn voor Update 0,4.
   
   > [!NOTE]
   > De software versies worden op een iets andere manier gerapporteerd in de lokale web-UI en de Azure Portal. De lokale web-UI rapporteert bijvoorbeeld **10.0.0.0.0.10289** en de Azure Portal rapport **10.0.10289.0** voor dezelfde versie.
   
    ![Scherm afbeelding toont de software-update pagina met de huidige software versie.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u update 0,2 en hoger uitvoert, wordt u aangeraden updates te installeren via de Azure Portal. Voor de portal procedure moet de gebruiker de updates scannen, downloaden en installeren. Deze procedure duurt circa 7 minuten om te volt ooien. Voer de volgende stappen uit om de update of hotfix te installeren.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Nadat de installatie is voltooid (zoals aangegeven door de taak status bij 100%), gaat u naar de StorSimple-Apparaatbeheer service. Selecteer **apparaten** en selecteer en klik vervolgens op het apparaat dat u wilt bijwerken in de lijst met apparaten die zijn verbonden met deze service. Ga op de Blade **instellingen** naar de sectie **beheren** en selecteer **updates voor apparaten**. De weer gegeven software versie moet **10.0.10289.0** zijn.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).

