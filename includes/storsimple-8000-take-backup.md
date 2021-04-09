---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e923fdf560692c645c8a69e7e26d13f69d6920c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376021"
---
### <a name="to-take-a-backup"></a>Een back-up maken

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer uw apparaat in de lijst in tabelvorm met apparaten en klik hierop. Klik daarna op **Alle instellingen**. Ga op de blade **Instellingen** naar **Instellingen > Beheren > Back-upbeleid**.

    ![Back-upbeleid-toevoegen](./media/storsimple-8000-take-backup/step8takebu1.png)

2. Klik op de blade **Back-upbeleid** op **+ Beleid toevoegen**.

    ![Add-backup-beleid 2](./media/storsimple-8000-take-backup/step8takebu2.png)

3. Geef op de blade **Back-upbeleid maken** een naam op voor uw back-upbeleid die tussen de 3 en 150 tekens lang is.

4. Selecteer de volumes waarvan u een back-up wilt maken. Als u meer dan één volume selecteert, worden deze volumes samen gegroepeerd om zo een crashconsistente back-up te maken.

    ![Add-backup-policy 3](./media/storsimple-8000-take-backup/step8takebu4.png)

5. Op de blade **Eerste schema toevoegen**:

    1. Selecteer het type back-up. Selecteer **Lokale momentopname** voor sneller herstellen. Selecteer **Cloudmomentopname** voor gegevenstolerantie.
    2. Geef de back-upfrequentie op in minuten, uren, dagen of weken.
    3. Selecteer een bewaartijd. Hoe lang deze moet zijn, hangt af van hoe vaak een back-up wordt uitgevoerd. Als dagelijks een back-up wordt uitgevoerd, kunt u de bewaartijd instellen op weken. Als maandelijks een back-up wordt gemaakt, kunt u de bewaartijd instellen op maanden.
    4. Selecteer de begintijd en -datum voor het back-upbeleid.
    5. Klik op **OK** om het back-upbeleid te maken.

        ![Add-backup-policy 4](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Klik op **Maken** om te beginnen met het maken van het back-upbeleid. U krijgt een melding wanneer het back-upbeleid is gemaakt. De lijst met back-upbeleidsregels wordt ook bijgewerkt.
      
      ![Add-backup-policy 5](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      U hebt nu een back-upbeleid voor geplande back-ups van uw volumegegevens.




