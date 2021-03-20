---
title: StorSimple Snapshot Manager back-upbeleid | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Snapshot Manager MMC-module gebruikt om het back-upbeleid voor het beheren van geplande back-ups te maken en te beheren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054939"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>StorSimple-Snapshot Manager gebruiken voor het maken en beheren van back-upbeleid
## <a name="overview"></a>Overzicht
Met een back-upbeleid maakt u een planning voor back-ups van volume gegevens lokaal of in de Cloud. Wanneer u een back-upbeleid maakt, kunt u ook een Bewaar beleid opgeven. (U kunt Maxi maal 64 moment opnamen bewaren.) Zie [back-uptypen](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) in [de StorSimple 8000-serie: een hybride Cloud oplossing](storsimple-overview.md)voor meer informatie over back-upbeleid.

In deze zelfstudie wordt het volgende uitgelegd:

* Maak een back-upbeleid
* Een back-upbeleid bewerken
* Een back-upbeleid verwijderen

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid
Gebruik de volgende procedure om een nieuw back-upbeleid te maken.

#### <a name="to-create-a-backup-policy"></a>Een back-upbeleid maken
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op **back-upbeleid** en klik op **back-upbeleid maken**.

    ![Maak een back-upbeleid](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Het dialoog venster **een beleid maken** wordt weer gegeven.

    ![Een tabblad beleid maken-algemeen](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Voer op het tabblad **Algemeen** de volgende gegevens in:

   1. Typ in het tekstvak **naam** een naam voor het beleid.
   2. Typ in het tekstvak **volume groep** de naam van de volume groep die is gekoppeld aan het beleid.
   3. Selecteer een **lokale moment opname** of een **Cloud momentopname**.
   4. Selecteer het aantal moment opnamen dat u wilt behouden. Als u **alle** selecteert, worden de 64-moment opnamen bewaard (het maximum).
4. Klik op de **schema** tabblad.

    ![Een tabblad beleid maken](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Op het tabblad **planning** voert u de volgende gegevens in:

   1. Klik op het selectie vakje **inschakelen** om de volgende back-up te plannen.
   2. Onder **instellingen** selecteert u **eenmalig**, **dagelijks**, **wekelijks** of **maandelijks**.
   3. Klik in het tekstvak **Start** op het kalender pictogram en selecteer een begin datum.
   4. Onder **Geavanceerde instellingen** kunt u optionele herhalings schema's en een eind datum instellen.
   5. Klik op **OK**.

Nadat u een back-upbeleid hebt gemaakt, wordt de volgende informatie weer gegeven in het **resultaten** venster:

* **Naam** : de naam van het back-upbeleid.
* **Type** : lokale moment opname of Cloud momentopname.
* **Volume groep** : de volume groep die aan het beleid is gekoppeld.
* **Bewaren** : het aantal gearchiveerde moment opnamen; de maximum waarde is 64.
* **Gemaakt** : de datum waarop dit beleid is gemaakt.
* **Ingeschakeld** : of het beleid momenteel van kracht is: **True** geeft aan dat het actief is. **Onwaar** geeft aan dat deze niet van kracht is.

## <a name="edit-a-backup-policy"></a>Een back-upbeleid bewerken
Gebruik de volgende procedure om een bestaand back-upbeleid te bewerken.

#### <a name="to-edit-a-backup-policy"></a>Een back-upbeleid bewerken
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** op het knoop punt **back-upbeleid** . Alle back-upbeleid wordt weer gegeven in het **resultaten** venster.
3. Klik met de rechter muisknop op het beleid dat u wilt bewerken en klik vervolgens op **bewerken**.

    ![Een back-upbeleid bewerken](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Wanneer het venster **een beleid maken** wordt weer gegeven, voert u uw wijzigingen in en klikt u vervolgens op **OK**.

## <a name="delete-a-backup-policy"></a>Een back-upbeleid verwijderen
Gebruik de volgende procedure om een back-upbeleid te verwijderen.

#### <a name="to-delete-a-backup-policy"></a>Een back-upbeleid verwijderen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** op het knoop punt **back-upbeleid** . Alle back-upbeleid wordt weer gegeven in het **resultaten** venster.
3. Klik met de rechter muisknop op het back-upbeleid dat u wilt verwijderen en klik vervolgens op **verwijderen**.
4. Wanneer het bevestigings bericht wordt weer gegeven, klikt u op **Ja**.

    ![Bevestiging van back-upbeleid verwijderen](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het weer geven en beheren van back-uptaken](storsimple-snapshot-manager-manage-backup-jobs.md).
