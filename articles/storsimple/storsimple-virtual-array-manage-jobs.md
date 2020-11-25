---
title: StorSimple virtuele-matrix taken weer geven en beheren | Microsoft Docs
description: Hierin wordt de pagina StorSimple Apparaatbeheer service Jobs beschreven en wordt uitgelegd hoe u deze kunt gebruiken om recente en huidige taken bij te houden voor de virtuele StorSimple-matrix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992989"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>De StorSimple Apparaatbeheer-service gebruiken om taken voor de virtuele StorSimple-matrix weer te geven
## <a name="overview"></a>Overzicht
De Blade **taken** biedt één centrale portal voor het weer geven en beheren van taken die zijn gestart op virtuele matrices die zijn verbonden met uw StorSimple-Apparaatbeheer service. U kunt actieve, voltooide en mislukte taken weer geven voor meerdere virtuele apparaten. De resultaten worden weer gegeven in een tabel indeling.

![Blade taken](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

U kunt snel de taken vinden waarin u geïnteresseerd bent door te filteren op velden zoals:

* **Tijds bereik** : taken kunnen worden gefilterd op basis van het datum-en tijds bereik.
* **Apparaten** : taken worden gestart op een specifiek apparaat dat is verbonden met uw service. De gefilterde taken worden vervolgens getabeld op basis van de volgende kenmerken:
  
  * **Naam** : de naam van de taak kan **alle**, **Backup**, **Clone**, **failover**, **updates downloaden** of **updates installeren**.
  * **Status** : taken kunnen **allemaal**, **in uitvoering**, **geslaagd** of **mislukt** zijn of zijn **geannuleerd**.
  * **Entiteit** : de taken kunnen worden gekoppeld aan een volume, share of apparaat.
  * **Apparaat** : de naam van het apparaat waarop de taak is gestart.
  * **Gestart op** : de tijd waarop de taak is gestart.
  * **Duur** : de duur waarvoor de taak is uitgevoerd.
* **Status** : u kunt zoeken naar alle, actieve, voltooide of mislukte taken.
* **Taak type** : het taak type kan alle, back-up, herstel, failover, updates downloaden of updates installeren.

De lijst met taken wordt elke 30 seconden vernieuwd.

## <a name="view-job-details"></a>Taakdetails weergeven
Voer de volgende stappen uit om de details van een taak weer te geven.

#### <a name="to-view-job-details"></a>Taak details weer geven
1. Geef op de Blade **taken** de taken weer die u wilt uitvoeren door een query uit te voeren met de juiste filters. U kunt zoeken naar voltooide of actieve taken.
2. Selecteer een taak in de lijst in tabel vorm van taken.
   
    ![Taak blad](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Klik onder aan de pagina op **Details**.
4. In het dialoog venster **Details** kunt u de status, Details en tijd statistieken weer geven. In de volgende afbeelding ziet u een voor beeld van het dialoog venster Details van de **back-uptaak** .
   
    ![Taakdetails](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Taak fouten wanneer de virtuele machine wordt onderbroken in de Hyper Visor
Wanneer een taak wordt uitgevoerd op uw virtuele StorSimple-matrix en het apparaat (virtuele machine dat is ingericht in Hyper Visor) langer dan 15 minuten wordt onderbroken, mislukt de taak. Dit komt doordat uw virtuele StorSimple-matrix tijd niet synchroon is met de Microsoft Azure tijd. 

U ziet de volgende fout: ' de tijd van uw apparaat is niet gesynchroniseerd met de Microsoft Azure tijd van meer dan 15 minuten. Zorg ervoor dat de Hyper Visor en de tijden van het apparaat worden gesynchroniseerd met een NTP-server. Controleer of er verbindings problemen zijn. Voer diagnostische tests uit via de lokale webgebruikersinterface van uw virtueel apparaat om verbindings problemen op te lossen.

Deze fouten zijn van toepassing op back-up-, herstel-, update-en failover-taken. Als uw virtuele machine in Hyper-V is ingericht, synchroniseert de computer uiteindelijk tijd met uw Hyper Visor. Als dat het geval is, kunt u de taak opnieuw starten.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het gebruik van de lokale web-UI voor het beheren van uw StorSimple-virtuele matrix](storsimple-ova-web-ui-admin.md).

