---
title: StorSimple Snapshot Manager back-uptaken | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Snapshot Manager MMC-module gebruikt om geplande, momenteel actieve en voltooide back-uptaken weer te geven en te beheren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998189"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple Snapshot Manager gebruiken om back-uptaken weer te geven en te beheren

## <a name="overview"></a>Overzicht
Het knoop punt **taken** in het deel venster **bereik** toont de **geplande**, **laatste 24 uur** en **uitvoeren** van back-uptaken die u interactief of met een geconfigureerd beleid hebt gestart. 

In deze zelf studie wordt uitgelegd hoe u het knoop punt **taken** kunt gebruiken om informatie weer te geven over geplande, recente en momenteel actieve back-uptaken. (De lijst met taken en de bijbehorende informatie wordt weer gegeven in het **resultaten** venster.) Daarnaast kunt u met de rechter muisknop op een vermelde taak klikken en een context menu weer geven met een lijst met beschik bare acties.

## <a name="view-scheduled-jobs"></a>Geplande taken weer geven
Gebruik de volgende procedure om geplande back-uptaken weer te geven.

#### <a name="to-view-scheduled-jobs"></a>Geplande taken weer geven
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten. 
2. Vouw in het deel venster **bereik** het knoop punt **taken** uit en klik op **gepland**. De volgende informatie wordt weer gegeven in het **resultaten** venster:
   
   * **Naam** : de naam van de geplande moment opname
   * **Volgende uitvoering** : de datum en tijd van de volgende geplande moment opname
   * **Laatste uitvoering** : de datum en tijd van de meest recente geplande moment opname
     
     > [!NOTE]
     > Voor eenmalige moment opnamen worden de volgende keer **uitvoeren** en de **laatste uitvoering** gelijk.
     
     ![Geplande back-uptaken](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Als u aanvullende acties wilt uitvoeren op een specifieke taak, klikt u met de rechter muisknop op de naam van de taak in het **resultaten** venster en selecteert u in de menu opties.

## <a name="view-recent-jobs"></a>Recente taken weer geven
Gebruik de volgende procedure om taken voor back-up en herstel weer te geven die in de afgelopen 24 uur zijn voltooid.

#### <a name="to-view-recent-jobs"></a>Recente taken weer geven
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **taken** uit en klik op **afgelopen 24 uur**. In het deel venster met **resultaten** worden back-uptaken voor de afgelopen 24 uur weer gegeven (tot een maximum van 64 taken). De volgende informatie wordt weer gegeven in het deel venster met **resultaten** , afhankelijk van de **weer gave** -opties die u opgeeft:
   
   * **Naam** : de naam van de geplande moment opname.
   * **Gestart** : de datum en tijd waarop de moment opname begon.
   * **Gestopt** : de datum en tijd waarop de moment opname is voltooid of is beëindigd.
   * **Verstreken** : de hoeveelheid tijd tussen de **begin** -en **Stop** tijden.
   * **Status** : de status van de laatst voltooide taak. **Geslaagd** geeft aan dat de back-up is gemaakt. **Mislukt** geeft aan dat de taak niet is uitgevoerd.
   * **Gegevens** : de reden voor de fout.
   * **Verwerkte bytes (MB)** : de hoeveelheid gegevens uit de verwerkte volume groep (in MB). 
     
     ![Taken die in de afgelopen 24 uur zijn uitgevoerd](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Als u aanvullende acties wilt uitvoeren op een specifieke taak, klikt u met de rechter muisknop op de naam van de taak in het **resultaten** venster en selecteert u in de menu opties.
   
    ![Een taak verwijderen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Actieve taken weer geven
Gebruik de volgende procedure om taken weer te geven die momenteel worden uitgevoerd.

#### <a name="to-view-currently-running-jobs"></a>Actieve taken weer geven
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **taken** uit en klik op **uitvoeren**. Afhankelijk van de **weergave** opties die u opgeeft, wordt de volgende informatie weer gegeven in het **resultaten** venster:
   
   * **Naam** : de naam van de geplande moment opname.
   * **Gestart** : de datum en tijd waarop de moment opname begon.
   * **Controle punt** : de huidige actie van de back-up.
   * **Status** : het voltooiings percentage.
   * **Verstreken** : de hoeveelheid tijd die is verstreken sinds de back-up is gestart. 
   * **Gemiddelde door Voer (MB)** : de verhouding van het totale aantal gegevens bytes dat wordt verwerkt voor de totale tijd die wordt gebruikt voor verwerking (MBS).
   * **Verwerkte bytes (MB)** : het totale aantal verwerkte gegevens bytes (in MB).
   * **Geschreven bytes (MB)** : het totale aantal bytes geschreven gegevens (in MB). Het bevat zowel de gegevens als de meta gegevens en is doorgaans groter dan de verwerkte bytes.
     
     ![Taken die momenteel worden uitgevoerd](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Als u aanvullende acties wilt uitvoeren op een specifieke taak, klikt u met de rechter muisknop op de naam van de taak in het **resultaten** venster en selecteert u in de menu opties.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van de back-upcatalogus](storsimple-snapshot-manager-manage-backup-catalog.md).

