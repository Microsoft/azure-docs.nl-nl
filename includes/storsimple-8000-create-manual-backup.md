---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 89f7be31514f0b78c3bfb3efd6e6aca14658d5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93375986"
---
#### <a name="to-create-a-manual-backup"></a>Een handmatige back-up maken

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer uw apparaat in de lijst in tabelvorm met apparaten. Ga naar **Instellingen > Beheren > Back-upbeleid**.

2. Het tabblad **Back-upbeleid** bevat alle back-upbeleid in tabelvorm, met inbegrip van het beleid voor het volume waarvan u een back-up wilt maken. Selecteer het beleid dat is gekoppeld aan het volume waarvan u een back-up wilt maken en klik met de rechtermuisknop om het contextmenu aan te roepen. Selecteer **Nu back-up uitvoeren** in de vervolgkeuzelijst.

    ![Handmatige back-up maken](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. Voer de volgende stappen uit op de blade **Nu back-up uitvoeren**:

    1. Kies het juiste **Type momentopname** in de vervolgkeuzelijst: **Lokale momentopname** of **Cloud-momentopname**. Selecteer Lokale momentopname voor snelle back-ups of herstelacties en Cloud-momentopname voor tolerantie van gegevens.

        ![Hand matige back-up maken 2](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Klik op **OK** om een taak te starten voor het maken van een momentopname. U ziet boven aan de pagina een melding nadat de taak is gemaakt.

        ![Hand matige back-up maken 3](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Klik op de melding om de taak te controleren. U gaat nu naar de blade **Taken**, waar u de voortgang van de taak kunt bekijken.


5. Nadat de back-uptaak is voltooid, gaat u naar het tabblad **Back-upcatalogus**.

6. Stel de filterselecties in op het juiste apparaat, back-upbeleid en tijdsbereik. De back-up moet worden weergegeven in de lijst met back-upsets die in de catalogus wordt weergegeven.

