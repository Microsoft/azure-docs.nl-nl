---
title: Modus StorSimple apparaat wijzigen | Microsoft Docs
description: Hierin worden de StorSimple beschreven en wordt uitgelegd hoe u Windows PowerShell voor StorSimple kunt gebruiken om de modus apparaat te wijzigen.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017080"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>De modus apparaat op uw StorSimple-apparaat wijzigen

In dit artikel vindt u een korte beschrijving van de verschillende modi waarin uw StorSimple-apparaat kan worden gebruikt. Uw StorSimple-apparaat kan in drie modi worden gebruikt: normaal, onderhoud en herstel.

Nadat u dit artikel hebt gelezen, weet u het volgende:

* Wat de StorSimple zijn
* Vaststellen in welke modus het StorSimple-apparaat zich bevindt
* De onderhouds modus van Normal naar maintenance *wijzigen en omgekeerd*

De bovenstaande beheer taken kunnen alleen worden uitgevoerd via de Windows Power shell-interface van uw StorSimple-apparaat.

## <a name="about-storsimple-device-modes"></a>Over StorSimple

Uw StorSimple-apparaat kan worden uitgevoerd in de modus normaal, onderhoud of herstel. Elk van deze modi wordt hieronder beschreven.

### <a name="normal-mode"></a>Normale modus

Dit is gedefinieerd als de normale operationele modus voor een volledig geconfigureerd StorSimple-apparaat. Uw apparaat moet standaard de modus Normaal hebben.

### <a name="maintenance-mode"></a>Onderhoudsmodus

Soms moet het StorSimple-apparaat in de onderhouds modus worden geplaatst. Met deze modus kunt u onderhoud uitvoeren op het apparaat en verstorende updates installeren, zoals de bestanden die betrekking hebben op de schijf firmware.

U kunt het systeem alleen via de Windows PowerShell voor StorSimple in de onderhouds modus plaatsen. Alle I/O-aanvragen worden in deze modus onderbroken. Services als niet-vluchtig RAM-geheugen (Random Access Memory) of de Clustering-service worden ook gestopt. Beide controllers worden opnieuw gestart wanneer u deze modus invoert of afsluit. Wanneer u de onderhouds modus afsluit, worden alle services hervat en moeten ze in orde zijn. Dit kan enkele minuten duren.

> [!NOTE]
> **De onderhouds modus wordt alleen ondersteund op een goed functionerende apparaat. Het wordt niet ondersteund op een apparaat waarop een of beide controllers niet werken.**


### <a name="recovery-mode"></a>Herstel modus

De herstel modus kan worden beschreven als ' Windows veilige modus met netwerk ondersteuning '. In de herstel modus wordt het Microsoft Ondersteuning team uitgevoerd en kunnen ze diagnostische gegevens uitvoeren op het systeem. Het belangrijkste doel van de herstel modus is het ophalen van de systeem Logboeken.

Als uw systeem in de herstel modus wordt gezet, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. Ga naar [Contact Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md)voor meer informatie.

> [!NOTE]
> **U kunt het apparaat niet in de herstel modus plaatsen. Als het apparaat een slechte status heeft, probeert de herstel modus het apparaat op te halen in een staat waarin Microsoft Ondersteuning personeel het kan onderzoeken.**

## <a name="determine-storsimple-device-mode"></a>De StorSimple-apparaatmodus bepalen

#### <a name="to-determine-the-current-device-mode"></a>De huidige apparaat modus bepalen

1. Meld u aan bij de seriële console van het apparaat door de stappen in [putty gebruiken te volgen om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Bekijk het banner bericht in het menu van de seriële console van het apparaat. Dit bericht geeft expliciet aan of het apparaat zich in de onderhouds-of herstel modus bevindt. Als het bericht geen specifieke informatie met betrekking tot de systeem modus bevat, is het apparaat in de normale modus.

## <a name="change-the-storsimple-device-mode"></a>De StorSimple wijzigen

U kunt het StorSimple-apparaat in de onderhouds modus plaatsen (van de normale modus) om onderhoud uit te voeren of updates voor de onderhouds modus te installeren. Voer de volgende procedures uit om de onderhouds modus in of uit te sluiten.

> [!IMPORTANT]
> Voordat u de onderhouds modus inschakelt, moet u controleren of beide controllers in orde zijn door de apparaatinstellingen te openen **> de hardware-status** voor uw apparaat in de Azure Portal. Als een of beide controllers niet in orde zijn, neemt u contact op met Microsoft Ondersteuning om de volgende stappen uit te voeren. Ga naar [Contact Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md)voor meer informatie.
 

#### <a name="to-enter-maintenance-mode"></a>Onderhouds modus activeren

1. Meld u aan bij de seriële console van het apparaat door de stappen in [putty gebruiken te volgen om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**. Geef het **beheerders wachtwoord** voor het apparaat op wanneer u hierom wordt gevraagd. Het standaard wachtwoord is: `Password1` .
3. Typ bij de opdracht prompt 
   
    `Enter-HcsMaintenanceMode`
4. Er wordt een waarschuwings bericht weer gegeven met de melding dat de onderhouds modus alle I/O-aanvragen verstoort en de verbinding met de Azure Portal verkrijgt en u wordt gevraagd om bevestiging. Typ **Y** om de onderhouds modus in te voeren.
5. Beide controllers worden opnieuw opgestart. Wanneer het opnieuw opstarten is voltooid, geeft de seriële console banner aan dat het apparaat zich in de onderhouds modus bevindt. Hieronder ziet u een voorbeeld van de uitvoer.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Onderhouds modus afsluiten

1. Meld u aan bij de seriële console van het apparaat. Controleer in het banner bericht dat het apparaat zich in de onderhouds modus bevindt.
2. Typ in de opdrachtprompt:
   
    `Exit-HcsMaintenanceMode`
3. Er verschijnt een waarschuwings bericht en er wordt een bevestigings bericht weer gegeven. Typ **Y** om de onderhouds modus af te sluiten.
4. Beide controllers worden opnieuw opgestart. Wanneer het opnieuw opstarten is voltooid, geeft de seriële console banner aan dat het apparaat in de normale modus is. Hieronder ziet u een voorbeeld van de uitvoer.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [Toep assen van updates op de normale en onderhouds modus](storsimple-update-device.md) op uw StorSimple-apparaat.

