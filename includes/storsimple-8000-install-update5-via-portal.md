---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9832a961562930e61e94b28ed78976fd9f8c6f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376010"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Een update installeren via Azure Portal

1. Selecteer uw apparaat op de pagina van de StorSimple-service.

    ![Apparaat selecteren](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Navigeren naar **Apparaatinstellingen apparaat-**  >  **updates**.

    ![Klik op updates voor apparaten](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Er wordt een melding weer gegeven als er nieuwe updates beschikbaar zijn. U kunt ook op de Blade **updates voor apparaten** klikken op **updates scannen**. Er wordt een taak gemaakt om te zoeken naar beschikbare updates. U ontvangt een melding wanneer de taak is voltooid.

    ![Klik op updates voor apparaten 2](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. We raden u aan de releaseopmerkingen te bekijken voordat u een update toepast op uw apparaat. Klik op **updates installeren** om updates toe te passen. Controleer op de Blade **regel matige updates bevestigen** de vereiste onderdelen om te volt ooien voordat u updates toepast. Schakel het selectie vakje in om aan te geven dat u klaar bent voor het bijwerken van het apparaat en klik vervolgens op **installeren**.

    ![Klik op updates 3](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Er wordt een reeks vereiste controles gestart. Deze controles zijn onder andere:
   
   * **Statuscontroles controllers** om te controleren of beide apparaatcontrollers in orde en online zijn.
   * **Statuscontroles hardwareonderdelen** om te controleren of alle hardwareonderdelen op uw StorSimple-apparaat in orde zijn.
   * **DATA 0-controles** om te controleren of DATA 0 is ingeschakeld op uw apparaat. Als deze interface niet is ingeschakeld, schakelt u deze in en probeert u het vervolgens opnieuw.

     De update wordt alleen gedownload en geïnstalleerd als alle controles zijn voltooid. U krijgt een melding wanneer de controles worden uitgevoerd. Als de voor controle mislukt, dan worden de redenen voor de fout weer gegeven. Los deze problemen op en voer de bewerking opnieuw uit. Mogelijk moet u contact opnemen met Microsoft-ondersteuning als u deze problemen niet zelf kunt oplossen.

7. Nadat de voor controle is voltooid, wordt een update taak gemaakt. U krijgt een melding wanneer de bijwerktaak is gemaakt.
   
    ![Maken van bijwerktaak](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    De update wordt vervolgens op het apparaat toegepast.

9. De update neemt enkele uren in beslag. Selecteer de bijwerktaak en klik op **Details** om de details van de taak te bekijken.

    ![Taak maken 2 bijwerken](./media/storsimple-8000-install-update5-via-portal/update8.png)

     U kunt ook de voortgang van de update taak controleren op basis van **apparaatinstellingen > taken**. Op de Blade **taken** ziet u de voortgang van de update.

     ![Taak voor het maken van updates 3](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Nadat de taak is voltooid, gaat u naar de **apparaatinstellingen > updates** van het apparaat. De software versie moet nu worden bijgewerkt.

