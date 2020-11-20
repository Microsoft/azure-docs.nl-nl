---
title: Microsoft Azure StorSimple zelf studie over het maken van een virtuele matrix | Microsoft Docs
description: Maak geplande en hand matige back-ups voor Microsoft Azure StorSimple virtuele matrix en voer herstel op item niveau uit om een verwijderd bestand in uw virtuele matrix te herstellen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99c7f4ab92e4cce3937a933fec97008c281a0eae
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954117"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Maak een back-up van shares of volumes op uw virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

De virtuele StorSimple-matrix is een Hybrid Cloud Storage on-premises virtueel apparaat dat kan worden geconfigureerd als een bestands server of een iSCSI-server. Met de virtuele matrix kan de gebruiker geplande en hand matige back-ups maken van alle shares of volumes op het apparaat. Wanneer het is geconfigureerd als een bestands server, kan er ook herstel op item niveau worden ingesteld. In deze zelf studie wordt beschreven hoe u geplande en hand matige back-ups maakt en herstel op item niveau uitvoert om een verwijderd bestand in uw virtuele matrix te herstellen.

Deze zelf studie is alleen van toepassing op de virtuele StorSimple-matrices. Ga voor meer informatie over de 8000-serie naar [een back-up maken voor een 8000-serie apparaat](./storsimple-8000-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Back-ups maken van shares en volumes

Back-ups bieden tijdgebonden beveiliging, het verbeteren van herstel baarheid en het minimaliseren van de terugzet tijden voor shares en volumes. U kunt op twee manieren een back-up maken van een share of een volume op uw StorSimple-apparaat: **gepland** of **hand matig**. Elk van de methoden wordt in de volgende secties besproken.

## <a name="change-the-backup-start-time"></a>De begin tijd van de back-up wijzigen

> [!NOTE]
> In deze release worden geplande back-ups gemaakt met een standaard beleid dat dagelijks op een opgegeven tijd wordt uitgevoerd en een back-up maakt van alle shares of volumes op het apparaat. Het is momenteel niet mogelijk om op dit moment aangepaste beleids regels te maken voor geplande back-ups.


De virtuele StorSimple-matrix bevat een standaard back-upbeleid dat begint op een opgegeven tijdstip (22:30) en een back-up maakt van alle shares of volumes op het apparaat één keer per dag. U kunt de tijd wijzigen waarop de back-up wordt gestart, maar de frequentie en de retentie (waarmee het aantal te bewaren back-ups wordt opgegeven) kan niet worden gewijzigd. Tijdens deze back-ups wordt er een back-up van het hele virtuele apparaat gemaakt. Dit kan mogelijk invloed hebben op de prestaties van het apparaat en van invloed zijn op de werk belastingen die op het apparaat zijn geïmplementeerd. Daarom wordt u aangeraden deze back-ups te plannen voor daluren.

 Als u de standaard start tijd van de back-up wilt wijzigen, voert u de volgende stappen uit in de [Azure Portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>De start tijd voor het standaard back-upbeleid wijzigen

1. Ga naar **apparaten**. De lijst met apparaten die zijn geregistreerd bij uw StorSimple Apparaatbeheer service worden weer gegeven. 
   
    ![naar apparaten navigeren](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecteer en klik op uw apparaat. De Blade **instellingen** wordt weer gegeven. Ga naar **> back-upbeleid beheren**.
   
    ![uw apparaat selecteren](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Op de Blade **back-upbeleid** is de standaard start tijd 22:30. U kunt de nieuwe begin tijd voor de dagelijkse planning in de tijd zone van het apparaat opgeven.
   
    ![navigeren naar back-upbeleid](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klik op **Opslaan**.

### <a name="take-a-manual-backup"></a>Een hand matige back-up maken

Naast geplande back-ups kunt u op elk gewenst moment een hand matige back-up (op aanvraag) maken van apparaatgegevens.

#### <a name="to-create-a-manual-backup"></a>Een handmatige back-up maken

1. Ga naar **apparaten**. Selecteer uw apparaat en klik met de rechter **muisknop helemaal rechts** in de geselecteerde rij. Selecteer **back-up maken** in het context menu.
   
    ![navigeren om back-up te maken](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Klik in de Blade **back-up** maken op **back-up** maken. Hiermee maakt u een back-up van alle shares op de bestands server of alle volumes op uw iSCSI-server. 
   
    ![back-up starten](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Er wordt een back-up op aanvraag gestart en u ziet dat er een back-uptaak is gestart.
   
    ![back-up starten 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Zodra de taak is voltooid, wordt u opnieuw op de hoogte gebracht. Het back-upproces wordt vervolgens gestart.
   
    ![back-uptaak gemaakt](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Als u de voortgang van de back-ups wilt bijhouden en de taak details wilt bekijken, klikt u op de melding. Hiermee gaat u naar  **taak Details**.
   
     ![Details van back-uptaak](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Nadat de back-up is voltooid, gaat u naar **beheer > backup Catalog**. Er wordt een Cloud momentopname van alle shares (of volumes) op het apparaat weer geven.
   
    ![Volledige back-up](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Bestaande back-ups weer geven
Als u de bestaande back-ups wilt weer geven, voert u de volgende stappen uit in de Azure Portal.

#### <a name="to-view-existing-backups"></a>Bestaande back-ups weer geven

1. Ga naar de Blade **apparaten** . Selecteer en klik op uw apparaat. Ga op de Blade **instellingen** naar **beheer > backup Catalog**.
   
    ![Navigeren naar back-upcatalogus](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Geef de volgende criteria op die moeten worden gebruikt voor het filteren:
   
   - **Tijds bereik** : kan **na 1 uur**, **afgelopen 24 uur**, **afgelopen 7 dagen**, **afgelopen 30 dagen**, **afgelopen jaar** en **aangepaste datum** zijn.
    
   - **Apparaten** : Selecteer in de lijst met bestands servers of iSCSI-servers die zijn geregistreerd bij uw StorSimple-Apparaatbeheer service.
   
   - **Gestart** : kan automatisch worden **gepland** (door middel van een back-upbeleid) of **hand matig** worden geïnitieerd (door u).
   
     ![Back-ups filteren](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klik op **Toepassen**. De gefilterde lijst met back-ups wordt weer gegeven op de Blade **back-upcatalogus** . Opmerking alleen 100 back-upelementen kunnen op een bepaald moment worden weer gegeven.
   
    ![Bijgewerkte back-upcatalogus](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw StorSimple Virtual array](storsimple-ova-web-ui-admin.md).