---
title: Taken weer geven en beheren voor de StorSimple 8000-serie | Microsoft Docs
description: Beschrijft de Blade StorSimple Apparaatbeheer service taken en hoe u deze kunt gebruiken om recente, huidige en geplande back-uptaken bij te houden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949425"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>De StorSimple Apparaatbeheer-service gebruiken voor het weer geven en beheren van taken (update 3 en hoger)

## <a name="overview"></a>Overzicht
De Blade **taken** biedt één centrale portal voor het weer geven en beheren van taken die zijn gestart op apparaten die zijn verbonden met uw StorSimple-Apparaatbeheer service. U kunt geplande, actieve, voltooide, geannuleerde en mislukte taken weer geven voor meerdere apparaten. De resultaten worden weer gegeven in een tabel indeling.

![Blade taken](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

U kunt snel de taken vinden waarin u geïnteresseerd bent door te filteren op velden zoals:

* **Status** : taken kunnen worden uitgevoerd, geslaagd, geannuleerd, mislukt, geannuleerd of geslaagd met fouten.
* **Tijds bereik** : taken kunnen worden gefilterd op basis van het datum-en tijds bereik. De bereiken zijn afgelopen 1 uur, afgelopen 24 uur, afgelopen 7 dagen, afgelopen 30 dagen, afgelopen jaar of aangepaste datum.
* **Type** : het taak type kan een back-up worden gepland, hand matige back-up maken, back-up herstellen, het volume klonen, een failover uitvoeren voor volume containers, lokaal vastgemaakt volume maken, volume wijzigen, updates installeren, ondersteunings logboeken verzamelen en Cloud apparaat maken.
* **Apparaten** : taken worden gestart op een bepaald apparaat dat is verbonden met uw service.
  
De gefilterde taken worden vervolgens genoteerd op basis van de volgende kenmerken:
  
* **Naam** : geplande back-up, hand matige back-up, back-up herstellen, kloon volume, failover voor volume, lokaal vastgemaakt volume maken, volume wijzigen, updates installeren, ondersteunings logboeken verzamelen of Cloud apparaat maken.
* **Status** : wordt uitgevoerd, voltooid, geannuleerd, mislukt, geannuleerd of voltooid met fouten.
* **Entiteit** : de taken kunnen worden gekoppeld aan een volume, een back-upbeleid of een apparaat. Een kloon taak is bijvoorbeeld gekoppeld aan een volume, terwijl een geplande back-uptaak is gekoppeld aan een back-upbeleid. Er wordt een apparaat taak gemaakt als gevolg van een nood herstel (DR) of een herstel bewerking.
* **Apparaat** : de naam van het apparaat waarop de taak is gestart.
* **Gestart op** : de tijd waarop de taak is gestart.
* **Duur** – de tijd die nodig is om de taak te volt ooien.

De lijst met taken wordt elke 30 seconden vernieuwd.

Op deze pagina kunt u de volgende taak acties uitvoeren:

* Taakdetails weergeven
* Een taak annuleren

## <a name="view-job-details"></a>Taakdetails weergeven
Voer de volgende stappen uit om de details van een taak weer te geven.

#### <a name="to-view-job-details"></a>Taak details weer geven
1. Ga naar de StorSimple-Apparaatbeheer service en klik vervolgens op **Jobs**.

2. Geef op de Blade **taken** de gewenste taak (s) weer door een query uit te voeren met de juiste filters. U kunt zoeken naar voltooide, actieve of geannuleerde taken.

    ![Taak Blade 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecteer en klik op een taak.

    ![Taak Blade 3](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. Op de Blade taak Details kunt u de status, Details, tijd statistieken en gegevens statistieken weer geven.
   
    ![Taakdetails](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Een taak annuleren
Voer de volgende stappen uit om een actieve taak te annuleren.

> [!NOTE]
> Sommige taken, zoals het wijzigen van een volume om het volume type te wijzigen of een volume uit te breiden, kunnen niet worden geannuleerd.


### <a name="to-cancel-a-job"></a>Een taak annuleren
1. Geef op de pagina **taken** de actieve taken weer die u wilt annuleren door een query uit te voeren met de juiste filters. Selecteer de taak.

2. Klik met de rechter muisknop op de geselecteerde taak om het context menu aan te roepen en klik op **Annuleren**.

    ![Taak Details 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd. Deze taak is nu geannuleerd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het beheren van uw StorSimple-back-upbeleid](storsimple-8000-manage-backup-policies-u2.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

