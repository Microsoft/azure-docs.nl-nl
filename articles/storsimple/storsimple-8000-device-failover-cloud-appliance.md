---
title: Failover en herstel na nood gevallen naar een StorSimple Cloud Appliance
description: Meer informatie over het uitvoeren van een failover van het fysieke apparaat van uw StorSimple 8000-serie naar een Cloud apparaat.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91774457"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Failover naar uw StorSimple Cloud Appliance

## <a name="overview"></a>Overzicht

In deze zelf studie worden de stappen beschreven die nodig zijn voor het uitvoeren van een failover van een StorSimple 8000-serie een fysiek apparaat naar een StorSimple Cloud Appliance als er een nood geval is. StorSimple maakt gebruik van de functie Failover van het apparaat om gegevens te migreren van een fysiek bron apparaat in het Data Center naar een Cloud apparaat dat wordt uitgevoerd in Azure. De richt lijnen in deze zelf studie zijn van toepassing op fysieke apparaten van de StorSimple 8000-serie en Cloud apparaten met software versies update 3 en hoger.

Voor meer informatie over failover van apparaten en hoe deze wordt gebruikt om een nood geval te herstellen, gaat u naar [failover en herstel na nood gevallen voor apparaten uit de StorSimple-serie van 8000](storsimple-8000-device-failover-disaster-recovery.md).

Als u een failover wilt uitvoeren op een fysiek StorSimple-apparaat naar een ander fysiek apparaat, gaat u naar [een failover naar een fysiek StorSimple-apparaat](storsimple-8000-device-failover-physical-device.md). Als u een failover wilt uitvoeren voor een apparaat naar zichzelf, gaat u naar [failover naar hetzelfde fysieke StorSimple-apparaat](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de overwegingen voor failover van het apparaat hebt gecontroleerd. Ga voor meer informatie naar [algemene overwegingen voor failover van het apparaat](storsimple-8000-device-failover-disaster-recovery.md).

- U moet een StorSimple Cloud Appliance hebben gemaakt en geconfigureerd voordat u deze procedure uitvoert. Als u update 3-software versie of hoger uitvoert, kunt u overwegen een 8020-Cloud apparaat te gebruiken voor de DR. Het 8020-model heeft 64 TB en maakt gebruik van Premium Storage. Ga voor meer informatie naar [implementeren en beheren van een StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Stappen voor het uitvoeren van een failover naar een Cloud apparaat

Voer de volgende stappen uit om het apparaat terug te zetten naar een doel StorSimple Cloud Appliance.

1.  Controleer of de volume container waarnaar u een failover wilt uitvoeren, gekoppelde cloud momentopnamen heeft. Ga voor meer informatie naar [gebruik StorSimple Apparaatbeheer service om back-ups te maken](storsimple-8000-manage-backup-policies-u2.md).
2. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Ga op de Blade **apparaten** naar de lijst met apparaten die zijn verbonden met uw service.
    ![Apparaat selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecteer en klik op uw bron apparaat. Het bron apparaat heeft de volume containers waarvoor u een failover wilt uitvoeren. Ga naar **instellingen > volume containers**.

    ![Apparaat 2 selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecteer een volume container waarvoor u een failover wilt uitvoeren naar een ander apparaat. Klik op de volume container om de lijst met volumes in deze container weer te geven. Selecteer een volume, klik met de rechter muisknop en klik op **offline nemen** om het volume offline te halen.

    ![Apparaat selecteren 3](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Herhaal dit proces voor alle volumes in de volume container.

     ![Apparaat selecteren 4](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Herhaal de vorige stap voor alle volume containers waarvoor u een failover wilt uitvoeren naar een ander apparaat.

7. Ga terug naar de Blade **apparaten** . Klik op de opdracht balk op **failover**.

    ![Klik op failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Voer de volgende stappen uit op de Blade **failover** :
   
    1. Klik op **bron**. Selecteer de volume containers waarvoor een failover moet worden uitgevoerd. **Alleen de volume containers met gekoppelde cloud momentopnamen en offline volumes worden weer gegeven.**
        ![Bron selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Klik op **doel**. Selecteer een doel-Cloud apparaat in de vervolg keuzelijst met beschik bare apparaten. **Alleen de apparaten die voldoende capaciteit hebben om te voldoen aan de bron volume containers, worden weer gegeven in de lijst.**

        ![Doel selecteren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Controleer de failover-instellingen onder **samen vatting** en schakel het selectie vakje in om aan te geven dat de volumes in de geselecteerde volume containers offline zijn. 

        ![Failover-instellingen controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Er wordt een failover-taak gemaakt. Als u de failover-taak wilt controleren, klikt u op de taak melding.

    ![Failover-taak controleren](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Nadat de failover is voltooid, gaat u terug naar de Blade **apparaten** .

    1. Selecteer het apparaat dat is gebruikt als doel voor de failover.

       ![Apparaat selecteren 5](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Klik op **volume containers**. Alle volume containers, samen met de volumes van het oude apparaat, moeten worden weer gegeven.

       Als de volume container waarvoor u een failover hebt uitgevoerd, lokaal vastgemaakte volumes heeft, worden deze volumes failover als gelaagde volumes. Lokaal vastgemaakte volumes worden niet ondersteund op een StorSimple Cloud Appliance.

       ![Doel volume containers weer geven](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Volgende stappen

* Nadat u een failover hebt uitgevoerd, moet u mogelijk [uw StorSimple-apparaat deactiveren of verwijderen](storsimple-8000-deactivate-and-delete-device.md).

* Voor informatie over het gebruik van de StorSimple Apparaatbeheer-service gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).

