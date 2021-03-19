---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176231"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Updates voor onderhouds modus installeren via Windows PowerShell voor StorSimple

Wanneer u updates voor de onderhouds modus toepast op een StorSimple-apparaat, worden alle I/O-aanvragen onderbroken. Services, zoals een niet-vluchtig geheugen (Random Access Memory) of de Clustering-Service, worden gestopt. Beide controllers worden opnieuw opgestart wanneer u deze modus insluit of afsluit. Wanneer u deze modus verlaat, worden alle services hervat en zijn ze in orde. (Dit kan enkele minuten duren.)

> [!IMPORTANT]
> * Voordat u de onderhouds modus inschakelt, moet u controleren of beide controllers in orde zijn in de Azure Portal. Als de controller niet in orde is, [neemt u contact op met Microsoft ondersteuning](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) om de volgende stappen uit te voeren.
> * Wanneer u zich in de onderhouds modus bevindt, moet u eerst één controller en vervolgens de andere controller bijwerken.

1. Gebruik PuTTy om verbinding te maken met de seriële console. Volg de gedetailleerde instructies in [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Druk op de opdrachtregel op **Enter**. Selecteer optie 1, **Meld u aan met volledige toegang**.

2. Als u de controller in de onderhouds modus wilt plaatsen, typt u:
    
    `Enter-HcsMaintenanceMode`

    Beide controllers worden opnieuw opgestart in de onderhouds modus.

3. Installeer de updates voor de onderhouds modus. Type:

    `Start-HcsUpdate`

    U wordt gevraagd om bevestiging. Nadat u de updates hebt bevestigd, worden deze geïnstalleerd op de controller waartoe u momenteel toegang hebt. Nadat de updates zijn geïnstalleerd, wordt de controller opnieuw opgestart.

4. Bewaak de status van updates. Meld u aan bij de peer controller wanneer de huidige controller wordt bijgewerkt en kan geen andere opdrachten verwerken. Type:

    `Get-HcsUpdateStatus`

    Als dat zo `RunInProgress` is `True` , wordt de update nog steeds uitgevoerd. Als `RunInProgress` dat `False` het geval is, geeft dit aan dat de update is voltooid.

5. Nadat de updates van de schijf firmware zijn toegepast en de bijgewerkte controller opnieuw is opgestart, controleert u de versie van de schijf firmware. Op de bijgewerkte controller typt u:

    `Get-HcsFirmwareVersion`
   
    De verwachte schijf firmware versies zijn:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. De onderhouds modus afsluiten. Typ de volgende opdracht voor elke apparaat-controller:

    `Exit-HcsMaintenanceMode`

    De controllers starten opnieuw op wanneer u de onderhoudsmodus afsluit.

7. Ga terug naar Azure Portal. De portal geeft mogelijk niet aan dat de updates voor de onderhouds modus 24 uur zijn geïnstalleerd.