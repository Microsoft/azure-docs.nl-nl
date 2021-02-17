---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545296"
---
> [!NOTE] 
> U kunt de versleutelings instellingen en de referenties van het opslag account die zijn gekoppeld aan een volume container, niet wijzigen nadat deze is gemaakt.

#### <a name="to-modify-a-volume-container"></a>Een volume container wijzigen

1. Ga naar de StorSimple-Apparaatbeheer-service en navigeer vervolgens naar **beheer > volume containers**.

2. Selecteer in de lijst in tabel vorm van volume containers de volume container die u wilt wijzigen. Selecteer op de pagina **apparaten** het apparaat, dubbel klik erop en klik vervolgens op het tabblad **volume containers** .

3. Selecteer in de lijst in tabel vorm van de volume containers de volume container die u wilt wijzigen. Klik op de Blade die wordt geopend op **wijzigen** vanaf de opdracht balk.

    ![Volume container wijzigen](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. Voer de volgende stappen uit op de Blade **volume container wijzigen** :
   
   1. De naam, de versleutelings sleutel en het opslag account die aan de volume container zijn gekoppeld, kunnen niet meer worden gewijzigd nadat ze zijn opgegeven. Wijzig de instellingen voor de gekoppelde band breedte.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Bandbreedte-instelling wijzigen](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Klik op **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Op de volgende pagina van het dialoog venster **volume container wijzigen** :<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Kies in de vervolg keuzelijst een bestaande sjabloon voor de band breedte.
   1. Controleer de schema-instellingen voor de opgegeven sjabloon voor de band breedte.
   1. Klik op **Opslaan** en bevestig de wijzigingen.
      
       ![Wijzigingen bevestigen](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      De Blade **volume containers** wordt bijgewerkt om de wijzigingen weer te geven.
