---
title: Zelf studie-een failover van het fysieke StorSimple-apparaat naar hetzelfde apparaat uitvoeren
description: Meer informatie over de stappen die nodig zijn voor het uitvoeren van een failover naar een fysiek StorSimple 8000-serie apparaat als er sprake is van een nood geval.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017097"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Failover van het fysieke StorSimple-apparaat naar hetzelfde apparaat

## <a name="overview"></a>Overzicht

In deze zelf studie worden de stappen beschreven die nodig zijn voor het uitvoeren van een failover van een StorSimple-fysieke apparaat in een 8000-serie als er een nood geval is. StorSimple maakt gebruik van de functie Failover van het apparaat om gegevens te migreren van een fysiek bron apparaat in het Data Center naar een ander fysiek apparaat. De richt lijnen in deze zelf studie zijn van toepassing op fysieke apparaten uit de 8000 StorSimple-serie met software versies update 3 en hoger.

Voor meer informatie over failover van apparaten en hoe deze wordt gebruikt om een nood geval te herstellen, gaat u naar [failover en herstel na nood gevallen voor apparaten uit de StorSimple-serie van 8000](storsimple-8000-device-failover-disaster-recovery.md).

Als u een failover wilt uitvoeren voor een fysiek apparaat naar een ander fysiek apparaat, gaat u naar een [failover naar hetzelfde fysieke StorSimple-apparaat](storsimple-8000-device-failover-physical-device.md). Als u een failover wilt uitvoeren voor een StorSimple-fysiek apparaat naar een StorSimple Cloud Appliance, gaat u naar [een failover naar een StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de overwegingen voor failover van het apparaat hebt gecontroleerd. Ga voor meer informatie naar [algemene overwegingen voor failover van het apparaat](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Stappen voor het uitvoeren van een failover naar hetzelfde apparaat

Voer de volgende stappen uit als u een failover naar hetzelfde apparaat moet uitvoeren.

1. Maak Cloud momentopnamen van alle volumes op het apparaat. Ga voor meer informatie naar [gebruik StorSimple Apparaatbeheer service om back-ups te maken](storsimple-8000-manage-backup-policies-u2.md).
2. Stel de fabrieks instellingen van uw apparaat opnieuw in. Volg de gedetailleerde instructies voor het [opnieuw instellen van een StorSimple-apparaat op de standaard fabrieks instellingen](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Ga naar de StorSimple-Apparaatbeheer service en selecteer vervolgens **apparaten**. Op de Blade **apparaten** moet het oude apparaat als **offline** worden weer gegeven.

    ![Bron apparaat is offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configureer uw apparaat en registreer het opnieuw met uw StorSimple-Apparaatbeheer service. Het nieuwe geregistreerde apparaat moet zo worden weer gegeven **dat het kan worden ingesteld**. De apparaatnaam voor het nieuwe apparaat is hetzelfde als het oude apparaat, maar toegevoegd met een cijfer om aan te geven dat het apparaat opnieuw is ingesteld op de standaard fabriek en opnieuw wordt geregistreerd.

    ![Nieuw geregistreerd apparaat gereed voor configuratie](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Voor het nieuwe apparaat voert u de installatie van het apparaat uit. Ga voor meer informatie naar [stap 4: minimale installatie van het apparaat volt ooien](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Op de Blade **apparaten** wordt de status van het apparaat gewijzigd in **online**.

   > [!IMPORTANT]
   > **Voltooi eerst de minimale configuratie of uw DR kan mislukken.**

    ![Nieuw geregistreerd apparaat online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecteer het oude apparaat (status offline) en klik op de opdracht balk op **failover**. Selecteer in de Blade **failover** een oud apparaat als bron en geef het doel apparaat op als het nieuwe geregistreerde apparaat.

    ![Overzicht van failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Voor gedetailleerde instructies raadpleegt u failover naar een ander fysiek apparaat.

7. Er wordt een taak voor het herstellen van een apparaat gemaakt die u kunt bewaken vanuit de Blade **taken** .

8. Nadat de taak is voltooid, opent u het nieuwe apparaat en gaat u naar de Blade **volume containers** . Controleer of alle volume containers van het oude apparaat naar het nieuwe apparaat zijn gemigreerd.

   ![Gemigreerde volume containers](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Nadat de failover is voltooid, kunt u het oude apparaat deactiveren en verwijderen uit de portal. Selecteer het oude apparaat (offline), klik met de rechter muisknop en selecteer **deactiveren**. Nadat het apparaat is gedeactiveerd, wordt de status van het apparaat bijgewerkt.

     ![Bron apparaat gedeactiveerd](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecteer het gedeactiveerde apparaat, klik met de rechter muisknop en selecteer **verwijderen**. Hiermee verwijdert u het apparaat uit de lijst met apparaten.

    ![Bron apparaat is verwijderd](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Volgende stappen

* Nadat u een failover hebt uitgevoerd, moet u mogelijk [uw StorSimple-apparaat deactiveren of verwijderen](storsimple-8000-deactivate-and-delete-device.md).
* Voor informatie over het gebruik van de StorSimple Apparaatbeheer-service gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).

