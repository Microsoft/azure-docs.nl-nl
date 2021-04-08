---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552672"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Een StorSimple-back-upbeleid toevoegen

1. Ga naar uw StorSimple-apparaat en klik op **Back-upbeleid**.

2. Klik op de blade **Back-upbeleid** op **+ Beleid toevoegen** vanuit de opdrachtbalk.
   
    ![Een back-upbeleid toevoegen](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. Voer de volgende stappen uit op de blade **Back-upbeleid maken**:
   
   1. **Apparaat selecteren** wordt automatisch ingevuld op basis van het apparaat dat u hebt geselecteerd.
   
   2. Geef een naam voor het back- **upbeleid** op dat uit 3 tot 150 tekens bestaat. Als het beleid is gemaakt, kunt u het beleid niet hernoemen.
       
   3. Als u volumes wilt toewijzen aan dit back-upbeleid, selecteert u **Volumes toevoegen** en klikt u vervolgens vanuit de lijst in tabelvorm met volumesop het selectievakje uit om een of meer volumes toe te wijzen aan het back-upbeleid.

       ![Een back-upbeleid toevoegen 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Als u een planning voor het back-upbeleid wilt definiëren, klikt u op **Eerste planning** en wijzigt u vervolgens de volgende parameters:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Een back-upbeleid toevoegen 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Selecteer voor **Type momentopname** de optie **Cloud** of **Lokaal**.

       2. Geef de frequentie van back-ups (een getal op en kies vervolgens **dagen** of **weken** in de vervolg keuzelijst).

       3. Voer een bewaarschema in.

       4. Voer een begintijd en -datum in voor het back-upbeleid.

       5. Klik op **OK** om de planning te definiëren.

   5. Klik op **Maken** om een back-upbeleid te maken.
   
   6. U krijgt een melding wanneer het back-upbeleid is gemaakt. Het nieuwe beleid wordt in tabelvorm weergegeven op de blade **Back-upbeleid**.

       ![Een back-upbeleid toevoegen 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
