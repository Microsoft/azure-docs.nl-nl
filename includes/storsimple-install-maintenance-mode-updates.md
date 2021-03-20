---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176070"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Updates voor onderhouds modus installeren via Windows PowerShell voor StorSimple
1. Als u dit nog niet hebt gedaan, opent u de seriële console van het apparaat en selecteert u optie 1, **Meld u aan met volledige toegang**. 
2. Typ het wacht woord. Het standaardapparaatwachtwoord is **Wachtwoord1**.
3. Typ in de opdrachtprompt:
   
     `Get-HcsUpdateAvailability` 
4. U wordt gewaarschuwd als er updates beschikbaar zijn en of de updates storend zijn of niet zijn verstoord. Als u storende updates wilt Toep assen, moet u het apparaat in de onderhouds modus plaatsen. Zie [stap 2: de onderhouds modus](../articles/storsimple/storsimple-update-device.md#step2) voor instructies opgeven.
5. Wanneer het apparaat zich in de onderhouds modus bevindt, typt u bij de opdracht prompt: `Start-HcsUpdate`
6. U wordt gevraagd om een bevestiging. Nadat u de updates hebt bevestigd, worden deze geïnstalleerd op de controller waartoe u momenteel toegang hebt. Nadat de updates zijn geïnstalleerd, wordt de controller opnieuw opgestart. 
7. Bewaak de status van updates. Type:
   
    `Get-HcsUpdateStatus`
   
    Als dat zo `RunInProgress` is `True` , wordt de update nog steeds uitgevoerd. Als `RunInProgress` dat `False` het geval is, geeft dit aan dat de update is voltooid.  
8. Wanneer de update op de huidige controller is geïnstalleerd en deze opnieuw is opgestart, maakt u verbinding met de andere controller en voert u stap 1 tot en met 6 uit.
9. Nadat beide controllers zijn bijgewerkt, sluit u de onderhouds modus. Zie [stap 4: onderhouds modus afsluiten](../articles/storsimple/storsimple-update-device.md#step4) voor instructies.

