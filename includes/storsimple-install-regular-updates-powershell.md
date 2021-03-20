---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176068"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Regel matige updates installeren via Windows PowerShell voor StorSimple
1. Open de seriële console van het apparaat en selecteer optie 1, **Meld u aan met volledige toegang**. Typ het wacht woord. Het standaardapparaatwachtwoord is *Wachtwoord1*. 
2. Typ in de opdrachtprompt:
   
     `Get-HcsUpdateAvailability`
   
    U wordt gewaarschuwd als er updates beschikbaar zijn en of de updates storend zijn of niet zijn verstoord.
3. Typ in de opdrachtprompt:
   
     `Start-HcsUpdate`
   
    Het update proces wordt gestart.

> [!IMPORTANT]
> * Deze opdracht is alleen van toepassing op regel matige updates. U voert deze opdracht uit op slechts één controller, maar beide controllers worden bijgewerkt. 
> * Mogelijk ziet u een failover van een controller tijdens het update proces. de failover heeft echter geen invloed op de beschik baarheid van het systeem of de bewerking.
> 
> 

