---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176067"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Regel matige hotfixes installeren via Windows PowerShell voor StorSimple
1. Verbinding maken met de seriële console van het apparaat. Zie [stap 1: verbinding maken met de seriële console](../articles/storsimple/storsimple-update-device.md#step1)voor meer informatie.
2. Selecteer optie 1 in het menu seriële console en **Meld u aan met volledige toegang**. Typ het wacht woord. Het standaardapparaatwachtwoord is **Wachtwoord1**.
3. Typ in de opdrachtprompt:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Deze opdracht is alleen van toepassing op reguliere hotfixes. U voert deze opdracht uit op slechts één controller, maar beide controllers worden bijgewerkt.
    > Mogelijk ziet u een failover van een controller tijdens het update proces. de failover heeft echter geen invloed op de beschik baarheid van het systeem of de bewerking.

4. Geef het pad naar de gedeelde netwerkmap met de hotfixbestanden op wanneer u hierom wordt gevraagd.
5. U wordt gevraagd om een bevestiging. Typ **j** om door te gaan met de installatie van de hotfix.

