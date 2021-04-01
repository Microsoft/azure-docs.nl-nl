---
title: Failover, herstel na nood geval naar een ander StorSimple 8000-apparaat
description: Meer informatie over het uitvoeren van een failover van het fysieke apparaat van de StorSimple 8000-serie naar een ander fysiek apparaat.
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
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017131"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Failover naar een fysiek StorSimple-apparaat in de 8000-serie

## <a name="overview"></a>Overzicht

In deze zelf studie worden de stappen beschreven die nodig zijn voor het uitvoeren van een failover van een 8000 StorSimple-fysieke apparaat naar een ander StorSimple-fysieke apparaat als er sprake is van een nood geval. StorSimple maakt gebruik van de functie Failover van het apparaat om gegevens te migreren van een fysiek bron apparaat in het Data Center naar een ander fysiek apparaat. De richt lijnen in deze zelf studie zijn van toepassing op fysieke apparaten uit de 8000 StorSimple-serie met software versies update 3 en hoger.

Voor meer informatie over failover van apparaten en hoe deze wordt gebruikt om een nood geval te herstellen, gaat u naar [failover en herstel na nood gevallen voor apparaten uit de StorSimple-serie van 8000](storsimple-8000-device-failover-disaster-recovery.md).

Als u een failover wilt uitvoeren voor een StorSimple-fysiek apparaat naar een StorSimple Cloud Appliance, gaat u naar [een failover naar een StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Als u een failover wilt uitvoeren naar een fysiek apparaat, gaat u naar een [failover naar hetzelfde fysieke StorSimple-apparaat](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de overwegingen voor failover van het apparaat hebt gecontroleerd. Ga voor meer informatie naar [algemene overwegingen voor failover van het apparaat](storsimple-8000-device-failover-disaster-recovery.md).

- U moet een fysiek StorSimple 8000-serie apparaat hebben geïmplementeerd in het Data Center. Op het apparaat moet update 3 of hoger van de software versie worden uitgevoerd. Ga naar [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie.


## <a name="steps-to-fail-over-to-a-physical-device"></a>Stappen voor het uitvoeren van een failover naar een fysiek apparaat

Voer de volgende stappen uit om uw apparaat terug te zetten naar een fysiek doel apparaat.

1. Controleer of de volume container waarnaar u een failover wilt uitvoeren, gekoppelde cloud momentopnamen heeft. Ga voor meer informatie naar [gebruik StorSimple Apparaatbeheer service om back-ups te maken](storsimple-8000-manage-backup-policies-u2.md).
2. Ga naar uw StorSimple-Apparaatbeheer en klik vervolgens op **apparaten**. Ga op de Blade **apparaten** naar de lijst met apparaten die zijn verbonden met uw service.
    ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selecteer en klik op uw bron apparaat. Het bron apparaat heeft de volume containers waarvoor u een failover wilt uitvoeren. Ga naar **instellingen > volume containers**.
4. Selecteer een volume container waarvoor u een failover wilt uitvoeren naar een ander apparaat. Klik op de volume container om de lijst met volumes in deze container weer te geven. Selecteer een volume, klik met de rechter muisknop en klik op **offline nemen** om het volume offline te halen. Herhaal dit proces voor alle volumes in de volume container.
5. Herhaal de vorige stap voor alle volume containers waarvoor u een failover wilt uitvoeren naar een ander apparaat.
6. Ga terug naar de Blade **apparaten** . Klik op de opdracht balk op **failover**.
    ![Klik op failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Voer de volgende stappen uit op de Blade **failover** :
   
   1. Klik op **bron**. De volume containers met volumes die zijn gekoppeld aan Cloud momentopnamen worden weer gegeven. Alleen de weer gegeven containers komen in aanmerking voor failover. Selecteer in de lijst met volume containers de volume containers waarvoor u een failover wilt uitvoeren. **Alleen de volume containers met gekoppelde cloud momentopnamen en offline volumes worden weer gegeven.**

       ![Bron selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Klik op **doel**. Selecteer een doel apparaat in de vervolg keuzelijst met beschik bare apparaten voor de volume containers die u in de vorige stap hebt geselecteerd. Alleen de apparaten die voldoende capaciteit hebben om te voldoen aan de bron volume containers, worden weer gegeven in de lijst.

        ![Doel selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Controleer ten slotte alle failover-instellingen onder **samen vatting**. Nadat u de instellingen hebt gecontroleerd, schakelt u het selectie vakje in om aan te geven dat de volumes in de geselecteerde volume containers offline zijn. Klik op **OK**.

       ![Failover-instellingen controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple maakt een failover-taak. Klik op het taak bericht om de failover-taak te bewaken via de Blade **taken** .

    Als de volume container waarvoor u een failover hebt uitgevoerd lokale volumes heeft, ziet u afzonderlijke herstel taken voor elk lokaal volume (niet voor gelaagde volumes) in de container. Het kan enige tijd duren voordat deze herstel taken zijn voltooid. Het is waarschijnlijk dat de failover-taak eerder is voltooid. Deze volumes hebben alleen lokale garanties nadat de herstel taken zijn voltooid.

    ![Failover-taak controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Nadat de failover is voltooid, gaat u naar de Blade **apparaten** .
   
   1. Selecteer het apparaat dat is gebruikt als doel apparaat voor het failoverproces.

       ![Apparaat 2 selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Ga naar de Blade **volume containers** . Alle volume containers, samen met de volumes van het oude apparaat, moeten worden weer gegeven.

       ![Doel volume containers weer geven](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Volgende stappen

* Nadat u een failover hebt uitgevoerd, moet u mogelijk [uw StorSimple-apparaat deactiveren of verwijderen](storsimple-8000-deactivate-and-delete-device.md).
* Voor informatie over het gebruik van de StorSimple Apparaatbeheer-service gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).

